---
title: hexo博客
date: 2018-11-26 10:27:32
tags: hexo
categories: docs
---

## 基本操作

### 写博客

```
hexo new 博客标题
```

### 本地服务
```
hexo serve
```

![hexo serve](/img/serve.png)

### ~~构建并发布~~
```
hexo g -d // hexo generate --deploy的简写
```

### 版本控制与持续集成
使用了上面的构建并发布流程后，你一定会觉得这个过程很麻烦，而且源文件不方便管理，虽然可以开一个分支放源文件，但还是很麻烦对不对？下面来看一个更好的处理办法，新的push出现时自动构建并发布：
1、Content Repo：Hexo生成出来的静态网站放到GitHub Pages时用到的repo;
2、Source Repo：hexo源文件的repo；
3、使用CI工具（比如[AppVeyor官网](https://www.appveyor.com/)）来完成下面的功能：
当有新的change push到Source Repo时，自动执行CI脚本，生成最新的静态网站发布到Content Repo。

<div class="tip">
使用AppVeyor来建立CI的方法参考本文第二个参考链接。
</div>

## 个性化

### 文章链接
更改`_config.yml`中的下列配置：

```
permalink: :category/:title/
```

### 图片资源处理
放在 source/images 文件夹中。然后通过 `![](/images/image.jpg)` 的方法访问它们。

```
![hexo serve](/img/serve.png) // 上面hexo serve的图片写法
```

## 参考
* [文档 | Hexo](https://hexo.io/zh-cn/docs/)
* [Hexo的版本控制与持续集成 | formulahendry](https://formulahendry.github.io/2016/12/04/hexo-ci/)
