---
title: javascript运算符
date: 2018-10-17 17:03:45
tags: js
categories: js
---

## 名词解释

1、operator: `+`，`-`、`*`、`/`等等
2、operand: 运算对象，操作数，变量
3、unary: 只有1个operand的operator
4、binary： 有2个operand的operator

## operator `+`

通常情况下。`+`对数字求和。
但是，如果应用到`String`上，则是合并它们：

``` javascript
let s = "my" + "string";
console.log(s); // mystring
```

<div class="tip">
注意：只要有1个operand是`String`,其它的operand都会转成`String`。只有`+`会处理字符串，其它operator都会先把operand转成数字。
</div>

``` javascript
console.log( '1' + 2 ); // "12"
console.log( 2 + '1' ); // "21"

console.log( 2 - '1' ); // 1
console.log( '6' / '2' ); // 3
```

<div class="tip">
注意：运算从左向右执行,unary优先级高于binary。
</div>

``` javascript
console.log(2 + 2 + '1' ); // "41" and not "221"
```

当`+`应用到单个operand时，会把该operand转成Number。效果和Number(...)一样，但更简洁。

``` javascript
// No effect on numbers
let x = 1;
console.log( +x ); // 1

let y = -2;
console.log( +y ); // -2

// Converts non-numbers
console.log( +true ); // 1
console.log( +"" );   // 0
```

## assignment `=`

所有的operator都有返回值，`=`也是，x = value 把value赋值给 x 后返回value。

``` javascript
let a = 1;
let b = 2;

let c = 3 - (a = b + 1);

console.log( a ); // 3
console.log( c ); // 0
```

## Increment/decrement

Operator ++/-- 可以放在变量前面或后面。优先级比大多数operator都高。

* 放在变量前面时叫做 “prefix form”: `++counter` / `--counter`
* 放在变量后面时叫做 “postfix form”: `counter++` / `counter--`

prefix form和postfix form的相同点是：

* 都会使counter加1（或减1）

不同点是：

* prefix form返回新值,postfix form返回旧值。


``` javascript
counter++;      // counter = counter + 1的简写
counter--;      // counter = counter - 1的简写
```

``` javascript
let counter = 0;
console.log( ++counter ); // 1
```

``` javascript
let counter = 0;
console.log( counter++ ); // 0
```

<div class="tip">
Increment/decrement只能用在变量上。用在常量上，比如 5++ ，就会报错。
</div>

## typeof

* 两种用法: `typeof x` 或 `typeof(x)`
* 以字符串形式返回变量类型
* typeof null 返回 'object'是javascript的语言错误

``` javascript
typeof 0 // "number"
typeof "foo" // "string"
typeof true // "boolean"
typeof undefined // "undefined"
typeof Symbol("id") // "symbol"
typeof alert // "function"  (3)
typeof Math // "object"  (1)
typeof null // "object"  (2)
```

## 参考
* [Operators](https://javascript.info/operators)
* [Data types](https://javascript.info/types)
