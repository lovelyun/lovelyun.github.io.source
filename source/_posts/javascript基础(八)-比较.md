---
title: javascript基础(八)-比较
date: 2019-06-04 16:29:53
tags: js
categories: js
---

> 本文译自[comparison](https://javascript.info/comparison)

从数学课本上，我们知道了很多比较操作：
* 大于/小于： a > b, a < b。
* 大于等于/小于等于： a >= b , a <= b。
* 等于： a == b(注意这里是2个等于号，一个等于号是赋值)。
* 不等于，数学符号是`≠`，不过JavaScript的写法是`!=`：a != b。

## 结果是布尔类型
和其他操作符一样，比较操作也有返回值，值是布尔类型。
例入：

```javascript
alert( 2 > 1 );  // true (对)
alert( 2 == 1 ); // false (错)
alert( 2 != 1 ); // true (对)
```

比较结果可以赋值给一个变量，就像其他任何值一样：

```javascript
let result = 5 > 4; // 比较的结果赋值给result
alert( result ); // true
```

## 字符串比较
看一个字符串是否比另一个大，JavaScript使用所谓的“字典”或“词典”顺序。
换句话说，字符串是逐个比较字符的。

```javascript
alert( 'Z' > 'A' ); // true
alert( 'Glow' > 'Glee' ); // true
alert( 'Bee' > 'Be' ); // true
```

比较两个字符串的算法很简单:
1、比较两个字符串的第一个字符。
2、如果第一个字符串的第一个字母比另一个的大（小），那第一个字符串就比第二个大（小）。
3、否则，如果两个字符串的第一个字符一样，就以相同的方式比较第二个字符。
4、重复直到有字符串到了末尾，
5、如果两个字符串长度一样，那么他们相等，否则长的那个更大。

上面的例子中，`'Z' > 'A'`在第1步中就得到了结果，“Grow”和“Glee”逐个字母的比较：
1、G和G一样。
2、l和l一样。
3、o比e大，这里比较结束，第一个字符串更大。

<div class="tip">
<b>不是一个真是的字典，而是Unicode顺序</b>
<p>上面给出的比较算法大致相当于字典或电话簿中使用的比较算法，但并不完全相同。</p>
<p>例如，大写字母“A”不等于小写字母“a”。哪个更大?小写字母“a”。为什么?因为小写字符在JavaScript使用的内部编码表(Unicode)中具有更大的索引。</p>
</div>

## 比较不同类型
当比较的值类型不同， JavaScript 就把值转成数字类型：

```javascript
alert( '2' > 1 ); // true, 字符串 '2' 变成数字 2
alert( '01' == 1 ); // true, 字符串 '01' 变成数字 1
```

如果是布尔类型，true会转成1,false转成0：
```javascript
alert( true == 1 ); // true
alert( false == 0 ); // true
```

### 一个有趣的结果
下面的情况同时成立：
* 两个值相等。
* 一个的布尔值是true而另一个的是false。
比如：
```javascript
let a = 0;
alert( Boolean(a) ); // false

let b = "0";
alert( Boolean(b) ); // true

alert(a == b); // true!
```

从JavaScript的角度来看，这个结果很正常。相等性检查使用数值转换转换值(因此“0”变为0)，而显式布尔转换使用另一组规则。

## 严格相等
常规的相等检查`==`有一个问题，它不能区分0和false:
```javascript
alert( 0 == false ); // true
```
空字符串也是这样：
```javascript
alert( '' == false ); // true
```

这是因为相等操作符`==`比较不同类型时先把值转成数字类型，空字符串，或者false，都变成了数字0.
如果我们要区分0和false该怎么做呢？
严格等于操作符`===`检查是否相等时不会做类型转换。
换句话说，如果a和b的类型不同，`a === b`会不尝试转换类型就立刻返回false。
同样的，有一个严格不等于`!==`对应`!=`。
严格等于操作符写起来长一点，但是能更明显的看到发生了什么，不容易出错。

## null和undefined的比较
我们看些边界情况：null和undefined做比较。
* 严格相等检查===
值不一样，因为他们的类型不一样。
```javascript
alert( null === undefined ); // false
```

* 非严格相等检查==
有一个特殊的规则。这两个人是“甜蜜的一对”:他们彼此平等(在==的意义上)，但没有任何其他的价值。
```javascript
alert( null == undefined ); // true
```

* 数学上的其他比较
数学上的其他比较`< > <= >=`,null/undefined会转成数字类型: null 变成 0, undefined 变成 NaN。

下面我们看一些比较有意思的比较，更重要的是以后我们自己要避免掉进这样的坑里。

1、null和0比较
```javascript
alert( null > 0 );  // (1) false
alert( null == 0 ); // (2) false
alert( null >= 0 ); // (3) true
```

从数学角度看，这很奇怪，最后一个结果声明“null大于或等于0”，因此在上面的比较中，它必须为真，但是它们都是假的。
原因是相等性检查`==`和比较`> < >= <=`工作方式不同。比较将null转换为数字，将其视为0。这就是为什么(3)null >= 0为真，(1)null > 0为假。
另一方面，等号检查`==`undefined 和 null的定义是这样的:不进行类型转换，它们彼此相等，并且不等于其他任何值。这就是为什么(2)null == 0是假的。

2、无与伦比的定义
`undefined`不应该和任何其他值比较：
```javascript
alert( undefined > 0 ); // false (1)
alert( undefined < 0 ); // false (2)
alert( undefined == 0 ); // false (3)
```
为什么它这么不喜欢0，老是false！
我们得到这个结果的原因是：
* 比较(1)和(2)返回false，因为undefined转换成了NaN，而NaN是一个特殊数值，它对所有的比较都返回false。
* 比较(3)返回false，因为undefined只等于null和undefined，再没有其他值

## 避免问题
我们为什么要复习这些例子?我们应该一直记住这些特性吗?好吧,其实不是。实际上，随着时间的推移，这些棘手的事情会逐渐变得熟悉，但有一个可靠的方法可以避免这些问题:
除了严格的等号`===`外，对`undefined/null`的比较都要格外小心。
对可能是`undefined/null`的值不使用`>= > < <=`来比较，除非你很清楚你在做什么。如果变量是这些值，就单独检查它们。

## 总结
* 比较操作符返回布尔值。
* 字符串比较时，逐个字符来根据Unicode编码顺序比较。
* 比较的值类型不同时，先自动转成数字类型（严格相等检查除外）。
* `undefined/null`等于`==`彼此，并且不等于任何其他值。
* 对可能是`undefined/null`的值不使用`>= > < <=`来比较，单独检查`undefined/null`更好。
