---
title: JavaScript引擎基础之形状
date: 2019-03-11 10:50:56
tags: js译文
categories: js
---

本文描述了所有JavaScript引擎共有的一些关键基础，不仅仅是作者所研究的V8引擎。作为JavaScript开发者，深入理解JavaScript引擎的工作方式，会帮助你推断代码的性能。

## JavaScript引擎管道

这一切都从您编写的JavaScript代码开始，JavaScript引擎解析源码，将其转换成`抽象语法树`（Abstract Syntax Tree，简称AST），基于AST,解释器将其变成字节码。此时，引擎实际上正在运行JavaScript代码。

![js-engine-pipeline](/img/js-engine-pipeline.svg)
<div class="tip">
<li>JavaScript source code: JavaScript源码</li>
<li>parser: 解析器</li>
<li>Abstract Syntax Tree： 抽象语法树，简称AST</li>
<li>interpreter: 解释器</li>
<li>bytecode: 字节码</li>
<li>optimize: 优化</li>
<li>optimizing compiler: 优化编译器</li>
<li>optimized code: 优化后的代码</li>
<li>deoptimize: 反优化</li>
<li>profiling data: 分析数据</li>
</div>

为了让它运行的更快，字节码可以和分析数据一起送到优化编译器，优化编译器根据它的分析数据做出某些假设，然后生成高度优化的机器码。如果在某一时刻其中一个假设被证明是错的，那么优化编译器就会取消优化并返回解释器。

## JavaScript引擎的解释/编译器管道（Interpreter/compiler）
现在，让我们放大这个管道中实际运行JavaScript代码的部分。比如代码在哪里得到解释和优化，以及主流JavaScript引擎之间的一些差异。
通常，有一个包含解释器和优化编译器的管道，解释器快速生成未经过优化的字节码，而优化编译器需要更长的时间，但最终生成高度优化的机器码。

优化编译器：
![interpreter-optimizing-compiler](/img/interpreter-optimizing-compiler.svg)

这个通用管道几乎和V8(Chrome和Node中使用的JavaScript引擎)的工作原理一样。

V8的优化编译器：
![interpreter-optimizing-compiler-v8](/img/interpreter-optimizing-compiler-v8.svg)

<div class="tip">
<li>Ignition: 点火器</li>
<li>TurboFan: 风扇</li>
</div>

V8里的解释器叫点火器，负责生成和执行字节码。当它运行字节码时，会收集分析数据（后面将会用来加快执行速度），当某个函数变热，比如经常执行，它的字节码和分析数据就会传到风扇（我们的优化编译器），然后基于分析数据来生成高度优化的机器码。

SpiderMonkey（Firefox和SpiderNode中使用的Mozilla JavaScript引擎），会有一点不同。它有两个而不是一个优化编译器。解释器优化到基线编译器里面，基线编译器会生成一些优化的代码。结合运行代码时收集的分析数据，IonMonkey编译器可以生成高度优化的代码。如果假设的优化失败了，IonMonkey返回到基线代码。

SpiderMonkey的优化编译器：
![interpreter-optimizing-compiler-spidermonkey](/img/interpreter-optimizing-compiler-spidermonkey.svg)
<div class="tip">
<li>Baseline: 基线</li>
</div>

Chakra(在Edge和Node-ChakraCore中使用的微软的JavaScript引擎)使用了类似的两个优化编译器。解释器优化成SimpleJIT (JIT代表即时编译器)，生成优化的代码。与分析数据相结合，FullJIT生成更优化的代码。
Chakra的优化编译器：
![interpreter-optimizing-compiler-chakra](/img/interpreter-optimizing-compiler-chakra.svg)

