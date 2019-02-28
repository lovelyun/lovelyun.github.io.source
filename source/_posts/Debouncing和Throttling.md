---
title: Debouncing和Throttling
date: 2019-02-27 15:47:44
tags: js
categories: js
---

本文出自David Corbacho，一名伦敦的前端工程师。

`Debounce` 和 `throttle`是两种相似但不同的技术，用来控制函数在一定时间内执行的次数，简单说是用来限频。

当我们的函数操作DOM事件时，对函数用使用`Debounce` 或 `throttle`非常有用，因为我们在DOM事件和函数执行之间加了我们的控制层。

当我们通过触控板、滚轮、拖动滚动条来滚动时，会很轻易的每秒触发30次滚动事件，但是在智能手机上测试缓慢的滚动时，每秒可触发多达100次滚动事件，你的滚动回调为这样高频的执行做好准备了吗？

2011年，Twitter网站突现了一个问题:页面在向下滚动时变得卡顿。John Resig发表了一篇关于这个问题的博文，来解释直接将昂贵的函数绑定到滚动事件上多么糟糕。John建议在滚动回调函数外包裹一个每250ms执行一次的循环，这样滚动回调不与滚动事件耦合，简单的避免了用户体验差的问题。

现在处理类似的高频事件的方式稍微复杂点。下面介绍Debounce, Throttle, 和 requestAnimationFrame。

## Debounce
Debounce可以把连续的多个调用分组到一个调用中。
![debounce](/img/debounce.webp)

想象下你在电梯里，门开始关闭，突然有人要进来，电梯没有改变楼层，门又开了，每次要关门时，如果有人要进来，都会再次开门，电梯在推迟移动到其他楼层，但在优化资源。

### Leading edge（或immediate）
在事件发生间隔变的足够长之前，Debounce会一直等待，推迟回调的执行，为什么不立刻触发回调的执行，看起来就像没有用Debounce处理过，只是在快速连续的触发事件停止之前不要再次执行，就像下面这样：
![debounce-leading](/img/debounce-leading.webp)

在underscore.js中, 这个参数叫immediate，而不是leading。

