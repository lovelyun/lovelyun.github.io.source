---
title: javascript基础(十六)-JavaScript基础篇总结
date: 2019-07-12 15:51:23
tags: js
categories: js
---

> 本文译自[javascript-specials](https://javascript.info/javascript-specials)

本章简要回顾了我们目前学到的JavaScript特性，特别关注一些细节。

## 代码组织
语句用分号分隔:
```javascript
alert('Hello'); alert('World');
```
通常，新的一行被视为分隔，下面写法也对：
```javascript
alert('Hello')
alert('World')
```
这叫做自动分号分隔，不过有时会出错：
```javascript
alert("这条信息后面会出错")

[1, 2].forEach(alert)
```

大多数代码风格指南推荐在每条语句后面加分号。
语句块`{...}`后面不需要分号：
```javascript
function f() {
  // 函数申明后不需要分号
}

for(;;) {
  // 循环后面不需要分号
}
```

即使我们在某个地方写下多余的分号，也不会有问题，它只会被忽略。

## 严格模式
要完全启用现代JavaScript的所有特性，我们需要开启严格模式：
```javascript
'use strict';

...
```

严格模式指令必须在代码顶部。
没有`'use strict'`也可以工作，只是一些功能表现得像旧式的“兼容”方式。我们更喜欢现代的行为。
一些现代的特性，比如classes，会隐式的开始严格模式。

## 变量
有3中方式申明变量：
* let
* const（常量，不可以改变）
* var（老式写法）

变量命名可以有：
* 字母和数字，但是不能用数字开头。
* 字符$和_与字母一样是正常的。
* 非拉丁字母和象形文字也是允许的，但通常不使用。

变量是动态类型，可以存储任何值：
```javascript
let x = 5;
x = "John";
```

有7中数据类型：
* `number`-浮点数和整数
* `strin`g-字符串
* `boolean`-布尔值`true/false`
* `null`-只有一个`null`值的类型，表示空或不存在
* `undefined`-只有一个`undefined`值的类型，表示未定义
* `objec`t`和`symbol`-复杂的数据结构和惟一标识符，我们还没有学习过。

`typeof`操作符返回数据类型，有2个坑：
```javascript
typeof null == "object" // 语言本身错误
typeof function(){} == "function" // 函数被特别对待
```

## 操作符
### 算术操作符
常规的`+ - * /`，还有求余`%`，`**`表示一个数的幂。
`+`操作符连接字符串，只要有一个操作数是字符串，其余的也会转成字符串：
```javascript
alert( '1' + 2 ); // '12', string
alert( 1 + '2' ); // '12', string
```

### 赋值
简单赋值比如`a = b`,组合赋值比如`a *= 2`。

### 位运算
位操作符在位级上处理整数，需要的时候看[文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators)。

### 三目
唯一一个有3个操作数的操作符：`cond ? resultA : resultB`。如果cond是true，返回resultA，否则返回resultB。

### 逻辑操作符
逻辑与`&&`和或`||`执行短路运算，然后返回它停止的值。逻辑非`!`将操作数转换为布尔类型并返回反值。

### 比较
`==`先转换类型成number，再比较值。
`===`不转换类型，类型不同就不等。
`null`和`undefined`比较特殊，它们互相`==`，但不等于其他任何值。
`>`和`<`在比较字符串的时候会逐个字母的比较，其他类型会先转成数字后再比较。

## 循环
我们了解了3中循环：
```javascript
// 1
while (condition) {
  ...
}

// 2
do {
  ...
} while (condition);

// 3
for(let i = 0; i < 10; i++) {
  ...
}
```

* 在`for(let...)`里申明的变量只在循环内部可见，不过我们也可以省略let并重用现有的变量。
* `break/continue`指令可以中断整个或当前循环，可以使用标签结束嵌套的多次循环。

## switch
switch可以代替多层if语句，比较时是`===`。
比如：
```javascript
let age = prompt('你的年龄?', 18);

switch (age) {
  case 18:
    alert("不执行"); // prompt的结果是字符串，不是数字

  case "18":
    alert("执行!");
    break;

  default:
    alert("不等于上面2个值的其他值");
}
```

## 函数
我们了解了3种函数的写法：
1、函数申明
```javascript
function sum(a, b) {
  let result = a + b;

  return result;
}
```

2、函数表达式
```javascript
let sum = function(a, b) {
  let result = a + b;

  return result;
}
```
函数表达式可以有名字，比如`sum = function name(a, b)`，不过名字只在函数内部可见。

3、箭头函数
```javascript
// 箭头右侧只有一个表达式
let sum = (a, b) => a + b;

// 用 { ... }写多行语句, 需要 return:
let sum = (a, b) => {
  // ...
  return a + b;
}

// 没有传参
let sayHi = () => alert("Hello");

// 只有1个传参
let double = n => n * 2;
```

* 函数可能有内部变量，在函数体中申明，这些变量只在函数内部可见
* 传参可以有默认值：`function sum(a = 1, b = 2) {...}`。
* 函数总有返回值，如果没有return,return的值就是undefined。
