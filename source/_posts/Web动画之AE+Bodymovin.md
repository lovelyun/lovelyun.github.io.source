---
title: Web动画之AE+Bodymovin
date: 2019-03-28 10:46:58
tags: 动画
categories: 动画
---

## 需求背景
做一个故宫相关的活动，有3个图片素材要动态配置到页面中。其中有两个是动图，最大的超过1M。

## 业务背景
要实现上面的需求，现在的业务框架支持这么做：
1、设计提供素材图片，由于有动图，为了缩小图片大小，从原来的gif改成webp，不过依然很大；
2、运营从后台管理页面上传3个图片素材；
3、服务端将对应的素材地址返回给前端；
4、前端通过服务端给的地址实时获取图片资源，从而出现流量的一系列问题。

## AE+Bodymovin调研
为了解决上面出现的流量问题，我调研了AE+Bodymovin的动效模式。

### AE+Bodymovin简介
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

### 产品
1、可以较低成本配置小动图（比如，故宫活动最大的图有1M，现在只需要库文件48.3k和json文件，json文件一般不超过200k），由于数据大多是矢量数据，所以活动图可以做大却没有增加流量，相比之前图的大小，同样的流量可以做更多的图。
2、开发成本低。

简单说，活动图可以做大做多了。这将直接提升用户交互率。

## 应用
最近终于应用在业务中了，做了一个童话故事的活动，依托之前的html吊牌业务，不需要后端配合。设计输出资源的目录结构是这样的：
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
示例使用官方CDN，实际使用的是公司CDN资源。

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
这种方式就小了很多，库文件lottie_svg.min.js有63k，data.json有2k，html不到1k，所有的图片资源96.1k,不过不知道为什么图片资源会加载2次(后面分析源码搞清楚了加载两次的原因，见下文)，造成了192.2k的图片资源流量。不过总体来说63+2+1+192.2=258.2k，是远小于2M的，只有原来的12.6%的大小，相当于节约了87.4%的流量。

2、开发效率高
设计输出动效资源，开发处理下上面列过的3个问题点就可以直接布署了，动效细节完美实现。

## 应用优化

上面的应用过程最近找到了2点优化空间：
1、图片资源加载2次，增加了CDN流量。优化方向：图片加载次数减小到1次；
2、设计输出的图片资源多且零散，有9张小图片。优化方向：多张图片合并成1张雪碧图，减少网络请求。

### 加载次数
![loadTwice](/img/loadTwice.png)
上图可以看到，同一张图加载了2次，且返回的status都是200。

首先分析同一张图片加载2次的原因。

查看源码发现图片加载主要有预加载逻辑和页面元素创建逻辑两处：

![creatimagedata](/img/creatimagedata.png)

![creatcontent](/img/creatcontent.png)

预加载逻辑中会调用`creatimagedata`，这里会将所有的图片提前下载，后面创建svg页面元素时调用了`creatcontent`也会加载图片。

点击图片查看请求Headers，发现Request Headers中的Cache-Control值为no-cache,表示客户端缓存内容，但是是否使用缓存则需要经过协商缓存来验证决定。协商缓存的概念和协商过程这里就不叙述了，但很明显，这里的协商缓存没有命中，如果协商缓存命中，请求响应返回的http状态为304并且会显示一个Not Modified的字符串。

这就体现了协商缓存的缺陷：
如果资源更新的速度是秒以下单位，那么该缓存是不能被使用的，因为它的时间单位最低是秒。

而我们这里的资源下载速度最慢的也只有293ms，所以预加载的图片缓存并没有体现出预计的效果。所以图片被加载了2次。

如果不做上面方案二雪碧图的优化，可以简单的把预加载逻辑去掉。那么图片就不会加载2次了。

