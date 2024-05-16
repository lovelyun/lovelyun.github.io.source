---
title: node自动布署
date: 2019-04-18 11:41:38
categories: node
---

前端静态资源布署，可以通过xShell或者SecureCRT这种软件来上传资源，用软件的ftp上传，或者手动输入一些命令来操作，比如：

```
#上传
rz
#解压
unzip
```

如果用node自动布署，就简便很多。

使用node脚本，可将`npm run build`之后构建出来的dist文件夹压缩，然后自动上传到远端服务器，然后在远端服务器自动解压到固定的目录。

## cli
在package.json中增加bin命令`cli-deploy`,指向`./bin/deploy.js`,在script中增加`deploy`命令。后面完成`deploy.js`后就可以直接`npm run deploy`来发布了。
不了解node cli的话，可以看这篇文章[A guide to create a NodeJS command-line package – Netscape – Medium](https://medium.com/netscape/a-guide-to-create-a-nodejs-command-line-package-c2166ad0452e)

```
// package.json
"scripts": {
  "dev": "vue-cli-service serve",
  "build": "vue-cli-service build",
  "lint": "vue-cli-service lint",
  "deploy": "vue-cli-service build && cli-deploy"
},
"bin": {
  "cli-deploy": "./bin/deploy.js"
},
```

也可以不使用bin，将scripts中的deploy命令改成`vue-cli-service build && node ./bin/deploy.js`

## 压缩

```javascript
// zip.js
const glob = require('glob')
const fs = require('fs')
const path = require('path')
const yazl = require("yazl")
const chalk = require('chalk')

const zip = ({ sourcePath, destPath, filename }) => {
  return new Promise((resolve) => {

  const zipfile = new yazl.ZipFile()

  if (!fs.existsSync(destPath)) {
    fs.mkdirSync(destPath, 0777)
  }

  const files = glob.sync('**/*.*', {
    cwd: sourcePath
  })

  files.forEach(function(file) {
    const filePath = path.join(sourcePath, file)
    zipfile.addFile(filePath, file)
  })

  zipfile.outputStream.pipe(fs.createWriteStream(path.resolve(destPath, filename))).on('close', () => {
    console.log(chalk.cyan(
      `  ${filename} has build!`
    ))
    resolve()
  })
  zipfile.end()
  })
}

module.exports = {
  zip
}
```

```javascript
// bin/deploy.js
#!/usr/bin/env node
const { zip } = require('./zip')
const zipOptions = {
  sourcePath: path.join(__dirname, '../dist'), // 需要要打包的目录
  destPath: path.join(__dirname, '../'), // 打包存放的目录
  filename: 'dist.zip', // 打包后的名称
}
zip(zipOptions)
```

## 上传
上传这块做了比较多的调研，看了比较多的库，最后决定用scp2，不需要配置ssh免密登录，直接使用的时候指定信息就行。

```javascript
// bin/deploy.js
const path = require('path')
const client = require('scp2')
client.scp(path.join(__dirname, '../dist.zip'), `${user}:${pass}@${host}:${reomteFloder}`, () => {
  // 这里远端解压
})
```

也可以不压缩，直接上传文件夹


```javascript
// bin/deploy.js
const path = require('path')
const client = require('scp2')
client.scp(path.join(__dirname, '../dist/'), `${user}:${pass}@${host}:${reomteFloder}/`, () => {
  // 上传成功
})
```

## 解压
选用simple-ssh连接远端，然后用ssh.exec在远端执行命令。simple-ssh只是把ssh2进一步封装了一下，让调用更简单。

```javascript
// bin/deploy.js
const SSH = require('simple-ssh')
const host = 'xx.xx.xx.xx' // 远端主机ip
const user = 'xxx' // 用户名
const pass = 'xxx' // 密码
const reomteFloder = '/xxx/xxx/xx' // 远端解压目录
const ssh = new SSH({host, user, pass})
ssh.exec(`rm -rf ${reomteFloder}/dist`) // 解压前先清空dist目录，避免有哈希的文件积累
  .exec(`unzip -o ${reomteFloder}/dist.zip -d ${reomteFloder}/dist`, {
    out: console.log.bind(console),
  })
  .start()
```

<div calss="tip">
unzip参数说明：
- -o 不必先询问用户，unzip执行后覆盖原有文件
- -d <目录> 指定文件解压缩后所要存储的目录
</div>

## pm2
有的项目需要用pm2重启，如果直接远程执行pm2命令，比如`pm2 status`, 会报错`pm2: command not found`。
解决办法：把`pm2 status`改成`'bash -l -c "pm2 status"'`。
参考了[bash-doesnt-load-node-on-remote-ssh-command](https://stackoverflow.com/questions/33357227/bash-doesnt-load-node-on-remote-ssh-command)和[env-problem-when-ssh-executing-command-on-remote](http://feihu.me/blog/2014/env-problem-when-ssh-executing-command-on-remote/)

## 总结
至此，完成了自动压缩、上传、解压。
`cli-deploy`可以部署；
`npm run deploy`可以构建+部署。

完整版本`deploy.js`如下：

```javascript
#!/usr/bin/env node
const { zip } = require('./zip')
const path = require('path')
const SSH = require('simple-ssh')
const client = require('scp2')

const zipOptions = {
  sourcePath: path.join(__dirname, '../dist'), // 需要要打包的目录
  destPath: path.join(__dirname, '../'), // 打包存放的目录
  filename: 'dist.zip', // 打包后的名称
}
const host = 'xx.xx.xx.xx' // 远端主机ip
const user = 'xxx' // 用户名
const pass = 'xxx' // 密码
const reomteFloder = '/xxx/xxx/xx' // 远端解压目录

const ssh = new SSH({host, user, pass})

const upload = () => {
  client.scp(path.join(__dirname, '../dist.zip'), `${user}:${pass}@${host}:${reomteFloder}`, () => {
    ssh.exec(`unzip -o ${reomteFloder}/dist.zip -d ${reomteFloder}/dist`, {
        out: console.log.bind(console),
    })
    .start()
  })
}

async function deploy () {
  await zip(zipOptions)
  console.log('Build complete')
  upload()
}

deploy()
```
