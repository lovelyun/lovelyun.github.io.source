---
title: javascript基础(九)-交互alert-prompt-confirm
date: 2019-06-06 10:36:29
tags: js
categories: js
---

> 本文译自[alert-prompt-confirm](https://javascript.info/alert-prompt-confirm)

在本教程的这一部分中，我们将“按原样”介绍JavaScript语言，不做环境特定的调整。
但是我们仍然使用浏览器作为演示环境，所以我们至少应该知道它的一些用户界面功能。在本章中，我们将熟悉浏览器的警告alert、提示prompt和确认confirm功能。

## alert
显示一条消息，并暂停脚本执行，直到用户按下“OK”。
```javascript
alert("Hello");
```
带有消息的迷你窗口称为模态窗。“模态”一词的意思是访问者在处理窗口之前不能与页面的其他部分交互，不能按其他按钮等。在这种情况下，直到他们按下“OK”。

## prompt
`prompt`函数接受两个参数：
```javascript
result = prompt(title, [default]);
```
它显示一个带有文本消息的模态窗、访问者的输入字段和OK/CANCEL按钮。

* <b>title</b>
显示给浏览者的文本。

* <b>default</b>
可选的第二个参数，输入字段的初始值。
访问者可以在输入字段中输入一些内容，然后按OK按钮。或者按cancel/Esc键来取消输入。

`prompt`函数返回输入框的内容，如果取消输入就返回null：
```javascript
let age = prompt('How old are you?', 100);

alert(`You are ${age} years old!`); // You are 100 years old!
```

注意：IE浏览器中，永远需要参数default
第二个参数是可选的，但是如果我们不写，IE浏览器就会在输入框中显示"undefined"。所以为了在IE中好看些，我们建议永远带上第二个参数：
```javascript
let test = prompt("Test", ''); // <-- 为了 IE
```

## confirm
`confirm`函数显示一个模态窗，有一个问题和两个按钮：OK按钮和CANCEL按钮。
按了OK按钮，就返回true，否则返回false。
```javascript
let isBoss = confirm("Are you the boss?");

alert( isBoss ); // 如果按OK按钮就是true
```

## 总结
我们介绍了3个浏览器特有的功能来与访客互动:

<b>alert</b>
显示一条信息

<b>prompt</b>
显示一条要求用户输入文本的消息。它返回文本，如果CANCEL或Esc被单击，则返回null。

<b>confirm</b>
显示一条消息，等待用户按下“OK”或“CANCEL”。OK返回true, CANCEL/Esc返回false。

所有这些方法都是模态的:它们暂停脚本执行，并且在窗口被关闭之前不允许访问者与页面的其他部分交互。
以上所有方法都有两个限制:
1、模态窗口的显示位置由浏览器决定。通常，它在中间。
2、窗口的外观也取决于浏览器。我们不能修改它。

这就是简单的代价。还有其他方法可以展示更好的窗户和更丰富的与用户的互动，但如果不注重用户体验，这些方法也可以很好地工作。