### Debounce实现
第一次看到Debounce的js实现是2009年John Hann的博文《[Debouncing Javascript Methods](http://unscriptable.com/2009/03/20/debouncing-javascript-methods/)》，此后不久，Ben Alman创建了一个jQuery插件(不再维护)，一年后，Jeremy Ashkenas将其添加到underscore.js中。后来，它被添加到Lodash中。这3种实现在内部略有不同，但它们的接口几乎相同。有一段时间underscore.js采用了Lodash的debounce/throttle实现，直到我2013年发现了`_.debounce`函数的一个bug，这两种实现逐渐不同。

Lodash在`_.debounce`和`_.throttle`中添加了更多的功能。原来的immediate参数被leading和trailing替代，你可以选择1个，或两个都选，默认只用trailing。

本文没有介绍的maxWait参数（目前仅在Lodash中）是非常有用的，实际上，在lodash源码中可以看到，Throttle函数的定义`_.debounce`中有maxWait。

### Debounce示例

1、resize
在拖动大小控制器来resize浏览器窗口时，可以触发很多次resize事件。
完整示例请查看原文[Debouncing and Throttling Explained Through Examples | CSS-Tricks](https://css-tricks.com/debouncing-throttling-explained-examples/)

```javascript
// Based on http://www.paulirish.com/2009/throttled-smartresize-jquery-event-handler/
$(document).ready(function(){

  var $win = $(window);
  var $left_panel = $('.left-panel');
  var $right_panel = $('.right-panel');

  function display_info($div) {
    $div.append($win.width() + ' x ' + $win.height() +  '<br>');
  }

  $(window).on('resize', function(){
    display_info($left_panel);
  });

  $(window).on('resize', _.debounce(function() {
    display_info($right_panel);
  }, 400));
});
```

resize事件中我们用了默认参数trailing，因为我们只关心浏览器停止改变窗口大小后的最终值。

2、按键输入关联的ajax请求
为什么我们要在用户还是输入的时候每50ms向服务器发ajax请求呢？`_.debounce`可以帮助我们避免这种冗余的造作，只在用户停止输入的时候发送请求。

此时，使用leading参数没有意思，我们要等最后一个字符输入完毕。

```javascript
$(document).ready(function(){

  var $statusKey = $('.status-key');
  var $statusAjax = $('.status-ajax');
  var intervalId;

  // 仅为示例模拟的假ajax
  function make_ajax_request(e){
    var that = this;
    $statusAjax.html('等待时间足够了，现在进行数据请求');

    intervalId = setTimeout(function(){
       $statusKey.html('在这里输入，当你停止输入的时候我会发现');
       $statusAjax.html('');
       $(that).val(''); // 清空输入值
    },2000);
  }

  // 当事件被触发时显示信息
  $('.autocomplete')
  .on('keydown', function (){
    $statusKey.html('等待后续输入... ');
    clearInterval(intervalId);
  })

  // 显示数据请求什么时候会发生（停止输入后）
  // 为了示例更明显，设置了超长的1.3s等待时长。实际情况下最好等待50ms到200ms
  $('.autocomplete').on('keydown', _.debounce(make_ajax_request, 1300));
});
```

类似的情形还可能是等到用户停止输入再校验其输入内容，然后显示类似“您的密码位数太短”的提示语。

### 如何使用debounce和throttle以及其中的坑
自己写一个debounce/throttle功能是很容易的，或者随便从哪个博客里复制一个。我的建议是直接用underscore或Lodash，如果你只需要`_.debounce`和`_.throttle`方法，你可以用Lodash的自定义构建输出一个2KB的压缩包，构建命令很简单：

```
npm i -g lodash-cli
lodash include = debounce, throttle
```
也就是说，一般会通过webpack/browserify/rollup使用`lodash/throttle`和`lodash/debounce`或`lodash.throttle`和`lodash.debounce`的包。

一个常见的坑是不止1次的调用`_.debounce`函数：

```javascript
// 错误的写法
$(window).on('scroll', function() {
   _.debounce(doSomething, 300);
});

// 正确的写法
$(window).on('scroll', _.debounce(doSomething, 200));
```

如果你有需要，在Lodash和underscore.js中，为需要debounce的函数创建一个变量将允许我们调用私有方法`debounced_version.cancel()`。

```javascript
var debounced_version = _.debounce(doSomething, 200);
$(window).on('scroll', debounced_version);

// 如果有需要
debounced_version.cancel();
```

## Throttle
使用了`_.throttle`,我们不允许函数每X毫秒执行超过1次。

throttle和debounce的主要区别是，throttle保证函数有规律的执行，至少每X毫秒执行1次。
和debounce一样，throttle也包含在Ben的插件、underscore.js和lodash中。

### Throttle示例
1、无限滚动
一个常见的例子：用户向下滑动你的无限滚动页面，你需要检查用户距离页面底部有多远，如果快滑到底部了，你就要通过ajax请求更多的数据来填充页面。
此时`_.debounce`没用了，它只能在用户停止滑动时触发，而我们需要在用户滑到底之前请求数据，`_.throttle`可以让我们不停的检查距离底部的距离。

```javascript
// 这是一个很简单的例子。
// 或许你想用类似下面这样的插件
// https://github.com/infinite-scroll/infinite-scroll/blob/master/jquery.infinitescroll.js
$(document).ready(function(){

  // 每200ms检查一下滚动位置
  $(document).on('scroll', _.throttle(function(){
    check_if_needs_more_content();
  }, 200));

  function check_if_needs_more_content() {
    pixelsFromWindowBottomToBottom = 0 + $(document).height() - $(window).scrollTop() -$(window).height();
    // console.log($(document).height());
    // console.log($(window).scrollTop());
    // console.log($(window).height());
    // console.log(pixelsFromWindowBottomToBottom);
    if (pixelsFromWindowBottomToBottom < 200){
      // 这里会有一个数据请求
      $('body').append($('.item').clone());
    }
  }
});
```

## requestAnimationFrame (rAF)
requestAnimationFrame是另外一种限频方式，可以看成`_.throttle(dosomething, 16)`，但是会精确很多，因为它是为了更好的精确度的浏览器原生API。

考虑下列优缺点后，我们可以酌情用rAF API替代throttle。

优点：
1、目标是60fps (16ms每帧)，但内部将决定如何安排渲染的最佳时间；
2、非常简单且标准的API，将来不会改变，便于维护。

缺点：
1、我们需要开始或取消rAF，不像.debounce或.throttle自己在内部处理；
2、如果浏览器页面不是激活状态，rAF将不会执行，虽然对于滚动、鼠标、键盘事件这并不重要；
3、虽然所有的现代浏览器支持rAF，但是IE9、Opera Mini、和老的安卓并不支持，现在还需要打[补丁](https://www.paulirish.com/2011/requestanimationframe-for-smart-animating/)；
4、node.js不支持rAF，所以不能用在服务端的文件系统事件。

一般来说，如果js函数需要重新计算元素位置，比如直接渲染或动效，我会用requestAnimationFrame。发起数据请求，增加或移除控制动效的css class，我会用`_.debounce`或` _.throttle`，这样可以更低的执行频率，比如200ms，而不是16ms。
你或许觉得rAF应该在underscore或lodash中实现，但他们都没有，因为它用途少并容易直接使用。

### rAF示例
Paul Lewis写的的《[Leaner, Meaner, Faster Animations with requestAnimationFrame](https://www.html5rocks.com/en/tutorials/speed/animations/)》一步一步的介绍了这个例子的逻辑，受他的启发，这里我只会介绍requestAnimation用在滚动上的例子。

我把`_.throttle`限频16ms和rAF放在一起对比，实现类似的功能，但极有可能rAF在复杂的场景会表现的更好。
```javascript
// 参考https://www.html5rocks.com/en/tutorials/speed/animations/#debouncing-scroll-events
var latestKnownScrollY = 0,
    ticking = false,
    item = document.querySelectorAll('.item');

function update() {
  // 重置tick，使我们能够捕获下一个onScroll
  ticking = false;

  item[0].style.width = latestKnownScrollY + 100 + 'px';
}

function onScroll() {
  latestKnownScrollY = window.scrollY; // 不支持IE8
  requestTick();
}

function requestTick() {
  if(!ticking) {
    requestAnimationFrame(update);
  }
  ticking = true;
}

window.addEventListener('scroll', onScroll, false);


// THROTTLE
function throttled_version() {
  item[1].style.width = window.scrollY + 100 + 'px';
}

window.addEventListener('scroll', _.throttle(throttled_version, 16), false);
```

rAF的一个更好的例子我在[headroom.js](https://github.com/WickyNilliams/headroom.js/blob/3282c23bc69b14f21bfbaf66704fa37b58e3241d/src/Debouncer.js)库中看到过，它把逻辑解耦并封装。

## 总结
使用debounce, throttle 和 requestAnimationFrame来优化事件处理，它们略有不同，但都很有用，并相互补充。

|||
| :--------: | :----- |
| debounce |把突然爆发的大量事件（比如连续快速的按键输入）组合成1个事件|
| throttle |保证每X毫秒执行1次的持续的事件流。比如每200ms检查下滚动位置来触发CSS动效|
| requestAnimationFrame |代替throttle。当你在屏幕上重新计算并渲染元素，想保证变化或动效的流畅时使用。注意：不支持IE9|

## 参考
* [Debouncing and Throttling Explained Through Examples](https://css-tricks.com/debouncing-throttling-explained-examples/)
