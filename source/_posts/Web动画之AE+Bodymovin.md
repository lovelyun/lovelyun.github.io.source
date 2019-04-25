---
title: Web动画之AE+Bodymovin
date: 2019-03-28 10:46:58
tags: 动画
categories: animation
---

## AE+Bodymovin简介
设计通过AE设计动效，安装Bodymovin插件后，直接导出js或者json数据给前端。前端利用设计提供的动画数据即可实现动效。

### 设计
设计首先需要[下载Bodymovin插件](http://aescripts.com/bodymovin/)。
具体的设计细节这里就不描述了，交给设计同事就好。
需要注意的是：
1、文件
如果使用了图片资源或者未转成形状图层的Adobe Illustrator文件图层, 将会同时生成一个images文件夹存放这些图片资源。(建议将AI图层转换为形状图层，这样他们会被导出为矢量数据，只需在AE中导入的AI图层上右键 > 从矢量图层创建形状)
<div class="tip">
注意，如果不同的带图片资源的动画导出到同一地址，images文件夹将会被覆盖。
</div>
2、性能
Bodymovin的动画都是实时渲染的，最好控制下AE工程文件体积。避免这种情况：绘制了一个巨大的形状图层，但是只通过遮罩使用其中一小部分。
过多的节点同样会影响性能。

Bodymovin支持的AE特性：

* 支持预合成、形状图层、固态层、图片、空对象以及文字图层。
* 支持遮罩和反向遮罩。也许别的模式也会支持，但是会对性能造成巨大影响。
* 支持时间重映射。
* 支持形状图层的形状、矩形、椭圆和星形。
* 支持滑块效果。
* 支持部分表达式。更多介绍可以查看[这里](https://github.com/airbnb/lottie-web/wiki/Expressions)。
* 不支持： 图像序列、视频和音频。
* 不要伸缩图层！不知为何，伸缩图层会破坏导出的数据，所以不要做这个操作。

### 前端
1、安装

```bash
npm install lottie-web
```

或者直接从[官方CDN](https://cdnjs.com/libraries/bodymovin)获取。

完整版支持渲染为svg\canvas\html三种格式，由于我们只需要svg格式（html格式性能不好，canvas格式性能够好，但是动画数据是js格式，有安全隐患，折中选择json格式数据的svg），所以只需要lottie_svg.min.js，经过gzip压缩的库文件只有48.3k。

2、demo
请看[demo](https://codepen.io/collection/nVYWZR/)。
请看[官方文档](https://github.com/airbnb/lottie-web?tdsourcetag=s_pctim_aiomsg)。

## 应用
设计输出资源的目录结构是这样的：
.
├── images
│   ├── img_0.png
│   ├── img_1.png
│   ├── img_2.png
│   ├── ...
├── demo.html
├── lottie.js
└── data.json

有3个问题导致不能直接布署：
1、资源太大；
2、目录结构不方便上传CDN；
3、动画尺寸未设置，默认是100%。

### 资源大小问题
由于不完全是矢量图的设计，所以有images图片资源，直接点击demo.html就可以看到效果。但是设计直接导出的资源比较大，主要有以下几点原因：

1、lottie.js未压缩，直接内嵌在html里面，有243k,如果选择官网CDN的[lottie_svg.min.js](https://cdnjs.cloudflare.com/ajax/libs/bodymovin/5.5.1/lottie_svg.min.js)就只有48k，不过公司的CDN只能压缩到62k。
2、data.json也直接内嵌到html里面，但把json文件放到CDN上用gzip压缩后，只有2k。

把demo.html里的lottie.js和data.json代码删除，用CDN加载lottie_svg.min.js和data.json。通过webpack压缩后的demo.html文件只有577字节。

### 目录结构问题
为了方便发布到CDN，改变了目录结构
.
├── img_0.png
├── img_1.png
├── ...
├── img_8.png
├── demo.html
└── data.json

需要把data.json里的图片资源路径改一下，assets里面的u代表图片资源路径。

```json
// 原来是这样的
"assets": [{
  "id": "image_0",
  "w": 66,
  "h": 55,
  "u": "images/",
  "p": "img_0.png",
  "e": 0
}]
// 改成下面这样
"assets": [{
  "id": "image_0",
  "w": 66,
  "h": 55,
  "u": "",
  "p": "img_0.png",
  "e": 0
}]
```

### 动画尺寸问题
修改`#lottie`的`width`和`height`。

### 完整示例
本示例`lottie_svg.min.js`使用官方CDN，实际使用的是公司CDN资源。

```html
<html xmlns="http://www.w3.org/1999/xhtml">
<meta charset="UTF-8">
<head>
  <style>
    body{
      margin: 0px;
      height: 100%;
      overflow: hidden;
    }
    #lottie{
      width:280px;
      height:300px;
      display:block;
      overflow: hidden;
      transform: translate3d(0,0,0);
      text-align: center;
      opacity: 1;
    }
  </style>
</head>
<body>
<script src="https://cdnjs.cloudflare.com/ajax/libs/bodymovin/5.5.1/lottie_svg.min.js" type="text/javascript"></script>
<div id="lottie"></div>
<script>
  var params = {
    container: document.getElementById('lottie'),
    renderer: 'svg',
    loop: true,
    autoplay: true,
    path: 'data.json'
  };
  var anim;
  anim = lottie.loadAnimation(params);
</script>
</body>
</html>
```

### 应用总结

1、资源大小
输出的webp格式动图接近2M。
这种方式就小了很多，库文件lottie_svg.min.js有63k，data.json有2k，html不到1k，所有的图片资源96.1k,不过不知道为什么图片资源会加载2次(后面分析源码搞清楚了加载两次的原因，见[lottie-web应用优化](https://lovelyun.github.io/js/lottie-web应用优化/)，造成了192.2k的图片资源流量。不过总体来说63+2+1+192.2=258.2k，是远小于2M的，只有原来的12.6%的大小，相当于节约了87.4%的流量。

2、开发效率高
设计输出动效资源，开发处理下上面列过的3个问题点就可以直接布署了，动效细节完美实现。


## 参考
* [lottie-web](https://github.com/airbnb/lottie-web)