### 雪碧图
查看[lottie-web](https://github.com/airbnb/lottie-web)的文档和源码，发现并不支持雪碧图。
参阅开源项目[lottie-web-sprite](https://github.com/newbieYoung/lottie-web-sprite)源码后，对我们用到的lottie_svg做改造：

1、生成雪碧图
生成雪碧图使用[lia](https://github.com/cupools/lia)。步骤如下：

创建精灵图配置文件sprite_conf.js。（使用lia init命令可以自动生成配置文件，只不过因为需要使用自定义模版，所以这里手动创建）

```javascript
'use strict';

module.exports = [{
    src: ['*.png'], // 图片素材路径匹配规则
    image: 'sprite.png', // 生成的精灵图的路径
    style: 'sprite.js', // 生成的图片素材和精灵图的位置关系数据文件的路径
    tmpl: './template.ejs' // 图片素材和精灵图的位置关系数据文件模版
}];
```

创建图片素材和精灵图的位置关系数据模版文件template.ejs。

```
var opt = {
  width: <%= size.width %>,
  height: <%= size.height %>,
  src: '<%= realpath %>',
  count: <%= items.length %>,
  items: [
      <% items.forEach(function(item, idx) { -%>
      {
          index: <%= idx %>,
          name: '<%= item.name %>',
          width: <%= item.size.width %>,
          height: <%= item.size.height %>,
          x: <%= item.x %>,
          y: <%= item.y %>
      },
      <% }) -%>
  ]
}

module.exports = opt;
```

运行下面2行命令得到精灵图sprite.png以及位置关系数据文件sprite.js：

```bash
npm i -g lia
lia
```

把位置关系数据文件sprite.js中的绝对路径改为相对路径：`src: './sprite.png',`。

<div class="tip">
此文基础：所有文件都在同一目录层级，否则需要注意修改相对目录结构。
</div>

2、修改data.json
data.json新增字段_sprite，将sprite.js中的数据复制给_sprite。此时data.json中的数据看起来类似这样：

```
{
  "v": "5.5.0",
  "fr": 25,
  "ip": 0,
  ...
  "assets": [{
    "id": "image_0",
    "w": 66,
    "h": 55,
    "u": "",
    "p": "img_0.png",
    "e": 0
  }
  ...
  ],
  "layers": [...],
  "markers": [],
  "_sprite": {
    "width": 733,
    "height": 568,
    "src": "./sprite.png",
    "count": 9,
    "items": [{
        "index": 0,
        "name": "img_0",
        "width": 66,
        "height": 55,
        "x": 243,
        "y": 310
      }
      ...
    ]
  }
}
```

3、更改源码
更改configAnimation方法，增加preloadSprite方法和loadAssetsFromSprite方法，这些可参考[lottie-web-sprite](https://github.com/newbieYoung/lottie-web-sprite)。

另外需要更改getAssetsPath方法，如果有_spriteSrc，图片路径取雪碧图生成的base64编码。方法里的其他逻辑不变。

```javascript
AnimationItem.prototype.getAssetsPath = function (assetData) {
    var path = '';
    if (this._spriteSrc) {
        this.imagePreloader.images.assetData
        var i = 0, len = this.imagePreloader.images.length;
        while (i < len) {
            if(assetData.id == this.imagePreloader.images[i].assetData.id){
                return this.imagePreloader.images[i].img.src;
            }
            i += 1;
        }
    } else if(assetData.e) {
        path = assetData.p;
    } else if(this.assetsPath){
        var imagePath = assetData.p;
        if(imagePath.indexOf('images/') !== -1){
            imagePath = imagePath.split('/')[1];
        }
        path = this.assetsPath + imagePath;
    } else {
        path = this.path;
        path += assetData.u ? assetData.u : '';
        path += assetData.p;
    }
    return path;
};
```

现在就可以应用雪碧图了。
![loadSprite](/img/loadSprite.png)

<div class="tip">
lia自动生成的雪碧图还可以再压缩的哦
</div>
![spriteTiny](/img/spriteTiny.png)

优化效果总结：

||优化前|优化后|
| :--------: | :-----: | :-----: |
| 图片流量 |258.2k|68.4k|
| 请求次数 |18|1|

## 参考
* [lottie-web](https://github.com/airbnb/lottie-web)
* [lottie-web-sprite](https://github.com/newbieYoung/lottie-web-sprite)
* [精灵图在 Lottie Web 动画中的应用 - 知乎](https://zhuanlan.zhihu.com/p/52456720)
* [lia](https://github.com/cupools/lia)
