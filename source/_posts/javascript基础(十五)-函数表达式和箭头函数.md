---
title: javascript基础(十五)-函数表达式和箭头函数
date: 2019-07-12 11:06:35
tags: js
categories: js
---

> 本文译自[function-expressions-arrows](https://javascript.info/function-expressions-arrows)

我们之前了解过函数申明：
```javascript
function sayHi() {
  alert( "Hello" );
}
```

此外，还有另一种创建函数的语法，叫做函数表达式：
```javascript
let sayHi = function() {
  alert( "Hello" );
};
```
这里创建了函数并赋值给变量，不管函数是怎么定义的，它只是一个存在变量`sayHi`中的值。
我们也可以输出这个值：
```javascript
function sayHi() {
  alert( "Hello" );
}

alert( sayHi ); // 显示函数代码
```
注意上面的最后一行并没有运行函数，因为`sayHi`后面没有括号，有的语言只要写了函数名就会执行，但是JavaScript不是。
在JavaScript中，函数只是一个值，上面的代码显示用字符串函数的源码。
它也是个特殊值，因为我们可以用`sayHi()`调用它。但它任然只是一个值,我们可以和其他值一样用它。
我们可以复制一个函数给并一个变量：
```javascript
function sayHi() {   // (1) 创建
  alert( "Hello" );
}

let func = sayHi;    // (2) 复制

func(); // Hello     // (3) 运行通过!
sayHi(); // Hello    // 一样可以运行
```

上面的代码发生了这些事情：
1、(1)的函数申明创建了函数，并放在变量sayHi中。
2、(2)行把函数复制给变量func。
   注意sayHi后面没有括号，如果有，那么`func = sayHi()`会把调用函数sayHi()的结果赋值给func，而不是函数本身。
3、现在`sayHi()`和`func()`都可以调用函数。

我们也可以用函数表达式来创建函数sayHi:
```javascript
let sayHi = function() { ... };

let func = sayHi;
// ...
```

你可能会好奇为什么函数表达式结尾有个分号呢？而函数申明就没有。
```javascript
function sayHi() {
  // ...
}

let sayHi = function() {
  // ...
};
```
答案很简单：
* 代码块后面都不需要分号，比如`{ ... }`, `for { }`, `function f { }`等等。
* 函数表达式是作为一个值用在声明里面，就像这样`let sayHi = ...;`，不是代码块，申明语句后面一般推荐用分号结尾，不管申明的值是什么。所以分号和函数表达式本身没有任何关系，它只是终止了语句。

## 回调函数
让我们看更多的关于把函数当初变量传递和使用函数表达式的例子。
我们写一个有3个参数的函数`ask(question, yes, no)`:

* `question`: 问题内容
* `yes`: 如果答案是"yes"时要运行的函数
* `no`: 如果答案是"no"时要运行的函数

这个函数要问问题，根据答案看是执行`yes()`还是`no()`。
```javascript
function ask(question, yes, no) {
  if (confirm(question)) yes()
  else no();
}

function showOk() {
  alert( "你同意." );
}

function showCancel() {
  alert( "你取消执行." );
}

// 调用: 函数 showOk, showCancel 都作为传参传给函数ask
ask("你同意吗?", showOk, showCancel);
```

在探索如何写的更简洁之前，我们注意到在浏览器（有时在服务端）中这种函数相当常见，只是现实代码中会用更复杂的方式和用户交互，而不是简单的confirm。

ask函数的传参叫做回调函数，或者仅仅叫做回调。

我们传递了一个函数，并且希望它等一会儿再执行。上面的例子中，`showOk`是答案`yes`的回调，`showCancel`是答案`no`的回调。
我们可以用函数表达式的写法简化上面的代码：
```javascript
function ask(question, yes, no) {
  if (confirm(question)) yes()
  else no();
}

ask(
  "Do you agree?",
  function() { alert("You agreed."); },
  function() { alert("You canceled the execution."); }
);
```

这时，函数在ask(...)内部申明，它们没有名字，所以也称为匿名函数，这样的函数在ask外部无法访问，因为它们没有存在变量中。

普通的值比如字符串或数字，代表数据。
函数代表动作，我们可以在变量中传递函数，在任何时候调用它。

## 函数表达式vs函数申明
下面我们讨论下函数表达式和函数申明的主要区别。
首先是语法上，写法不同：
```javascript
// 函数申明
function sum(a, b) {
  return a + b;
}

// 函数表达式
let sum = function(a, b) {
  return a + b;
};
```

更细微的区别是JavaScript引擎何时创建函数。

<b>函数表达式是在执行到达时创建的，并且只能从那时起使用。</b>

只有代码执行到赋值的右侧`let sum = function…`,函数才创建并从此刻开始可用。函数申明就不同。

<b>函数申明可以在定义之前调用。</b>

比如，全局的函数申明在整个代码中可见，不管它在那里申明的。
因为JavaScript内部算法中，当准备运行脚本时，它首先查找全局函数声明并创建函数。我们可以把它看作是一个“初始化阶段”。在处理完所有函数声明之后，执行代码。所以它可以访问这些函数。

比如：
```javascript
sayHi("John"); // Hello, John

function sayHi(name) {
  alert( `Hello, ${name}` );
}
```
函数申明写法的`sayHi`在JavaScript准备运行时创建，并全局可见。如果换成函数表达式就不行：
```javascript
sayHi("John"); // error!

let sayHi = function(name) {  // (*)
  alert( `Hello, ${name}` );
};
```
函数表达式写法时，函数只在执行到表达式的赋值时才创建。上面的例子中的(*)行，太晚了。

严格模式中，在代码块中的函数申明在这个块中可见，块外面不可见。

怎么选择函数申明还是函数表达式呢？
一个简要的规则，当我们需要写一个函数，首选函数表达式，因为它写法更自由，在它申明前后都可以调用。
而且可读性更强，`function f(…) {…}`比`let f = function(…) {…}`更容易阅读。
但是如果一个函数声明由于某种原因不适合我们，或者我们需要一个条件声明，那么应该使用函数表达式。

## 箭头函数
还有一个非常简单和简洁的语法用于创建函数，它通常比函数表达式更好。它被称为“箭头函数”，因为它看起来像这样:
```javascript
let func = (arg1, arg2, ...argN) => expression
```

和下面的写法含义一样：
```javascript
let func = function(arg1, arg2, ...argN) {
  return expression;
};
```

还有更为简洁的写法。我们看下面的例子：
```javascript
let sum = (a, b) => a + b;

/* 箭头函数比下面这种写法更简洁:

let sum = function(a, b) {
  return a + b;
};
*/

alert( sum(1, 2) ); // 3
```

如果只有一个传参，那括号也可以省略，更加简洁了：
```javascript
// 下面两个写法意思一样
// let double = function(n) { return n * 2 }
let double = n => n * 2;

alert( double(3) ); // 6
```

如果没有传参，就必须有一个空括号：
```javascript
let sayHi = () => alert("Hello!");

sayHi();
```

当箭头函数里有多行代码时，需要用花括号包起来：
```javascript
let sum = (a, b) => {
  let result = a + b;
  return result;
};

alert( sum(1, 2) ); // 3
```

箭头函数还有更多有意思的特性，可以看[这里](https://javascript.info/arrow-functions)

## 总结
* 函数也是值，可以赋值，复制或在任何地方申明。
* 独立申明语句定义的函数，叫做函数申明。
* 表达式中定义的函数叫做函数表达式。
* 函数声明在代码块执行之前处理。它们在代码块可见。
* 函数表达式是在执行到赋值操作时创建的。

当我们需要写一个函数时，大多数时候函数表达式的写法更好，因为它在函数定义之前可见，这样代码写法更灵活，更具可读性。

所以我们只在函数申明不合适的时候用函数表达式的写法。

箭头函数对于一行程序很方便。它们有两种写法:
1、没有花括号：`(...args) => expression`-右侧的expression，函数执行expression并返回它的结果。
2、有花括号：`(...args) => { body }`-花括号让我们可以在body里写多行代码，但是我们需要一个显式的return来返回一些东西。