JavaScriptCore(缩写为JSC，苹果的JavaScript引擎，用于Safari和React Native)，使用三种不同的优化编译器将其发挥到极致。底层解释器LLInt优化到基线编译器，然后基线编译器优化到DFG(Data Flow Graph)编译器，DFG又优化到FTL((Faster Than Light)编译器。
JavaScriptCore的优化编译器：
![interpreter-optimizing-compiler-jsc](/img/interpreter-optimizing-compiler-jsc.svg)

为什么有些引擎比其他引擎有更多的优化编译器?
一切都是关于权衡的。解释器可以快速生成字节码，但是字节码通常不是很高效。另一方面，优化编译器需要更长的时间，但最终会生成更高效的机器码。
这是快速让代码运行（解释器）和慢一点让代码运行，但最终让代码以最优性能的方式运行（优化编译器）之间的权衡。
一些引擎选择添加具有不同时间或效率特性的多个优化编译器，以额外的复杂性为代价对这些权衡进行更细致的控制。另一个权衡与内存使用有关。有关这方面的详细信息，请参阅我们的[后续文章](!https://mathiasbynens.be/notes/prototypes#tradeoffs)。

上面我们主要描述了不同的JavaScript引擎的解释器和优化编译器管道的差异，但是除了这些差异，在高层面上看，所有JavaScript引擎都具有相同的体系结构:有一个解析器和某种解释器/编译器管道。

## JavaScript的对象模型
让我们通过放大某些方面的实现来看看JavaScript引擎还有什么共同之处。比如，JavaScript引擎如何实现JavaScript对象模型，以及它们使用哪些技巧来加速访问JavaScript对象上的属性，事实上，所有主流引擎都实现了非常类似的功能。
ECMAScript规范定义对象是字符串键映射到属性值的字典。
![object-model](/img/object-model.svg)

除了`[[Value]]`本身外，规范还定义了这些属性：
* `[[Writable]]`：决定是否可以将属性重写
* `[[Enumerable]]`：决定属性是否出现在`for-in`循环中（是否可枚举）
* `[[Configurable]]`：决定是否可以将属性删除

双方括号`[[]]`看起来很时髦，但这只是规范表示不直接暴露给JavaScript的属性的方式。但您仍然可以在JavaScript中使用`Object.getOwnPropertyDescriptor`获得任何给定对象的这些值。

```javascript
const object = { foo: 42 };
Object.getOwnPropertyDescriptor(object, 'foo');
// → { value: 42, writable: true, enumerable: true, configurable: true }
```

这就是JavaScript如何定义对象的，那数组呢？
您可以将数组看作对象的特殊情况，一个区别是数组对数组索引有特殊的处理。数组索引是ECMAScript规范中的一个特殊术语。在JavaScript中数组长度限制在2³²−1以内，数组索引是该限制内任何有效的索引，比如，任何0到2³²−2之间的整数。

另一个不同之处在于数组还有一个神奇的长度属性。

```javascript
const array = ['a', 'b'];
array.length; // → 2
array[2] = 'c';
array.length; // → 3
```

上面的例子中，数组在创建的时候有一个值为2的length，然后我们给第2项赋值，length就自动更新了。

JavaScript定义数组类似于对象。例如，包括数组索引在内的所有键都显式地表示为字符串。数组中的第一个元素存储在键“0”下。
![array-1](/img/array-1.svg)

`length`属性只是另一个不可枚举和不可删除的属性，一旦元素被添加到数组中，JavaScript将自动更新`length`属性的`[[Value]]`值。总之，数组的表现和对象非常相似。
![array-2](/img/array-2.svg)

## 优化属性访问
现在我们知道在JavaScript中对象是怎么定义的了，让我们深入了解JavaScript引擎如何高效地处理对象。
JavaScript中访问属性是目前最常见的操作。对于JavaScript引擎来说，快速访问属性是至关重要的。

```javascript
const object = {
 foo: 'bar',
 baz: 'qux',
};

// 这里我们访问了`object`的`foo`属性
doSomething(object.foo);
```

## 形状
在JavaScript中，很多对象有相同的key是很常见的，这样的对象有相同的形状。

```javascript
const object1 = { x: 1, y: 2 };
const object2 = { x: 3, y: 4 };
// `object1`和`object2`有相同的形状
```

形状相同的对象获取相同的属性值也很常见：

```javascript
function logX(object) {
  console.log(object.x);
}

const object1 = { x: 1, y: 2 };
const object2 = { x: 3, y: 4 };

logX(object1);
logX(object2);
```

考虑到这一点，JavaScript引擎可以根据对象的形状优化对象属性访问。下面是它的工作原理。
让我们假设有一个对象，有`x`和`y`属性，它使用了我们前面讨论过的字典数据结构：它包含key的字符串，它们指向各自的值。
![object-model](/img/object-model1.svg)

如果你访问一个属性，比如`object.y`，JavaScript引擎在`JSObject`中查找键`y`,然后加载相应的属性值，最后返回`[[value]]`。
但是这些属性值在内存中是存在哪里呢？我们可以把他们存为`JSObject`的一部分吗？如果我们假设以后会看到更多具有这种形状的对象，那么将包含键值对的完整字典存储在`JSObject`本身是很浪费的，因为所有具有相同形状的对象都重复使用属性名。这是大量重复和不必要的内存使用。作为一种优化，引擎单独存储对象的形状。
![shape-1](/img/shape-1.svg)

这个形状Shape包含所有的键和值，除了`[[value]]`,在Shape中用值的偏移量`offset`代替`JSobject`中的`value`，这样JavaScript就知道去哪里找到`value`，每个具有相同形状的`JSObject`都指向这个形状实例，现在，每个`JSObject`只需要存储这个对象特有的值。
![shape-2](/img/shape-2.svg)

这个好处在有大量对象的时候变得明显，不管有多少对象，只要它们形状相同，我们只需要存储一次形状和属性。

所有JavaScript引擎都使用形状作为优化手段，但它们并不都称它们为形状:
* 学术论文称它们为隐藏类（Hidden Classes）
* V8称它们为映射（Maps）
* JavaScriptCore称它们为结构（Structures）
* SpiderMonkey称它们为形状（Shapes）

在本文中，我们将继续使用形状这个术语。

## 转换链和树
如果你有一个具有特定形状的对象，但是你给它添加了一个属性，会发生什么?JavaScript引擎如何找到新形状?

```javascript
const object = {};
object.x = 5;
object.y = 6;
```

这些形状在JavaScript引擎中形成所谓的转换链。这里有一个例子:
![shape-chain](/img/shape-chain1.svg)

这个对象一开始没有任何属性，所以它指向一个空的形状，下一个状态中，这个对象增加了x为5的属性，所以JavaScript引擎将其指向一个新的形状，这个形状有x属性，值5在第一个偏移量0处添加到JSObject，再下一个状态增加y为6的属性，所以JavaScript引擎将其指向又一个新的形状，这个形状包含x和y属性，并将值6追加到JSObject(偏移量为1)。

<div class="tip">
属性添加的顺序影响形状,例如`{ x: 4, y: 5 }`的形状和`{ y: 5, x: 4 }`不同
</div>

我们甚至不需要为每个形状存储完整的属性表，每个形状只需要知道它引入的新属性。
在本例中，我们不必将关于`x`的信息存储在最后一个形状中，因为它可以在前面的链找到。为了实现这一功能，每个形状都链接着上一个形状。
![shape-chain-2](/img/shape-chain-2.svg)

如果你JavaScript代码中写`o.x`，JavaScript引擎通过遍历转换链查找属性`x`，直到找到引入属性`x`的形状。
但是如果没有办法创建一个转换链呢?例如，如果您有两个空对象，并且为每个空对象添加了不同的属性，该怎么办?

```javascript
const object1 = {};
object1.x = 5;
const object2 = {};
object2.y = 6;
```

在这种情况下，我们必须用分支，而不是链，我们最终得到一个转换树:
![shape-tree](/img/shape-tree.svg)

这里我们创建了空对象a，然后给a增加x属性，最终我们得到只有一个`value`的`JSObject`和2个形状：一个空形状，一个只有属性x的形状。
第二个例子一开始也只有一个空对象b，然后增加属性y。最终我们得到两个转换链和三个形状。

这意味着我们总是从空的形状开始？不一定。
引擎对已经包含属性的对象应用了一些优化。假设我们从空对象开始添加x，或者有一个已经包含x的对象：

```javascript
const object1 = {};
object1.x = 5;
const object2 = { x: 6 };
```

在第一个例子中，我们一开始是空的形状，然后链接到有x属性的形状，就像我们上文看到的那样；在`object2`的例子中，直接生成有x的对象，而不是从一个空对象开始并进行转换。
![empty-shape-bypass](/img/empty-shape-bypass.svg)

包含属性x的对象从包含x的形状开始，有效地跳过了空形状。这就是V8和SpiderMonkey所做的。这种优化缩短了转换链，使从字面构造对象更加有效。

下面的例子是有x,y和z的3D坐标对象。

```javascript
const point = {};
point.x = 4;
point.y = 5;
point.z = 6;
```

如前所述，这将在内存中创建有3个形状的对象(不包括空形状)。读取对象上的x，比如你在代码中写`point.x`，JavaScript引擎需要遵循链接列表：它从底部的形状开始，然后逐渐上升到顶部引入x的形状。
![shapetable-1](/img/shapetable-1.svg)

如果我们经常这样做，将会非常慢，尤其是当对象有很多属性的时候。找到该属性的时间是O(n)，也就是说，对象的属性数量是线性的。为了加快搜索属性的速度，JavaScript引擎添加了叫形状表的数据结构。这个形状表是一个字典，将属性键映射到引入属性的各个形状。
![shapetable-2](/img/shapetable-2.svg)

等一下，现在我们回到了字典查找……这是我们开始添加形状之前的位置!那么我们为什么还要为形状而烦恼呢？
这就是形状可以用内联缓存优化的原因(内联缓存见后续文章)。

## 参考
* [JavaScript engine fundamentals: Shapes and Inline Caches · Mathias Bynen](https://mathiasbynens.be/notes/shapes-ics)
