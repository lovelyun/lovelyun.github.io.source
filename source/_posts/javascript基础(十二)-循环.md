---
title: javascript基础(十二)-循环
date: 2019-07-11 17:14:09
tags: js
categories: js
---

> 本文译自[while-for](https://javascript.info/while-for)

我们常常需要重复一些动作。
比如：根据商品列表一个一个的输出商品，或者只是从0到10运行同样的代码。

循环是多次重复相同代码的一种方式。

## while
while循环语法：
```javascript
while (condition) {
  // 代码
  // 叫做循环体
}
```
当`condition`是`true`时，循环体里的代码会执行。
比如，下面的循环在`i < 3`时输出`i`:
```javascript
let i = 0;
while (i < 3) { // 显示 0, 然后 1, 然后 2
  alert( i );
  i++;
}
```

循环体的一次执行称为迭代。上面例子中的循环进行了三次迭代。

如果上面例子中没有`i++`，循环会永远继续下去，不过，事实上，浏览器提供了停止这种循环的方法，在服务器端JavaScript中，我们可以终止进程。

循环条件`condition`可以是任何表达式或变量，而不仅仅是比较:while对条件求值并将其转换为布尔值。
比如，`while (i != 0)`更短的写法是`while (i)`：
```javascript
let i = 3;
while (i) { // 当i变成0，条件变成false,循环停止
  alert( i );
  i--;
}
```

注意：单行主体不需要花括号
```javascript
let i = 3;
while (i) alert(i--);
```

## do...while

```javascript
do {
  // loop body
} while (condition);
```
`do...while`循环会先执行循环体，然后检查条件，条件为真，就再次执行循环体。
举个例子：
```javascript
let i = 0;
do {
  alert( i );
  i++;
} while (i < 3);
```

这种循环只能在不管条件是否为真，循环体至少执行一次的时候使用。通常，`while(...){...}`是更好的选择。

## for
for循环是用的最多的循环。语法如下：
```javascript
for (begin; condition; step) {
  // ... loop body ...
}
```

看下面的例子，在i从0增加到3的时候循环alert(i)：
```javascript
for (let i = 0; i < 3; i++) { // 显示 0, 然后 1, 然后 2
  alert(i);
}
```

||||
| :--------: | :-----: | :-----: |
| begin |i=0|进入循环体后执行一次|
| condition |i < 3|每次进入循环前检查一下，如果为false，循环结束|
| step |i++|在每次迭代的主体之后，但在条件检查之前执行|
| body |alert(i)|条件为真的时候执行|

一般的循环算法是这样工作的:
```javascript
开始执行
→ (if condition → run body and run step)
→ (if condition → run body and run step)
→ (if condition → run body and run step)
→ ...
```

### 内联变量声明
这里，计数器变量`i`在循环中申明，叫做内联变量申明，这样的变量只在循环体内部可见：
```javascript
for (let i = 0; i < 3; i++) {
  alert(i); // 0, 1, 2
}
alert(i); // error, 没有这个变量
```
当然，我们如果不定义，可以使用已经存在的变量：
```javascript
let i = 0;

for (i = 0; i < 3; i++) { // 使用已经存在的变量
  alert(i); // 0, 1, 2
}

alert(i); // 3, 可见, 因为i在循环体外部申明
```

### 省略部分
for循环的任何一个部分都可以省略。
比如，我们可以省略begin，如果在循环开始的时候不需要做什么：
```javascript
let i = 0; // 我们已经申明i并赋值

for (; i < 3; i++) { // 不需要 "begin"
  alert( i ); // 0, 1, 2
}
```

我们也可以省略step部分：
```javascript
let i = 0;

for (; i < 3;) {
  alert( i++ );
}
```
这使循环和`while(i < 3)`一样。

我们也可以把3个部分都省略，写个无限循环：
```javascript
for (;;) {
  // 无限制重复
}
```

注意for循环的两个分号`;`必须有，否则会出现语法错误。

### 中断循环
通常，循环在他们的条件为false时中断。
不过我们可以使用`break`命令在任何时候强制中断。
比如，下面的循环询问用户一系列的数字，当没有输入数字时中断:
```javascript
let sum = 0;
while (true) {
  let value = +prompt("输入一个数字", '');
  if (!value) break; // (*)
  sum += value;
}
alert( '总和: ' + sum );
```
`(*)`行的break在没有输入或者取消输入时激活，使循环立刻停止，把控制权交给循环后的第一行`alert`。
`infinite loop + break as needed`组合非常适用于这样的情况:必须在循环的中间甚至在循环体的几个位置检查循环的条件，而不是在循环的开始或结束时检查。

### 继续下一个迭代
`continue`指令是轻量版的`break`,它不会停止整个循环。相反，它停止当前迭代并强制循环开始一个新的迭代(如果条件允许)。如果我们已经完成了当前的迭代，并且想继续下一个迭代，我们可以使用它。
下面的循环用`continue`输出奇数：
```javascript
for (let i = 0; i < 10; i++) {
  // 如果为真，跳过循环体剩余的部分
  if (i % 2 == 0) continue;
  alert(i); // 1, 然后 3, 5, 7, 9
}
```
对于偶数i，`continue`指令停止执行循环体，把控制权交给下一个迭代，所以alert只在奇数的时候执行。

`continue`指令可以减少嵌套，一个显示奇数的循环可以像下面这样：
```javascript
for (let i = 0; i < 10; i++) {
  if (i % 2) {
    alert( i );
  }
}
```

技术上讲，这和上一个例子效果一样，我们可以把代码用if包起来来代替`continue`。但副作用是多了一层嵌套，如果If中的代码超过几行，那么可能会降低整体可读性。

请注意，非表达式的语法结构不能与三元运算符一起使用。
比如下面这段代码：
```javascript
if (i > 5) {
  alert(i);
} else {
  continue;
}
```
我们用三目运算符重写：
```javascript
(i > 5) ? alert(i) : continue; // 这里不允许continue，它没有效果，这种代码会报语法错误。
```

## break/continue的labels标签
有时我们需要结束多层嵌套的循环。比如，下面的例子，我们对i和j循环：
```javascript
for (let i = 0; i < 3; i++) {
  for (let j = 0; j < 3; j++) {
    let input = prompt(`位置 (${i},${j})的值是：`, '');
    // 我想在这里中断循环，执行下面的Done该怎么办？
  }
}
alert('Done!');
```

你需要一个方式在用户取消输入的时候中断循环。
但是input后面的break指令只能中断内部循环。这还不够，这时就需要标签！
标签是循环前带有冒号的标识符:
```javascript
labelName: for (...) {
  ...
}
```

下面循环中的`break <labelName>`语句结束循环并走到标签处。
```javascript
outer: for (let i = 0; i < 3; i++) {
  for (let j = 0; j < 3; j++) {
    let input = prompt(`位置 (${i},${j})的值是：`, '');
    // 如果值为空或取消输入，就中断这2个循环
    if (!input) break outer; // (*)
    // 用输入值做点什么...
  }
}
alert('Done!');
```

在上面的代码中，`break outer`向上查找名为`outer`的标签，并跳出循环。
因此控件直接从(*)变为alert('Done!')。
我们也可以把标签移到单独的一行:
```javascript
outer:
for (let i = 0; i < 3; i++) { ... }
```

`continue`指令也可以使用标签，这时，代码执行跳转到标记循环的下一个迭代。

注意标签不是`gito`。
标签不允许我们跳转到代码中的任意位置。比如，下面的代码是不可能的：
```javascript
break label;  // 跳到 label? 不会.

label: for (...)
```

`break/continue`指令只有在label后面，并且在循环内部执行，才会有效果。

## 总结
我们学习了3中循环：
* `while`-每次迭代前检查迭代条件
* `do..while`-迭代后检查迭代条件
* `for (;;)`-在每次迭代之前检查迭代条件

`break`指令可以中断循环。
如果我们在当前循环里什么也不想做，只想去下一个循环，可以用`continue`指令。

`break/continue`支持循环之前的标签，标签只是用`break/continue`来跳出循环的一种方式。
