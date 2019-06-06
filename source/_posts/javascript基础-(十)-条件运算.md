---
title: javascript基础(十)-条件运算
date: 2019-06-06 11:02:46
tags: js
categories: js
---

> 本文译自[ifelse](https://javascript.info/ifelse)

条件运算：if,"?"。
有时候，我们需要根据不同的情况采取不同的行为。
为此，我们可以使用if语句和条件运算符?，这也称为“问号”运算符。

## if
if语句计算一个条件，如果条件的结果为真，则执行一段代码。
```javascript
let year = prompt('ECMAScript-2015规范是哪一年发布的?', '');

if (year == 2015) alert( '你答对了！' );
```

上面例子中的条件是十分简单的等式校验，但条件可是很复杂。
如果我们想执行多条语句，就必须把代码块包在花括号里:
```javascript
if (year == 2015) {
  alert( "你答对了！" );
  alert( "你真聪明!" );
}
```

我们推荐在写if语句时，一直用花括号{}包裹代码块，即使只有一条语句。这将增加代码可读性。

## 布尔转换
if(…)语句计算括号中的表达式，并将结果转换为布尔值。
让我们复习下转换规则：
* 数字`0`，空字符串`""`,`null`,`undefined`和`NaN`会转成`false`。因此它们叫做假值。
* 其他值会转成`true`。所以它们叫做真值。

所以以下条件中的代码不会执行：
```javascript
if (0) { // 0 是假值
  ...
}
```

然而，下面这种条件中的代码永远会执行：
```javascript
if (1) { // 1 是真值
  ...
}
```

我们也可以给if条件传一个提前算好的布尔值：
```javascript
let cond = (year == 2015);

if (cond) {
  ...
}
```

## else
if 语句可能有一个else代码块，在if条件为false时执行：
```javascript
let year = prompt('ECMAScript-2015规范是哪一年发布的?', '');

if (year == 2015) {
  alert( '你猜对了!' );
} else {
  alert( '你怎么能答错呢?' ); // 除了2015的值
}
```

## else if
有时，我们想测试一个条件的几个变体。else if允许我们这样做。
```javascript
let year = prompt('ECMAScript-2015规范是哪一年发布的?', '');

if (year < 2015) {
  alert( '太早了...' );
} else if (year > 2015) {
  alert( '太晚了' );
} else {
  alert( '对了!' );
}
```

上面的代码中，JavaScript先检查`year < 2015`，如果不符合条件，再检查`year > 2015`，如果还是不符合，就显示最后一个alert。
可以有多个else if,最后的else不是必须的。

## 条件运算符?
有时，我们需要根据条件给变量赋值：
```javascript
let accessAllowed;
let age = prompt('你多大了?', '');

if (age > 18) {
  accessAllowed = true;
} else {
  accessAllowed = false;
}

alert(accessAllowed);
```

这样的条件可以有更短的写法。
操作符用问号?表示。有时它被称为“三元”或“三目”，因为操作符有三个操作数。它是JavaScript中唯一一个拥有这么多的操作符。
三目运算符语法：
```javascript
let result = condition ? value1 : value2;
```

`condition`如果是真值，返回value1，否则返回value2。
举个例子：
```javascript
let accessAllowed = (age > 18) ? true : false;
```

从技术上讲，我们可以省略`age > 18`的括号。问号操作符的优先级较低，因此它在比较>之后执行。
下面的例子和上面的效果一样：
```javascript
let accessAllowed = age > 18 ? true : false;
```

但是括号使代码可读性增强，所以我们推荐使用括号。

请注意：上面的例子可以不用三目，因为条件本身返回了`true/false`:
```javascript
let accessAllowed = age > 18;
```

## 多个?
一连串的问号运算符可以返回一个依赖于多个条件的值。
比如：
```javascript
let age = prompt('年龄?', 18);

let message = (age < 3) ? '你好, 宝贝!' :
  (age < 18) ? '你好!' :
  (age < 100) ? '您好!' :
  '多么稀有的年龄!';

alert( message );
```

第一眼可能很难看清楚发生了什么，但仔细看一下就会发现这只是一个普通的测试序列:
1、第一个问号检查`age < 3`是否成立；
2、如果成立，返回`'你好, 宝贝!'`。否则继续冒号后面的表达式，检查`age < 18`是否成立；
3、如果成立，返回`'你好!'`。否则继续冒号后面的表达式，检查`age < 100`是否成立；
3、如果成立，返回`'您好!'`。否则继续冒号后面的表达式，返回`'多么稀有的年龄!'`。

如果用`if...else`写，就像下面这样：
```javascript
if (age < 3) {
  message = '你好, 宝贝!';
} else if (age < 18) {
  message = '你好!';
} else if (age < 100) {
  message = '您好!';
} else {
  message = '多么稀有的年龄!';
}
```

## “?”的非传统用法
有时`?`可以代替`if`：
```javascript
let company = prompt('哪家公司创建了JavaScript?', '');

(company == 'Netscape') ?
   alert('对了!') : alert('错了.');
```

对于条件`company == 'Netscape'`，不管问号后面的第一个还是第二个表达式执行了，都会显示一个alert。

这里我们不把结果赋值给变量，而是根据条件执行不同的代码。

不过我们不推荐这样使用三目运算符。虽然比if写法简短，但可读性变差了。
下面使使用if的写法：
```javascript
let company = prompt('哪家公司创建了JavaScript?', '');

if (company == 'Netscape') {
  alert('对了!');
} else {
  alert('错了.');
}
```

我们从上往下阅读代码时，多行短代码比一行长代码更容易理解。

三目运算符的目的是根据条件返回一个值。请准确的使用它，如果要执行不同的代码，请用if。
