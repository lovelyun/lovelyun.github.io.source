---
title: node自动布署
date: 2019-04-18 11:45:38
tags: node
categories: node
---

## 需求背景
最近有了一个测试服务器用来部署前端的静态资源，有了用户名和密码，可以通过xShell或者SecureCRT这种软件来上传资源。
按照以前的经验，一般就用软件的ftp上传，或者手动输入一些命令来操作，比如：

```shell
#上传
rz
#解压
unzip
```

之前很长的一段时间都是这么布署，前同事启达是命令行高手，各种命令敲起来溜的飞起，于是我也很喜欢命令行，后来有同事搞了个线上布署系统（应该是光富大神和接神），打开一个页面，然后就是点按钮，填信息，一切过程可视化。再后来又有同事搞了个cli来处理布署（应该是宇行）。
新公司这些都是空白，而我又比较懒，于是决定也搞一个cli来布署，想起了很久以前，前同事宇行分享过node cli，一直觉得很不错，但是没有应用场景，现在终于有机会了。

现在明确需求：我需要把`npm run build`之后构建出来的dist文件夹压缩，然后自动上传到远端服务器，然后在远端服务器自动解压到固定的目录。

## 解决方案

### cli
在package.json中增加bin命令`cli-deploy`,指向`./bin/deploy.js`,在script中增加`deploy`命令。后面完成deploy.js后就可以直接`npm run deploy`来发布了。
不了解node cli的话，可以看这篇文章[A guide to create a NodeJS command-line package – Netscape – Medium](https://medium.com/netscape/a-guide-to-create-a-nodejs-command-line-package-c2166ad0452e)

```json
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

### 压缩
压缩这块没有调研，拿来主义用了前同事（应该是丁一吧，或者是兴军）的方案，稍微润色后便使用了,主要是去掉了源码中删除sourcemap的功能，让zip返回一个Promise，方便处理后续逻辑。

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

### 上传
上传这块做了比较多的调研，看了比较多的库，最后决定用[scp2](https://github.com/spmjs/node-scp2)，不需要配置ssh免密登录，直接使用的时候指定信息就行。

```javascript
// bin/deploy.js
const path = require('path')
const client = require('scp2')
client.scp(path.join(__dirname, '../dist.zip'), `${user}:${pass}@${host}:${reomteFloder}`, () => {
  // 这里远端解压
})
```

### 解压
选用[simple-ssh](https://github.com/MCluck90/simple-ssh)连接远端，然后用ssh.exec在远端执行命令：

```javascript
// bin/deploy.js
const SSH = require('simple-ssh')
const host = 'xx.xx.xx.xx' // 远端主机ip
const user = 'xxx' // 用户名
const pass = 'xxx' // 密码
const reomteFloder = '/xxx/xxx/xx' // 远端解压目录
const ssh = new SSH({host, user, pass})
ssh.exec(`unzip -o ${reomteFloder}/dist.zip -d ${reomteFloder}/dist`, {
  out: console.log.bind(console),
})
.start()
```

<div class="tip">
unzip参数说明：
<li>-o 不必先询问用户，unzip执行后覆盖原有文件</li>
<li>-d <目录> 指定文件解压缩后所要存储的目录</li>
</div>

### 总结
至此，完成了自动压缩、上传、解压。完整版本deploy.js如下：

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
