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

### 构建并发布
```
hexo g -d // hexo generate --deploy的简写
```


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
