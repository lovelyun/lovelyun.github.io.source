---
title: javascript基础(五)-数据类型
date: 2019-05-24 17:19:38
tags: js
categories: js
---

> 本文译自[Data types](https://javascript.info/types)

JavaScript中的变量可以存储任何数据，同一个变量，可以一会儿是字符串，一会儿是数字：

```javascript
// 没有报错
let message = "hello";
message = 123456;
```

编程语言中，这叫做“动态类型”，代表有数据类型，但是变量不和类型绑定。
JavaScript有7种基础数据类型，下面我们将简单介绍一下，下个章节再详细讨论它们。

## 数字

```javascript
let n = 123;
n = 12.345;
```

数字类型同时代表整数和浮点数。
有很多数字操作符，比如： 乘`*`，除`/`，加`+`，减`-`等等。
除了一般的数字，所谓的“特殊数值”也属于这种数据类型：正无穷大（Infinity）、负无穷大（-Infinity）和NaN。

### Infinity
Infinity代表数学上的无穷大∞，它是一个大于所有数字的特殊值。我们可以通过除以0得到它：

```javascript
alert( 1 / 0 ); // Infinity
```

或者直接引用它：

```javascript
alert( Infinity ); // Infinity
```

### NaN
NaN代表计算错误。是错误的或未定义的数学运算的结果。比如：

```javascript
alert( "not a number" / 2 ); // NaN, 这种除法写法错误
```

NaN的任何操作结果都是NaN：

```javascript
alert( "not a number" / 2 + 5 ); // NaN
```

所以，如果在数学表达式任何一个地方有NaN，它将使整个结果变成NaN。
<div class="tip">
<b>数学运算是安全的</b>
JavaScript中数学运算是安全的，我们可以做任何事：除以0，把非数字的字符串当成数字等等。脚本不会因为致命错误中断。最坏情况下，我们会得到NaN。
</div>
特殊的数值正式属于“number”类型。当然，在大众认知中它们不是数字。
在[Number](https://javascript.info/number)章节中我们会学详细讨论数字类型。

## 字符串
JavaScript中的字符串必须用引号(有3种引号)包起来：

```javascript
let str = "Hello"; // 双引号
let str2 = '单引号也可以'; // 双引号
let phrase = `可以嵌入变量 ${str}`; // 倒引号
```

双引号和单引号都是简单引号，在javascript中它们俩没有区别。
倒引号是有扩展功能的引号。它们可以将变量和表达式用`${…}`包起来后放到字符串里面。举个例子：

```javascript
let name = "John";

// 嵌入变量
alert( `Hello, ${name}!` ); // Hello, John!

// 嵌入表达式
alert( `结果是 ${1 + 2}` ); // 结果是 3
```

`${…}`中的变量会被转换成字符串的一部分，我们可以在里面放任何值：一个叫`name`的变量，或者类似`1+2`这种算术表达式，甚至更复杂的东西。
请注意，只有倒引号可以这么嵌入，其他引号没有这个嵌入的功能。

```javascript
alert( "结果是 ${1 + 2}" ); // 结果是 ${1 + 2} (双引号什么也没做，直接输出了结果)
```

在[Strings](https://javascript.info/string)章节中我们会学详细讨论字符串类型。
<div class="tip">
<b>没有char类型</b>
<p>在一些语言中，有一个特殊的“character”类型给简单的字符使用，比如，在C语言和Java中是`char`类型。</p>
<p>在JavaScript中没有这种类型，只有一个`string`类型。一个字符串可能只有一个字符或者若干个字符。</p>
</div>

## 布尔
布尔类型只有2个值： true和false。
常用于存储是/否值：true表示“是，对”，false表示“否，错”。
举个例子：

```javascript
let nameFieldChecked = true; // yes, name field is checked
let ageFieldChecked = false; // no, age field is not checked
```

布尔值也可以是比较之后的结果：

```javascript
let isGreater = 4 > 1;

alert( isGreater ); // true (比较结果是 "yes")
```

在[booleans](https://javascript.info/logical-operators)章节中我们会学详细讨论布尔类型。

## null
null不属于上面的任何一个类型。它形成一个单独的类型，只包含空值null：
```javascript
let age = null;
```
在`JavaScript`中，`null`不是“对不存在对象的引用”，也不像其他语言中的“空指针”。
它仅仅是个表示“没有任何东西”、“空的”或者“未知值”的特殊值。
上面例子中的代码表示`age`是未知的，或者因为某些原因是空的。

## undefined
`undefined`就像null一样独自成为一个类型。表示值未定义。
如果一个变量申明过，但是没有赋值，那么它的值是`undefined`：
```javascript
let x;

alert(x); // 显示 "undefined"
```

技术上，可以给任何变量定义成`undefined`。
```javascript
let x = 123;

x = undefined;

alert(x); // "undefined"
```

……但是我们不推荐这么做，通常，我们会给“空的”或者“未知的”变量赋值为`null`，用`undefined`来判断一个变量是否赋值过。

## Objects和Symbols
`object`是个特殊的类型。
其他所有类型都被叫做简单类型，因为它们的值只能包含单一的东西（是个字符串，或者数字，或者其他的什么）。相反，`object`用于存储数据集合和更复杂的实体。在我们学习更过简单类型的知识后再详细了解`object`。

符号类型用于为对象创建惟一标识符，为了完整起见，我们必须在这里提到它，但是最好在对象之后研究这种类型。

## typeof操作符
`typeof`操作符返回变量的类型，当我们想处理不同类型的值，或者只想快速看下类型的时候，它十分有用。

它支持两种写法：
1、作为操作符：`typeof x`.
2、作为函数：`typeof(x)`。
换句话说，使用`typeof`的时候有没有括号都行。

`typeof x`返回类型名称的字符串：
```javascript
typeof undefined // "undefined"

typeof 0 // "number"

typeof true // "boolean"

typeof "foo" // "string"

typeof Symbol("id") // "symbol"

typeof Math // "object"  (1)

typeof null // "object"  (2)

typeof alert // "function"  (3)
```

后面3行可能需要更多的说明：
1、`Math`是提供数学运算的内置对象。
2、`typeof null`的结果是“object”，这是错误的。这是一种官方认可的typeof错误，为了兼容性而保留。当然，null不是对象。它是一个特殊的值，具有自己的独立类型。这是语言上的一个错误。
3、`typeof alert`的结果是“function”，因为`alert`是语言的一个函数，我们将在下一章学习函数，在那里我们将看到JavaScript中没有特殊的“函数”类型。函数属于`object`类型。但typeof对待它们的方式不同。形式上，这是不正确的，但在实践中非常方便。

## 总结
`JavaScript`中有7种基本数据类型：
* number: 任何数字，整数或浮点数。
* string: 字符串。一个字符串可能有1个或多个字符。没有单独的字符类型。
* boolean: 布尔值，true/false。
* null: 未知的值。只有一个`null`值的类型。
* undefined: 已申明变量。只有一个`undefined`值的类型。
* object: 存储更复杂的数据结构。
* symbol：唯一标识符。

`typeof`操作符可以看到变量类型。
* 两种用法： `typeof x`或者`typeof(x)`。
* 返回类型名称的字符串。比如“string”。
* `typeof null`返回“object”————这是语言本身的错误，事实上`null`不是`object`类型。

在下一章中，我们将集中讨论基本值，一旦熟悉了它们，我们将继续讨论对象。
