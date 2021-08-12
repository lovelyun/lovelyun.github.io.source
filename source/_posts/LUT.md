---
title: 前端如何通过LUT实现图片滤镜
date: 2021-08-09 15:58:53
tags: 图像
categories: 图像处理
---

## 前言
说到图片滤镜，相信大家都不陌生，比如什么晨光效果、小清新效果，但大家都清楚滤镜效果是怎么实现的吗？比如下面两个好看的滤镜效果：
![film-emulation-lut-21613370085.jpg](/img/film-emulation-lut-21613370085.jpg)
![lut-film-emulation-271613371435.jpg](/img/lut-film-emulation-271613371435.jpg)
在前端，一般的图像处理库，都是基于算法来实现的，获取图片的像素，解析成R、G、B，然后对这3个颜色一通操作，比如R都变成255，G都减25...，或者复杂点，进行个卷积运算，比如fabric.js的滤镜效果。
但是这些算法实现的滤镜效果不仅数量少，效果也不够丰富，只能实现些简单的效果，比如反色、灰阶、怀旧等等。如果设计师在PS中对图片进行了风格调色，比如相机校正、曲线、色彩分割、HSL调整等等项目，在程序上我们又要如何实现呢？
本文要介绍的图片滤镜实现方式就可以解决上面的问题，只要是设计师实现的滤镜效果，我们都可以实现，而且，还有很多免费的滤镜效果可以使用，不一定需要设计师输出，滤镜效果好，并且处理速度快，这种方式就是3D LUT（look up table），即3D颜色查找表。

## 准备工作
### 3D LUT资源文件
设计师输出.CUBE文件，或者网上找免费资源。
图片的风格处理大都比较复杂，对设计师来说，`Photoshop`中内建了几个影片的`3D LUT`电影调色档，也可以用外部导入的电影调色档，所以我们也可以直接用设计师们用到的外部电影调色档，资源的格式一般是.CUBE文件。

### 3D LUT资源文件格式处理
如果前端直接使用.CUBE文件，不仅文件体积大，而且需要做文件解析，但是如果把.CUBE文件转成png，不仅体积小很多，也不用解析文本，直接解析png图片中的像素即可，这里推荐一个[cube转png小工具](http://yyb.gtimg.com/aiplat/static/qcloud-cube-to-png.html)，处理后的png一般是下面这样，宽高尺寸是512x512。
![Going for a walk.png](/img/Going-for-a-walk.png)

## 前端实现基于3D LUT的滤镜

下面我们写一个简单的页面，实现用户上传原图和lut图，就可以得到处理后的图，并且可以点击下载处理后的图。

```html
<!DOCTYPE html>
<html>
    <head>
        <title>LUT</title>
        <style type="text/css">
        canvas {
            width: 300px;
        }
        </style>
    </head>

    <body>
        <a href="#" id="downloadButton">下载</a>
        <input type="file" id="fileInput" name="选择图片"/>
        <input type="file" id="lutInput"/>
        <div class="wrap-image">
            <canvas id="imageUpload"></canvas>
            <canvas id="lutUpload"></canvas>
            <canvas id="canvasOutput"></canvas>
        </div>
        <script type="text/javascript">
            var imgElement = document.getElementById('imageUpload');
            var lutElement = document.getElementById('lutUpload');
            var outputElement = document.getElementById('canvasOutput');

            var fileInput = document.getElementById('fileInput');
            var lutInput = document.getElementById('lutInput');

            fileInput.addEventListener('change', (e) => {
                var img = new Image
                img.src = URL.createObjectURL(e.target.files[0]);
                img.onload = function(){
                    imgElement.width = img.width
                    imgElement.height = img.height
                    outputElement.width = img.width
                    outputElement.height = img.height
                    var context = imgElement.getContext("2d");
                    context.drawImage(img, 0, 0);
                }
            }, false);
            lutInput.addEventListener('change', (e) => {
                var img = new Image
                img.src = URL.createObjectURL(e.target.files[0]);
                img.onload = function(){
                    lutElement.width = img.width
                    lutElement.height = img.height
                    var context = lutElement.getContext("2d");
                    context.drawImage(img, 0, 0);
                    applyLUT("canvasOutput");
                }
            }, false);
            function applyLUT(resultID) {
                var imageContext = imgElement.getContext("2d");
                var lutContext = lutElement.getContext("2d");
                var imageData = imageContext.getImageData(0, 0, imgElement.width, imgElement.height);
                var lutData = lutContext.getImageData(0, 0, lutElement.width, lutElement.height);
                for (var i = 0; i < imageData.data.length; i += 4) {
                    var r = Math.floor(imageData.data[i] / 4);
                    var g = Math.floor(imageData.data[i + 1] / 4);
                    var b = Math.floor(imageData.data[i + 2] / 4);
                    var lutX = (b % 8) * 64 + r;
                    var lutY = Math.floor(b / 8) * 64 + g;
                    var lutIndex = (lutY * lutElement.width + lutX) * 4;
                    imageData.data[i] = lutData.data[lutIndex];
                    imageData.data[i + 1] = lutData.data[lutIndex + 1];;
                    imageData.data[i + 2] = lutData.data[lutIndex + 2];;
                }
                document.getElementById(resultID).getContext("2d").putImageData(imageData, 0, 0);
            };
            document.getElementById('downloadButton').onclick = function() {
              this.href = document.getElementById('canvasOutput').toDataURL();
              this.download = 'image.png';
            };
        </script>
    </body>
</html>
```

效果如下，左边是原图，中间是3D LUT文件，右边是处理后的图，效果不错吧！
![goingforawalk.png](/img/goingforawalk.png)

怎么样，效果是不是特别好？而且3D LUT不仅可以处理图片，还可以处理视频哦。

## 参考
* [A function that helps to apply LUT to image. Make sure to change the canvas IDs or to create temporary canvases](https://gist.github.com/kishmiryan-karlen/559c190f6c20856ee323)
* [Playing with JavaScript, photos and 3D LUTS (lookup tables)](https://www.emanueleferonato.com/2018/06/09/playing-with-javascript-photos-and-3d-luts-lookup-tables/)
* [free-luts](https://purple11.com/free-luts/)
* [film-emulation-luts](https://fixthephoto.com/film-emulation-luts)
