---
title: javascript基础(六)-类型转换
date: 2019-05-27 10:32:45
tags: js
categories: js
---

> 本文译自[type-conversions](https://javascript.info/type-conversions)

大多数时候，操作符和函数会自动把值转换到正确的类型。
比如：`alert`把要显示的值自动转换成字符串类型，数学操作符自动把值转成数字类型。
在某些情况下，我们还需要显式地将值转换为期望的类型。

<div class="tip">
本节只学习简单类型转换，先不讨论object类型。
</div>

## ToString
当我们需要值的字符串形式时，就会发生字符串转换。
举个例子，`alert(value)`为了显示值就会先把值转成字符串。
我们也可以使用`String(value)`函数将值转成字符串：

```javascript
let value = true;
alert(typeof value); // boolean

value = String(value); // now value is a string "true"
alert(typeof value); // string
```

字符串转换非常明显,`false`会变成"false"，`null`变成"null"等等。

## ToNumber
数值转换是在数学函数和表达式中自动进行的。
举个例子，对非数字值应用除法运算时：

```javascript
alert( "6" / "2" ); // 3, 字符串都转换成了数字
```

我们也可以使用`Number(value)`函数将值转成数字：

```javascript
let str = "123";
alert(typeof str); // string

let num = Number(str); // becomes a number 123

alert(typeof num); // number
```

当我们从基于字符串的源(如文本表单)读取值，但希望输入数字时，通常需要显式转换。
如果字符串不是一个合法的数值，运算结果会是`NaN`:

```javascript
let age = Number("任意字符串代替数字");

alert(age); // NaN, 字符串转换到数字的过程失败了
```

数字转换规则：

|值|转换结果|
| :--------: | :----- |
| `undefined` |`NaN`|
| `null	` |`0`|
| `true` 和 `false` |`1` 和 `0`|
| `string` |首位空格会被移除，移除后如果字符串是空的，结果就是`0`，否则会从字符串读取数字，报错后给出`NaN`的结果|

举个例子：
```javascript
alert( Number("   123   ") ); // 123
alert( Number("123z") );      // NaN (error reading a number at "z")
alert( Number(true) );        // 1
alert( Number(false) );       // 0
```

请注意，`null`和`undefined`这里的表现不一样，`null`的结果是`0`，`undefined`的结果是`NaN`.

加号“+”连接字符串
几乎所有的数学运算都把值转换成数字。一个值得注意的例外是加法。如果其中一个添加值是字符串，那么另一个也将转换为字符串。然后连接他们：
```javascript
alert( 1 + '2' ); // '12' (string to the right)
alert( '1' + 2 ); // '12' (string to the left)
```
只有当至少一个参数是字符串时才会发生这种情况。否则，值将转换为数字。

## ToBoolean
Boolean转换是最简单的一个。
它发生在逻辑操作中(稍后我们将满足条件测试和其他类似的事情)，但是也可以通过调用`Boolean(value)`显式地执行。

转换规则：
* 明显的空值，比如`0`,一个空字符串，`null`，`undefined`和`NaN`,会转成`false`。
* 其他的值都会转成`true`。

举个例子：
```javascript
alert( Boolean(1) ); // true
alert( Boolean(0) ); // false

alert( Boolean("hello") ); // true
alert( Boolean("") ); // false
```

但药注意，字符串"0"转成布尔值的结果是`true`。
有的语言（比如PHP）把"0"看成`false`，但在javascript中，一个非空字符串永远是`true`。
```javascript
alert( Boolean("0") ); // true
alert( Boolean(" ") ); // 空格, 也是 true (任何非空字符串都是true)
```

## 总结
三种广泛应用的类型转换：转成字符串、转成数字、转成布尔。
`ToString`————当我们输出一些东西时发生。可以使用`String(value)`执行。
`ToNumber`————数学操作中发生。可以使用`Number(value)`执行。
转换规则：

|值|转换结果|
| :--------: | :----- |
| `undefined` |`NaN`|
| `null	` |`0`|
| `true` / `false` |`1` / `0`|
| `string` |字符串被读取成数字，两端空格被忽略，空字符串结果是0，发生错误时结果是NaN|

`ToBoolean`————逻辑操作中发生。可以使用`Boolean(value)`执行。
转换规则：

|值|转换结果|
| :--------: | :----- |
| 0, null, undefined, NaN, "" |`false`|
| 任何其他值 |`true`|

上面的大部分规则都很好理解并记住，人们容易弄错的是：
* `undefined`转成数字时是`NaN`，不是`0`；
* "0"和只有空格的字符串，比如" "，转成布尔时是`true`。
