---
title: javascript基础(十三)-switch
date: 2019-07-11 16:31:39
tags: js
categories: js
---

> 本文译自[switch](https://javascript.info/switch)

`switch `可以替换多个if，它提供了一种更具描述性的方法来比较具有多个变量的值。

## 语法
`switch`有一个或多个`case`块和一个默认选项。就像下面这样：
```javascript
switch(x) {
  case 'value1':  // if (x === 'value1')
    ...
    [break]
  case 'value2':  // if (x === 'value2')
    ...
    [break]
  default:
    ...
    [break]
}
```

* `x`的值被检查是否严格等于第一个case(value1)，然后是第二个case...
* 如果找到相等，switch将从对应的情况开始执行代码，直到最近的break(或者直到switch结束)。
* 如果没有相等的case,就只想default（如果有default的话）。

举个例子：
```javascript
let a = 2 + 2;
switch (a) {
  case 3:
    alert( 'Too small' );
    break;
  case 4:
    alert( 'Exactly!' );
    break;
  case 5:
    alert( 'Too large' );
    break;
  default:
    alert( "I don't know such values" );
}
```
switch开始比较a和case,首先a和3比较，匹配失败，然后和4比较，匹配成功，所以执行`case 4`里面的代码，直到最近的`break`出现。如果没有`break`,就会不做任何检查直接执行下一个case里面的代码。
就像下面这样：
```javascript
let a = 2 + 2;

switch (a) {
  case 3:
    alert( 'Too small' );
  case 4:
    alert( 'Exactly!' );
  case 5:
    alert( 'Too big' );
  default:
    alert( "I don't know such values" );
}
```

上面的例子顺序执行3个alert:
```javascript
alert( 'Exactly!' );
alert( 'Too big' );
alert( "I don't know such values" );
```

任何表达式都可以是`switch/case`的参数，比如：

```javascript
let a = "1";
let b = 0;

switch (+a) {
  case b + 1:
    alert("执行, 因为 +a 是 1, 等于 b+1");
    break;
  default:
    alert("不执行");
}
```

## 组合case
可以对共享相同代码的case进行分组。
比如，`case 3`和`case 5`要执行相同的代码：
```javascript
let a = 2 + 2;

switch (a) {
  case 4:
    alert('对!');
    break;
  case 3: // (*) 两个case组合
  case 5:
    alert('错!');
    break;
  default:
    alert('奇怪的结果.');
}
```

3和5都显示相同的信息。
组合case的能力是没有`break`给的，执行`case 3`，然后执行`case 5`,因为`case 3`里没有`break`。

## 参数类型
注意这里的case条件匹配检查是严格相等检查，数据类型也要一样。
比如：
```javascript
let arg = prompt("输入一个值?");
switch (arg) {
  case '0':
  case '1':
    alert( '1 或 0' );
    break;

  case '2':
    alert( '1' );
    break;

  case 3:
    alert( '永远不知晓!' );
    break;
  default:
    alert( '未知值' );
}
```

1、对于0、1，第一个alert执行。
2、对于2，第二个alert执行。
3、但是3，由于`prompt`的结果是字符串“3”，不严格等于数字3，所以`case 3`不会执行，会执行`default`。
