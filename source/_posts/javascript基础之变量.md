---
title: javascript基础之变量
date: 2019-05-10 14:18:03
tags: js
categories: js
---

> 本文译自[Variables](https://javascript.info/variables)

大多数时候，一个JavaScript应用需要处理信息，这里有2个例子：
1、一个线上商店————这些信息可能包括正在销售的商品和购物车。
2、一个聊天应用————这些信息可能包括用户、聊天信息等等。
变量用来存储这些信息。

## 变量
变量是数据的“命名存储”。我们可以使用变量来存储商品、访问者和其他数据。
在JavaScript中创建变量，使用`let`关键词。
下面创建（申明或定义）了一个名称是"message"的变量：

```javascript
let message;
```

现在我们可以用赋值运算符`=`给message赋值。

```javascript
let message;

message = 'Hello'; // 存储字符串
```

字符串现在保存到与变量关联的内存区域。我们可以使用变量名访问它:

```javascript
let message;
message = 'Hello!';

alert(message); // 显示变量值
```

简化一下，我们可以将变量声明和赋值合并成一行:

```javascript
let message = 'Hello!'; // 定义变量并赋值

alert(message); // Hello!
```

我们还可以在一行申明多个变量：

```javascript
let user = 'John', age = 25, message = 'Hello';
```

这样看起来更简洁，但是我们不推荐这种写法。为了代码可读性，请为每个变量使用一行。
多行写法会稍微长一点，但更容易阅读：

```javascript
let user = 'John';
let age = 25;
let message = 'Hello';
```

一些人还会用这种多行形式定义多个变量：

```javascript
let user = 'John',
    age = 25,
    message = 'Hello';
```

甚至是“comma-first”（逗号前置）形式：

```javascript
let user = 'John'
  , age = 25
  , message = 'Hello';
```

技术上来讲，这些形式做的事情都一样，所有这只是个人品味和审美的问题。

在老的代码中，你可能会发现另外一个关键词`var`代替了`let`:

```javascript
var message = 'Hello';
```

关键词`var`和`let`几乎一样，它也声明了一个变量，但以一种稍微不同的“老式”方式。
let和var之间有细微的区别，但它们对我们来说还不重要。我们将在旧的“var”一章中详细介绍它们。

## 一个真实的类比
如果我们把“变量”想象成一个数据的“盒子”，上面有唯一的的标签名，那么我们就很容易理解“变量”的概念。
举个例子，变量“message”可以想象为一个名为“message”的盒子，里面装的值为“Hello!”：
![variable](/img/variable.png)
我们可以放任何值到盒子里。也可以不限次的改变里面的值：

```javascript
let message;

message = 'Hello!';

message = 'World!'; // 改变值

alert(message);
```

当值改变时，老的数据从变量中移除：
![variable-change](/img/variable-change.png)

我们还可以申请2个变量，并从一个变量复制值到另一个变量。

```javascript
let hello = 'Hello world!';

let message;

// 从变量hello复制值'Hello world'到变量message
message = hello;

// 现在两个变量值相同
alert(hello); // Hello world!
alert(message); // Hello world!
```

<div class="tip">
<p>函数式语言</p>
<p>注意到一个有意思的事情，有一些函数式语言，比如[Scala](https://www.scala-lang.org/)或[Erlang](http://www.erlang.org/)，会禁止改变变量的值。</p>
<p>在这些语言中，一旦变量值被存储了，值就永远在那里，如果我们想再存点别的东西，这些语言会强制我们创建一个新的盒子（申明一个新的变量），我们不能复用老的变量。</p>
虽然乍一看可能有点奇怪，但这些语言都具有相当强的开发能力。不仅如此，在并行计算等领域，这种限制也带来了一定的好处。学习这样一门语言(即使你不打算很快使用它)可以开阔思路。
</div>

## 变量命名
在JavaScript中命名变量有2个限制：
1、命名只能包含字母、数字，或者$ 和 _。
2、名称不能以数字开头。
类似下面这样：

```javascript
let userName;
let test123;
```

当变量名有多个单词时，一般用[驼峰式命名法](https://en.wikipedia.org/wiki/Camel_case)。也就是说:单词一个接一个，第一个字母大写:`myVeryLongName`.
有趣的是，美元符`$`和下划线`_`可以用在命名中，它们只是普通的符号，就像字母一样，没有任何特殊含义。
这些命名也是合法的：

```javascript
let $ = 1; // 用"$"申明一个变量
let _ = 2; // 现在又用 "_" 申明一个变量

alert($ + _); // 3
```

不合法的变量名举例：

```javascript
let 1a; // 不可以用数字开头

let my-name; // 连字符 '-' 不允许出现在命名中
```

### 区分大小写
区分大小写：`apple`和`AppLE`是两个不同的变量。

### 非拉丁字母
允许使用非拉丁字母，但不建议使用。比如:
```javascript
let имя = '...';
let 我 = '...';
```
技术上，这里不会报错，这种命名是合法的，但是在变量名中使用英语是一个国际传统。即使我们在写一个小脚本，它可能还有很长的路要走。其他国家的人可能需要一些时间来阅读它。

### 保留字
这里有一个[保留字列表](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Lexical_grammar#Keywords)，这些保留字不能作为变量名，因为语言自己要用。
比如：`let`, `class`, `return`, 和 `function`都是保留字。
下面的代码会报语法错误：
```javascript
let let = 5; // 不可以用"let"命名, error!
let return = 5; // 也不可以用"return"命名, error!
```

### 未指定"use strict"
通常，在使用变量前我们要先定义它，但在以前，在技术上可以仅通过赋值而不使用let创建变量，现在也可以这样，如果我们为了兼容老代码而不适用严格模式。

```javascript
// 注意: 本例没有"use strict"

num = 5; // 变量"num"不存在时会被创建

alert(num); // 5
```

这种写法不好，在严格模式下会报错：

```javascript
"use strict";

num = 5; // error: num is not defined
```

## 常量
要申明一个常量，就用`const`代替`let`:

```javascript
const myBirthday = '18.04.1982';
```

用`const`申明的变量叫做常量，它们的值不能改变，改变值会报错：

```javascript
const myBirthday = '18.04.1982';

myBirthday = '01.01.2001'; // error, can't reassign the constant!
```

当你确定这个变量永远不会改变，你可以用 `const` 申明来确保它不会变，并明确表示它不会改变的事实。

## 大写的常量
通常，人们会给要用到的难以记住的值用常量取一个别名，一般使用大写字母和下户线命名。
举个例子，让我们为十六进制颜色取些常量名：

```javascript
const COLOR_RED = "#F00";
const COLOR_GREEN = "#0F0";
const COLOR_BLUE = "#00F";
const COLOR_ORANGE = "#FF7F00";

// ...当我们需要使用上面的颜色时
let color = COLOR_ORANGE;
alert(color); // #FF7F00
```

这样做的好处是：
* `COLOR_ORANGE`比`"#FF7F00"`容易记的多了（好记）
* `"#FF7F00"`比`COLOR_ORANGE`容易打错多了（coding时不容易打错字）
* 看代码时，`COLOR_ORANGE`比`"#FF7F00"`语义性更强

那命名什么时候用大写字母，什么时候不用呢？下面我们区分一下。

常量只代表变量值不会改变，但是有的常量在执行前就已知，有的常量是运行时计算出来的，只是在定义后不再变化。比如：

```javascript
const pageLoadTime = /* 网页加载时长 */;
```

pageLoadTime的值在网页加载好之前是未知的，所以不用大写，但它任是常量，因为它在申明后值不再变化。
换句话说，全大写命名的常量只是难得书写的名称的别名。

## 正确命名
谈到变量，还有一件非常重要的事。
变量名应该具有清晰、明显的含义，来描述它存储的数据。
变量命名是编程最重要且复杂的技能之一。只要快速浏览一下变量名，就可以发现哪些代码是由初学者编写的，哪些是由经验丰富的开发人员编写的。

在一个实际项目中，大多数时间花在修改和扩展现有的代码上，而不是编写全新的代码。当我们在做其他事情一段时间后返回到某些代码时，好的变量名可以让我们更快读懂代码含义。
在声明变量之前，请花时间考虑变量的正确名称。这样做会给你带来丰厚的回报。
下面是一些好的命名规则：
* 使用可读的命名，比如`userName`、`shoppingCart`。
* 不要缩写或很短的命名，比如`a`、`b`、`c`，除非你真的知道自己在做什么。
* 命名尽可能具有描述性和简洁性。不好的命名，比如`data`、`value`，等于什么信息都没有，只有当代码的上下文特别清楚变量引用的数据或值时，才可以使用它们。
* 团队命名风格一致。如果一个用户被命名`user`，那么我们应该给相关的变量命名为`currentUser`或者`newUser`，而不是`currentVisitor`或`newManInTown`。

听起来很简单吧？确实，不过在实际情况中让变量名含义准确且简洁就不简单了。

<div>
复用还是创建？
<p>最后需要注意，有一些懒得程序员，比起定义一个新的变量，更会复用现有得变量。</p>
于是，他们的变量就像一个盒子，人们把不同的东西扔进去，而不改变盒子得标签。现在盒子里有什么?谁知道呢?我们需要靠近检查一下。
<p>这些程序员在定义变量上节省了一点点得内存，但是耗费了十倍以上的时间找bug</p>
记住：多一个变量是好的。
<p>JavaScript压缩和浏览器能够很好地优化代码，因此不会产生性能问题。为不同的值使用不同的变量甚至可以帮助引擎优化代码。</p>
</div>

## 总结
我们可以用`var`、`let`、`const`关键词来申明存储数据的变量。
* `let` - 是现代变量声明。Chrome (V8)的严格模式必须用let。
* `var` - 是老派的变量声明。通常情况下根本不用的，但我们会在旧的“var”一章中讨论与let的细微差别，以防你需要`var`。
* `const` - 和`let`一样，只不过变量值固定不变。

变量命名应该让我们容易知道里面存了什么，即要语义化。
