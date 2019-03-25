---
title: JavaScript引擎基础之内联缓存
date: 2019-03-25 16:55:51
tags: js译文
categories: js
---

形状背后的主要推动力是内联缓存（Inline Caches）或ICs的概念。ICs是让JavaScript快速运行的关键因素！JavaScript引擎使用ICs来记住去哪里寻找对象的属性，从而减少昂贵的查找次数。

下面这个函数getX接受一个对象并返回属性x：

```javascript
function getX(o) {
  return o.x;
}
```

如果我们在JSC中运行这个函数，它会生成以下字节码:
![ic-1](/img/ic-1.svg)

第一条命令`get_by_id`从第一个变量`arg1`中加载属性`x`，并把结果存到`loc0`中，第二条命令返回我们在`loc0`中存的内容。
JSC还将内联缓存嵌入`get_by_id`命令，该命令由两个未初始化的槽组成。
![ic-2](/img/ic-2.svg)

现在我们假设把对象`{ x: 'a' }`传入`getX`，正如我们上篇文章中所了解的，这个对象有一个带有属性x的形状，这个形状存储属性x的偏移量和值。当你第一次执行`getX`,`get_by_id`命令寻找x属性，然后发现值存在偏移为0的地方。
![ic-3](/img/ic-3.svg)

嵌入到`get_by_id`指令中的IC将记住找到的属性的形状和偏移：
![ic-4](/img/ic-4.svg)

后续运行种，IC只需要比较形状，如果一样，就直接从记忆偏移量处读取值，而且，如果JavaScript引擎遇到的对象的形状之前IC记录过，引擎就不会获取属性值，开销很大的属性寻找被直接跳过，这比每次都寻找属性明显快多了。

## 高效存储数组
数组是常见的存储类型，这些属性的值被称为数组元素，JavaScript引擎在默认情况下让数组索引属性可写、可枚举和可配置，并将数组元素与其他命名属性分开存储。

思考下面这个数组：

```javascript
const array = [
  '#jsconfeu',
];
```

JavaScript引擎存储数组长度length为1，然后指向含有offset和length属性的形状。
![array-shape](/img/array-shape.svg)

看起来和我们之前看到的一样，但是数组的value值存到哪里去了呢？
![array-elements](/img/array-elements.svg)

每个数组都有一个单独的元素支持存储，其中包含所有数组索引的属性值。JavaScript引擎不需要为数组元素存储任何属性属性，因为通常情况下数组都是可写、可枚举和可配置的。

异常情况下会发生什么呢？比如更改数组元素的属性值会怎样?

```javascript
// 请永远不要这么做
const array = Object.defineProperty(
  [],
  '0',
  {
    value: 'Oh noes!!1',
    writable: false,
    enumerable: false,
    configurable: false,
  }
);
```

上面的代码片段定义了一个名为“0”的属性(恰好是一个数组索引)，但是将其值设置为非默认值。
在这种情况下，JavaScript引擎将整个支持存储的元素表示为字典，将数组索引映射到属性值。
![array-dictionary-elements](/img/array-dictionary-elements.svg)

即使只有1个数组元素有非默认属性，整个数组的后备存储器进入这种缓慢而低效的模式。避免在数组索引上使用`Object.defineProperty`（我不知道你为什么要这么做。这似乎是一件奇怪的、无用的事情）。

## 小结
我们已经了解了JavaScript引擎如何存储对象和数组，形状和ICs又是如何优化对象和数组的常见操作。基于这些知识，我们发现了一些实用的JavaScript编码技巧，可以帮助提高性能:

* 始终以相同的方式初始化对象，这样它们就不会有不同的形状
* 不要打乱数组元素的默认值，这样可以有效地存储和操作它们

## 参考
* [JavaScript engine fundamentals: Shapes and Inline Caches · Mathias Bynen](https://mathiasbynens.be/notes/shapes-ics)
