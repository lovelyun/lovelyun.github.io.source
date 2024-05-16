---
title: 前端使用openCV处理图片的基础
date: 2021-10-15 15:29:15
tags:
categories: 图像处理
---

## 前言
[前文](https://lovelyun.github.io/%E5%9B%BE%E5%83%8F%E5%A4%84%E7%90%86/openCV/)我们讲了openCV如何在前端应用。
本文主要根据官方文档的[Core Operations](https://docs.opencv.org/4.5.4/d1/d78/tutorial_js_table_of_contents_core.html)部分，带大家了解OpenCV图片处理时需要的一些基础知识，比如mat数据类型的操作，绘制形状等等。

## 获取图片属性

```javascript
let src = cv.imread('imageUpload');
console.log('image width: ' + src.cols + '\n' +
            'image height: ' + src.rows + '\n' +
            'image size: ' + src.size().width + '*' + src.size().height + '\n' +
            'image depth: ' + src.depth() + '\n' +
            'image channels ' + src.channels() + '\n' +
            'image type: ' + src.type() + '\n');
```

![effect-original](../images/2021/effect-original.avif)

比如上面这张图的属性打印出来就是：

```javascript
image width: 600
image height: 473
image size: 600*473
image depth: 0
image channels 4
image type: 24
```

### Mat
## 构造Mat
创建Mat实例时，可以传入size, type或者rows, cols, type，一般用默认构造方式：

```javascript
let mat = new cv.Mat();
```
或者用数组来构建：

```javascript
// 比如: let mat = cv.matFromArray(2, 2, cv.CV_8UC1, [1, 2, 3, 4]);
let mat = cv.matFromArray(rows, cols, type, array);
```

或者用imgData：

```javascript
let ctx = canvas.getContext("2d");
let imgData = ctx.getImageData(0, 0, canvas.width, canvas.height);
let mat = cv.matFromImageData(imgData);
```

另外，还有3个静态函数：

```javascript
// 1. 创建一个全是0的Mat
let mat = cv.Mat.zeros(rows, cols, type);
// 2. 创建一个全是1的Mat
let mat = cv.Mat.ones(rows, cols, type);
// 3. 创建一个单位矩阵的Mat
let mat = cv.Mat.eye(rows, cols, type);
```

> Mat实例一定记得要及时删除

### 复制Mat
有2种复制方法：

```javascript
// 1. Clone
let dst = src.clone();
// 2. CopyTo
src.copyTo(dst, mask);
```

### 转换Mat类型
src.convertTo(dst, rtype)
rtype代表期望的输出矩阵类型，或者说是深度，因为通道的数量与输入相同;如果rtype为负，输出矩阵的类型将与输入矩阵的相同。

## 读写像素
### data
首先，需要了解不同的Data属性在不同语言中的type之间的关系：
|Data属性|C++ Type|JavaScript Typed Array|Mat Type|
|:---|:---|:---|:---|
|data|uchar|Uint8Array|CV_8U|
|data8S|char|Int8Array|CV_8S|
|data16U|ushort|Uint16Array|CV_16U|
|data16S|short|Int16Array|CV_16S|
|data32S|int|Int32Array|CV_32S|
|data32F|float|Float32Array|CV_32F|
|data64F|double|Float64Array|CV_64F|

通过data获取像素：

```javascript
let row = 3, col = 4;
let src = cv.imread("canvasInput");
if (src.isContinuous()) {
    let index = row * src.cols * src.channels() + col * src.channels()
    let R = src.data[index];
    let G = src.data[index + 1];
    let B = src.data[index + 2];
    let A = src.data[index + 3];
}
```

> data操作只对连续的Mat有效，使用前应该用isContinuous函数检查。


### at

|Mat Type|At 操作|
|:---|:---|
|CV_8U|ucharAt|
|CV_8S|charAt|
|CV_16U|ushortAt|
|CV_16S|shortAt|
|CV_32S|intAt|
|CV_32F|floatAt|
|CV_64F|doubleAt|

通过at获取像素：

```javascript
let row = 3, col = 4;
let src = cv.imread("canvasInput");
let colIndex = col * src.channels()
let R = src.ucharAt(row, colIndex);
let G = src.ucharAt(row, colIndex + 1);
let B = src.ucharAt(row, colIndex + 2);
let A = src.ucharAt(row, colIndex + 3);
```

> at操作只能读，不能写。

### ptr

|Mat Type|Ptr 操作|JavaScript Typed Array|
|:---|:---|:---|
|CV_8U|ucharPtr|Uint8Array|
|CV_8S|charPtr|Int8Array|
|CV_16U|ushortPtr|Uint16Array|
|CV_16S|shortPtr|Int16Array|
|CV_32S|intPtr|Int32Array|
|CV_32F|floatPtr|Float32Array|
|CV_64F|doublePtr|Float64Array|

mat.ucharPtr(k)获取mat的第k行，mat.ucharPtr(i, j)获取mat的第i行第j列。

```javascript
let row = 3, col = 4;
let src = cv.imread("canvasInput");
let pixel = src.ucharPtr(row, col);
let R = pixel[0];
let G = pixel[1];
let B = pixel[2];
let A = pixel[3];
```

### 颜色通道操作
有时我们需要单独操作图片的R/G/B通道，这时就需要对颜色通道进行分割，处理完毕后再合并。

```javascript
let src = cv.imread("canvasInput");
let rgbaPlanes = new cv.MatVector();
// 分割
cv.split(src, rgbaPlanes);
// 获取R通道
let R = rgbaPlanes.get(0);
// 合并
cv.merge(rgbaPlanes, src);
src.delete(); rgbaPlanes.delete(); R.delete();
```

## 坐标点Point
有2种方式创建一个点：

```javascript
let point = new cv.Point(x, y);
let point = {x: x, y: y};
```

## 像素点Scalar

```javascript
let scalar = new cv.Scalar(R, G, B, Alpha);
let scalar = [R, G, B, Alpha];
```

## 尺寸Size

```javascript
let size = new cv.Size(width, height);
let size = {width : width, height : height};
```

## 圆

```javascript
let circle = new cv.Circle(center, radius);
let circle = {center : center, radius : radius};
```

## 方形

```javascript
let rect = new cv.Rect(x, y, width, height);
let rect = {x : x, y : y, width : width, height : height};
```
带旋转角度的方形：
```javascript
let rotatedRect = new cv.RotatedRect(center, size, angle);
let rotatedRect = {center : center, size : size, angle : angle};
```
通过下面的方法获取方形的4个顶点：
```javascript
let vertices = cv.RotatedRect.points(rotatedRect);
let point1 = vertices[0];
let point2 = vertices[1];
let point3 = vertices[2];
let point4 = vertices[3];
```
通过下面的方法获取方形的边界：
```javascript
let boundingRect = cv.RotatedRect.boundingRect(rotatedRect);
```
