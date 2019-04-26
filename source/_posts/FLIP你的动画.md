---
title: FLIP你的动画
date: 2019-03-04 11:34:01
tags: animation
categories: animation
---

网页中的动效应该运行在60fps，达到这个帧率并不容易，它去取决于你做了多大的尝试，这里我将用FLIP来帮助你。

我已经写了一个[FLIP库](https://github.com/googlearchive/flipjs)，在这里你可以看到文档和demos。

FLIP本质上是一个原则，而不是框架或库。这是一种思考动画的方式，尝试让浏览器渲染动画的开销尽可能小，如果一切顺利，应该能达到60fps的动画。

## 基本概念
一般动画直接开始后，在每帧可能做开销大的计算，而这里我们让动画从头开始，动态的重新计算动效属性，然后让浏览器简单的执行渲染。
FLIP代表First,Last,Invert.Play。
* First: 元素涉及到动效的初值状态
* Last: 元素的最终状态
* Invert: 指出从动画开始到结束元素要如何变化，比如`width`、`height`、`opacity`。然后用`transform`、`opacity`来反向设置。如果元素从开始到结束下移了90px，你就要应用`transformY(-90px)`，让元素看起来和开始时一样，虽然实际上不是。
* Play: 开始变化任何你改变的属性，然后去掉逆变换。因为元素处于最终位置，所以反向设置中的`transform`、`opacity`，将使它们从模拟的第一个位置轻松变换到最后一个位置。

![flip-layout](/img/flip-layout-6.png)
![flip-layout](/img/flip-layout-7.gif)

## 代码分解

```javascript
// 获取初始位置
var first = el.getBoundingClientRect();

// 设置到最终位置
el.classList.add('totes-at-the-end');

// 再次获取位置信息. 注意这会引起重排.
var last = el.getBoundingClientRect();

// 需要的话也可以操作其他可计算的样式。
// 但要确保尽量使用只触发重绘的属性，比如transform、opacity
var invert = first.top - last.top;

// Invert.
el.style.transform = `translateY(${invert}px)`;

// 等待下一帧，这样我们就知道所有的样式更改都已生效
requestAnimationFrame(function() {

  // 开始动画过程
  el.classList.add('animate-on-transforms');

  el.style.transform = '';
});

// 用transitionend捕获动画结束
el.addEventListener('transitionend', tidyUpAnimations);
```

然而，也可以用即将来临的[Web Animations API](https://drafts.csswg.org/web-animations/)，这个会更简单，只是需要[Web Animations API polyfill](https://github.com/web-animations/web-animations-js)，不过这个补丁很轻量，并且确实很实用。

```javascript
// 获取初始位置
var first = el.getBoundingClientRect();

// 移动到最终位置
el.classList.add('totes-at-the-end');

// 获取最终位置
var last = el.getBoundingClientRect();

// Invert.
var invert = first.top - last.top;

// 从inverted位置移动到最终last位置.
var player = el.animate([
  { transform: `translateY(${invert}px)` },
  { transform: 'translateY(0)' }
], {
  duration: 300,
  easing: 'cubic-bezier(0,0,0.32,1)',
});

// 在动画结束时做任何需要的整理
player.addEventListener('finish', tidyUpAnimations);
```

## FLIP的用途
当你在响应到用户输入后用一些动画来响应，这绝对是非常棒的。比如，在Chrome开发峰会上，我展开用户点击的卡片，通常情况下，元素开始和结束的位置、大小都不知道，因为网页是响应式的，元素位置大小都不固定，但FLIP就很有用，它能明确的计算元素，在运行时给出正确的值。

你能够做这种相对昂贵的预计算是因为利用了用户感知。用户和你的网页有交互之后，你有趁他们不注意的100ms时间来做这些。在这100ms内，用户会觉得网站是立刻响应了的，而你只需要在动画过程中保持60fps。

我们可以利用这个感知期(100ms)，通过javascript做`getBoundingClientRect`操作（或者你非要用不优雅的`getComputedStyle`），这样我们使动画细腻流畅，利于重绘。

可以用`transform`和`opacity`重写动画是最好的，如果你在js或CSS中没有用这些属性，那你可以开始优化了，它们会在你改变布局属性（比如`width`、`height`、`left`、`top`）时，用开销小的属性重写后达到最好的优化效果。

有时你为了用FLIP需要重新构思你的动画，在多数情况下，我把动画元素单独提取出来，这样我就可以不失真的制作动画了，并且尽可能多的用FLIP。你可能会觉得这样应用过度了，但我觉得不是，因为：

1、大家想这样。我的一个同事兼好友最近做了一个关于人们想从新闻app上得到什么的调查，最多的回答（这让他很吃惊）不是离线支持、同步、通知，或类似的东西，而是浏览平滑——没有晃动、没有卡顿、没有颤抖。

2、程序员就是这么做的。当然，这是一种主观的衡量标准，但我已经听过很多次，程序员花了好几天的时间才把过渡做得恰到好处。通过运维服务，我们我网站加载速度都很快，用户会根据他们的操作体验来评价我们的网站，而这些细节将使我们与众不同。

## 注意事项
使用FLIP时要注意以下几点：
1、不要超过100ms的窗口期。
记住一定不要超过100ms，如果超过了，你的应用会表现得没反应。通过DevTools关注它，了解是否超出了100ms。

2、认真设计动画。
想象下，你在运行一个动画，做一些位移和透明度得改变，然后你决定运行另外一个动画，它需要大量得预计算，这会打断之前运行中的动画，这种情况很糟糕。这里的关键是保证你的预计算工作是在空闲时间或上面说过的100ms窗口期内完成的，这两个动画不会互相阻断。

3、内容会失真。
当你使用`scale`和`transform`，一些元素会失真。上面说过，我会调整一点结构，来不失真的使用FLIP，不过这可能有争议。

## 总结
我喜欢用FLIP来考虑动画的实现，因为它让JS和CSS配合的很好，用JS来计算，但用CSS处理动画过程，你不需要用CSS来实现动画，虽然你可以只用简单的Web Animations API或JS，或什么其他的简单的方式。主要的一点是，你正在降低每帧的复杂性和成本(通常意味着`transform `和`opacity`)，以尽量为用户提供最好的体验。

所以，使用FLIP吧。

## 参考
* [Aerotwist - FLIP Your Animations](https://aerotwist.com/blog/flip-your-animations/)
* [FLIP技术给Web布局带来的变化_JavaScript, FLIP, Animation, Web动画 教程_w3cplus](https://www.w3cplus.com/javascript/animating-layouts-with-the-flip-technique.html)
