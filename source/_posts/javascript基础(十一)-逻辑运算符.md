---
title: javascript基础(十一)-逻辑运算符
date: 2019-06-21 11:06:17
tags: js
categories: js
---

> 本文译自[logical-operators](https://javascript.info/logical-operators)

JavaScript中有3种逻辑运算符：`||`（或）， `&&`（与），`!`（非）。

虽然它们的名字有逻辑二字，但是它们可以用于任何类型的值，不限于布尔，它们的结果也可以是任何类型。下面我们详细看看。

## ||（或）
或操作符由两条竖线表示：`||`。
传统的编程中，或或运算只能操作布尔值，如果表达式里面有一个值是true，结果就是true,否则是false。
而JavaScript中，或操作符更加强大，首先我们看看操作布尔值的情况：

```javascript
alert( true || true );   // true
alert( false || true );  // true
alert( true || false );  // true
alert( false || false ); // false
```

我们可以看到，除了两个操作数都是false外，结果都是true。

如果一个操作数不是布尔类型，将自动转成布尔。
比如，数字1会看成true,数字0是false:

```javascript
if (1 || 0) { // 等于( true || false )
  alert( 'truthy!' );
}
```

通常，`||`或操作符会用在if表达式的条件里面，看是否有条件是true。
举个例子：

```javascript
let hour = 9;

if (hour < 10 || hour > 18) {
  alert( '办公室关门了。' );
}
```

我们可以传入多个参数：

```javascript
let hour = 12;
let isWeekend = true;

if (hour < 10 || hour > 18 || isWeekend) {
  alert( '办公室关门了。' ); // 是周末
}
```

### 或运算查找第一个真值
上面描述的逻辑有些经典。现在，让我们引入JavaScript的“额外”特性。
扩展算法的工作原理如下，给定多个或值:

```javascript
result = value1 || value2 || value3;
```

或操作符做了下面的事情：
* 从左到有计算操作数
* 把每个操作数都转成布尔类型。如果是true，停止运算并返回操作数的原始值。
* 如果所有的操作数都运算了（比如都是false），就返回最后一个操作数。

返回值是原始值，没有类型转换。
换句话说，多个或操作，返回第一个真值，或者最后一个值（如果没发现真值）。
比如：
```javascript
alert( 1 || 0 ); // 1 (1 是真值)
alert( true || 'no matter what' ); // (true 是真值)

alert( null || 1 ); // 1 (1 是第一个真值)
alert( null || 0 || 1 ); // 1 (第一个真值)
alert( undefined || null || 0 ); // 0 (都是false, 返回最后一个值)
```
与传统的或操作相比，这导致了一些有趣的用法。
1、从多个变量或表达式中获取第一个真值
想象一下，我们有一个变量列表，变量可能有数据，可能是`null/undefined`,我们怎么找到第一个有数据的变量呢？
可以用或运算：
```javascript
let currentUser = null;
let defaultUser = "John";

let name = currentUser || defaultUser || "unnamed";

alert( name ); // John – 第一个真值
```
如果currentUser和defaultUser都是false，结果才是unnamed。

2、短路运算
操作数不仅是变量，也可以是表达式。或运算符从左到右执行，停在遇到的第一个真值，并返回这个真值。这个过程叫做短路运算。
当作为第二个参数给出的表达式具有类似变量赋值的副作用时，这一点就很明显了。
在下面的例子中，x没有被赋值:
```javascript
let x;

true || (x = 1);

alert(x); // undefined, 因为 (x = 1) 没有执行
```

如果第一个变量是false,`||`就执行第二个操作数，这样就运行了赋值代码：
```javascript
let x;

false || (x = 1);

alert(x); // 1
```
赋值是个简单的例子，可能不会执行。

我们看到，这种写法是if的一种简写，第一个操作符转成布尔，如果是false，第二个就不会执行。
大多数时候，用常规的if写法更便于阅读，不过有时这种短路写法很简便。

## &&（与）
或操作符由两个&表示：`&&`。
传统编程中，如果两个操作数都是true，与操作就返回true,否则返回false.
```javascript
alert( true && true );   // true
alert( false && true );  // false
alert( true && false );  // false
alert( false && false ); // false
```

if中使用`&&`的例子：
```javascript
let hour = 12;
let minute = 30;

if (hour == 12 && minute == 30) {
  alert( '时间是 12:30' );
}
```

和或一样，与也可以操作任何类型：
```javascript
if (1 && 0) { // 等于 true && false
  alert( "不执行，因为结果是false" );
}
```

### 与运算查找第一个假值
给定多个与值:

```javascript
result = value1 && value2 && value3;
```

或操作符做了下面的事情：
* 从左到有计算操作数。
* 把每个操作数都转成布尔类型。如果是false，停止运算并返回操作数的原始值。
* 如果所有的操作数都运算了（比如都是true），就返回最后一个操作数。

换句话说，多个与操作，返回第一个假值，或者最后一个值（如果没发现假值）。

上面的规则和或运算类似，区别是与运算返回第一个假值，而或运算返回第一个真值。

举例：
```javascript
// 如果第一个操作是是true,
// &&返回第二个操作数:
alert( 1 && 0 ); // 0
alert( 1 && 5 ); // 5

// 如果第一个操作是是false,
// &&返回第一个操作数，第二个被忽略
alert( null && 5 ); // null
alert( 0 && "no matter what" ); // 0

alert( 1 && 2 && null && 3 ); // null
alert( 1 && 2 && 3 ); // 3, 所有值都是true,返回最后一个
```

<div class="tip">
`&&`的优先级比`||`高
</div>

就像或运算，与运算有时也可以代替if:
```javascript
let x = 1;

(x > 0) && alert( '大于0!' );
```

`&&`右边的表达式只有左边的`(x > 0)`是true时才会执行。所以上面的写法是下面的简写：
```javascript
let x = 1;

if (x > 0) {
  alert( '大于0!' );
}
```

`&&`的写法更简洁，但是if的写法明显更便于阅读。
因此，我们建议使用每一种结构的目的:条件语句时用if,与运算的时候用`&&`。

## !（非）
布尔非操作符用感叹号表示:!。
语法非常简单：
```javascript
result = !value;
```
非操作符接受一个参数，做下面的事情：
1、将操作数转成布尔类似：true/false。
2、返回取反后的值。

举例：
```javascript
alert( !true ); // false
alert( !0 ); // true
```

`!!`有时用来把值转成布尔类型：
```javascript
alert( !!"非空字符串" ); // true
alert( !!null ); // false
```

第一个`!`把值转成布尔并返回取反后的值。第二个`!`再次取反，最后，我们有一个简单的值到布尔值的转换。
还有一个更详细的方法来做同样的事情————一个内置的布尔函数:
```javascript
alert( Boolean("非空字符串") ); // true
alert( Boolean(null) ); // false
```

非操作符`!`的优先级在逻辑运算符里是最高的，所以会在`&&`和`||`之前执行。
