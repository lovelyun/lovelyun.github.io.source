---
title: openCVjs图像处理之手动矫正
date: 2021-10-19 14:30:32
tags: openCV
categories: 图像处理
---

本文将带大家看看怎么用openCVjs在前端实现图片手动矫正。就像下面这样，左边是原图，右边是矫正后的图：

![correct_demo](/img/opencv_correct/correct_demo.png)

首先框出需要矫正的四边形区域：
![correct_area](/img/opencv_correct/correct_area.png)

得到4个顶点，从左上角开始，顺时针排列，分别是p0、p1、p2、p3。
可以得到他们的准确坐标：

```javascript
p0 = [241, 60]
p1 = [763, 178]
p2 = [690, 689]
p3 = [188, 500]
```

矫正后的图，假设宽为`width`，高为`height`，那么矫正后的图4个顶点是：

```javascript
p4 = [0, 0]
p5 = [width, 0]
p6 = [width, height]
p7 = [0, height]
```

其中p0~p3与p4~p7对应，我们假设宽width为408，高height为380，可以通过`cv.getPerspectiveTransform`求出转换矩阵M：

```javascript
const srcPoints = [241, 60, 763, 178, 690, 689, 188, 500]
const dstPoints = [0, 0, 408, 0, 408, 380, 0, 380]
const srcTri = cv.matFromArray(4, 1, cv.CV_32FC2, srcPoints);
const dstTri = cv.matFromArray(4, 1, cv.CV_32FC2, dstPoints);
const M = cv.getPerspectiveTransform(srcTri, dstTri)
```

求出转转矩阵后，就可以通过`cv.warpPerspective`进行透视矫正：

```javascript
const dsize = new cv.Size(408, 380);
cv.warpPerspective(src, dst, M, dsize)
cv.imshow('canvasOutput', dst)
```

这样就得到了处理结果。现在我们看看简单的demo代码。
首先，初始化一个html：

```html
<!DOCTYPE html>
<html>
<head>
    <title>OpenCV.js手动矫正demo</title>
</head>
<body>
    <h3 id="status">Loading the Opencv ...</h3>
    <input type="file" id="fileInput" />
    <div id="changeImage">处理</div>
    <div class="wrap-image">
        <img id="imageUpload" alt="No Image" />
        <canvas id="canvasOutput"></canvas>
    </div>
    <script type="text/javascript">
        // TODO
    </script>
    <script async src="js/opencv.js" onload="onOpenCvReady();" type="text/javascript"></script>
</body>
</html>
```

再在上面的`TODO`处加入我们的逻辑代码，主要是一些事件绑定。

```javascript
const imgElement = document.getElementBy('imageUpload');
const inputElement = document.getElementBy('fileInput');
const changeImageElement = document.getElement('changeImage');

inputElement.onchange = function () {
    imgElement.src = URL.createObjectURL(eventarget.files[0]);
}
changeImageElement.onclick = function () {
    // 图片处理
}
function onOpenCvReady() {
    document.getElementById('status').remove();
}
```

最后在上面的图片处理处加入手动矫正相关代码，点击处理按钮时执行矫正。

```javascript
const src = cv.imread("imageUpload");
const dst = new cv.Mat();
const srcPoints = [241, 60, 763, 178, 690,188, 500]
const dstPoints = [0, 0, 408, 0, 408, 38380]
const srcTri = cv.matFromArray(4, 1, cv.CV_3srcPoints);
const dstTri = cv.matFromArray(4, 1, cv.CV_3dstPoints);
const M = cv.getPerspectiveTransform(srdstTri)
const dsize = new cv.Size(408, 380);
cv.warpPerspective(src, dst, M, dsize)
cv.imshow('canvasOutput', dst);
src.delete(); dst.delete();
```

>* 处理完不要忘记删除Mat哦

下面是原图，感兴趣的可以自己试试看。
![origin](/img/opencv_correct/origin.jpg)
