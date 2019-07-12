---
title: javascript基础(十四)-函数
date: 2019-07-11 16:31:40
tags: js
categories: js
---

> 本文译自[function-basics](https://javascript.info/function-basics)

我们经常需要在脚本的许多地方执行类似的操作。比如，在用户登录、登出或者其他时候，我们需要显示一个漂亮的信息。
函数是程序的主要“封装模块”。它们允许代码被多次调用而不重复。
我们已经见过封装好的函数，比如`alert(message)`、`prompt(message, default)`、`confirm(question)`。其实我们也可以自己创建函数。

## 函数申明

```javascript
function showMessage() {
  alert( '大家好!' );
}
```
首先是`function`关键字，然后是函数名，然后括号里是一系列参数，最后是花括号里的代码，也称为函数体。
可以通过函数名调用函数：`showMessage()`。

举个例子：
```javascript
function showMessage() {
  alert( '大家好!' );
}

showMessage();
showMessage();
```

`showMessage()`会执行`showMessage`函数体的代码，这里我们会看到2次“大家好！”。
这个例子很好的演示了函数的一个主要功能：避免重复代码。
如果我们需要更改消息或显示消息的方式，只需在一个地方修改代码就足够了:输出消息的函数。

## 内部变量
函数体内部申明的变量只在函数内部可见。
举个例子：
```javascript
function showMessage() {
  let message = "Hello, I'm JavaScript!"; // 内部变量
  alert( message );
}
showMessage(); // Hello, I'm JavaScript!
alert( message ); // <-- Error! message变量只对函数showMessage可见
```
## 外部变量
函数可以访问函数体外部的变量。
比如：
```javascript
let userName = 'John';
function showMessage() {
  let message = 'Hello, ' + userName;
  alert(message);
}
showMessage(); // Hello, John
```

函数不仅可以访问外部变量，还可以改变外部变量：
```javascript
let userName = 'John';
function showMessage() {
  userName = "Bob"; // (1) 改变外部变量
  let message = 'Hello, ' + userName;
  alert(message);
}
alert( userName ); // John 调用函数前
showMessage();
alert( userName ); // Bob, 值被函数更改了
```

外部变量只有在没有内部变量的时候会用到。如果内部有同名变量，内部变量会覆盖外部变量。
比如下面的代码，函数用内部的`userName`，外部的`userName`被忽略。
```javascript
let userName = 'John';
function showMessage() {
  let userName = "Bob"; // 申明内部变量
  let message = 'Hello, ' + userName; // Bob
  alert(message);
}
showMessage();
alert( userName ); // John, 没有变, 函数没有碰外部变量
```

函数外面申明的变量，比如上面代码中外面申明的`userName`，叫做全局变量。
全部变量对所有函数可见（除非函数内部有同名的局部变量）。
最好少用全局变量，好的代码只有很少甚至没有全局变量，大部分变量都在函数内部。不过有时候，全局变量可以用来存项目级别的数据。

## 参数
我们可以通过参数给函数传递任意值。（也叫做函数arguments）
下面的例子中，函数有`from`和`text`2个参数：
```javascript
function showMessage(from, text) { // arguments: from, text
  alert(from + ': ' + text);
}

showMessage('小安', '你好!'); // 安: 你好! (*)
showMessage('小安', "怎么了?"); // 安: 怎么了? (**)
```

(*)和(**)调用函数时，传参会复制给局部变量`from`和`text`给函数使用。
下面这个例子，变量from传给函数，函数内部改变了from，但是改变对外部不可见，因为函数内部得到的时复制过来的值：
```javascript
function showMessage(from, text) {
  from = '*' + from + '*'; // 让"from"更好看
  alert( from + ': ' + text );
}
let from = "小安";
showMessage(from, "你好"); // *小安*: 你好
// from的值没变，函数只改变了内部复制后的变量
alert( from ); // 小安
```

## 默认值
如果没有传参数，参数就是`undefined`。
比如函数`showMessage(from, text)`，调用时可以只传1个参数`showMessage("Ann")`。
这没有错，会输出`小安: undefined`，由于没有传text参数，text被赋值undefined。
我们可以给text一个默认值：
```javascript
function showMessage(from, text = "没有传text") {
  alert( from + ": " + text );
}

showMessage("小安"); // 小安: 没有传text
```

这里“没有传text”是个字符串，但它可以是很复杂的表达式，比如：
```javascript
function showMessage(from, text = anotherFunction()) {
  // anotherFunction() 只有在没有传text时执行
  // 它的结果赋值给text
}
```

注意，在JavaScript中，每次没有传参时，都会计算默认值。比如上面的例子，每次没有传text，`anotherFunction()`都会执行，这和其他语言比如Python不一样，Python只在第一次编译时执行计算一次默认值。

### 老式写法
由于旧版本的JavaScript不支持默认参数，所以在老的代码中可以看到另一种写法，直接检查参数是否等于`undefined`:
```javascript
function showMessage(from, text) {
  if (text === undefined) {
    text = '没有传text';
  }
  alert( from + ": " + text );
}
```

或者用`||`选择器：
```javascript
function showMessage(from, text) {
  text = text || '没有传text';
  ...
}
```

## 返回值
函数可以返回一个值到调用代码中。
最简单的例子是两数求和：
```javascript
function sum(a, b) {
  return a + b;
}

let result = sum(1, 2);
alert( result ); // 3
```

`return`指令可以在函数的任何地方，当代码执行到return,就中断函数，并把值返回给调用的代码。

一个函数里可能有多个return,比如：
```javascript
function checkAge(age) {
  if (age > 18) {
    return true;
  } else {
    return confirm('你有父母的允许吗?');
  }
}

let age = prompt('你多大了?', 18);

if ( checkAge(age) ) {
  alert( '可以进入' );
} else {
  alert( '不可进入' );
}
```

可以使用没有值的return。这时函数立即退出：
```javascript
function showMovie(age) {
  if ( !checkAge(age) ) {
    return;
  }

  alert( "给你看视频" ); // (*)
  // ...
}
```
上面的代码中，如果`checkAge(age)`返回了false，`showMovie`函数不会执行alert。

如果函数没有返回值，或没有return,等于return undefined。
```javascript
function doNothing1() { /* empty */ }
alert( doNothing1() === undefined ); // true

function doNothing2() {
  return;
}
alert( doNothing2() === undefined ); // true
```

在return和返回值之间不要换行。
返回的表达式很长时，很容易把它放在单独的一行，就像这样:
```javascript
return
  (some + long + expression + or + whatever * f(a) + f(b))
```

这样将不会像预想的那样，因为JavaScript会假定return后面有分号，上面的代码和下面的效果一样：
```javascript
return;
  (some + long + expression + or + whatever * f(a) + f(b))
```

所以，这样做很明显会没有返回值。我们要把返回值和return写在同一行。

## 函数命名
函数都是动作，所以它们的命名大多是动词，它应该尽可能简短、准确，并描述函数的功能，以便阅读代码的人了解函数的功能。
以一个模糊地描述动作的前缀开始函数是一种普遍的做法。必须在团队内部就前缀的含义达成一致。比如“show”开始的函数通常是要显示什么。
比如这些函数名前缀：
* "get..."-返回一个值
* "calc..."-计算什么
* "creat.."-创建什么
* "check..."-检查什么然后返回布尔值，等等

有了前缀，只需看一下函数名，就可以了解它做了什么工作以及返回了什么类型的值。

注意，一个函数只做一件事。
一个函数应该完全按照它的名称所说的那样做，仅此而已。两个独立的操作通常需要两个函数，即使它们通常一起调用(在这种情况下，我们可以使用第三个函数来调用这两个操作)。
下面是几个反例:
* `getAge`-如果用alert显示了age就很不好，它应该只返回age.
* `createForm`-如果把form添加到document中就很不好了，它应该只是创建form并返回它。
* `checkPermission`-如果显示了`可以/不可 进入`提示就不好了，它应该只是检查并返回检查结果。

经常使用的函数有时具有超短名称。比如jQuery库定义了函数$,Lodash库的核心函数用`_`命名。这些都是例外。一般来说，函数名应该是简洁和描述性的。

## 函数拆分
函数应该短小并只做一件事，如果这个事情太复杂，就值得拆成多个更小的函数，有时要遵循这个规则不容易，但它肯定是好的规则。
独立的函数不仅容易测试和解决bug，它的存在本身就是一个很好的注释！

举个例子，下面的两个`showPrimes(n)`函数，都输出到n的素数。
第一个使用标签：
```javascript
function showPrimes(n) {
  nextPrime: for (let i = 2; i < n; i++) {
    for (let j = 2; j < i; j++) {
      if (i % j == 0) continue nextPrime;
    }
    alert( i ); // 一个素数
  }
}
```

第二个使用`isPrime(n)`函数来检查是否是素数：
```javascript
function showPrimes(n) {
  for (let i = 2; i < n; i++) {
    if (!isPrime(i)) continue;

    alert(i);  // 一个素数
  }
}

function isPrime(n) {
  for (let i = 2; i < n; i++) {
    if ( n % i == 0) return false;
  }
  return true;
}
```

第二种写法是不是更容易理解？我们看到的不是代码段，而是操作的名称(isPrime)。有时人们把这样的代码称为自我描述。
因此，即使我们不打算复用函数，也可以创建它们。它们使代码可读性更强。

## 总结
函数申明看起来就像下面这样：
```javascript
function name(parameters, delimited, by, comma) {
  /* code */
}
```
* 函数的传参都复制给了函数的内部变量。
* 函数可以访问外部变量，函数外部无法访问函数内部变量。
* 函数可以有返回值，如果没有，就返回undefined。

为了使代码整洁并便于阅读，建议主要使用内部变量，不要用全局变量。
毕竟，使用传参并返回一个值，比没有传参，使用全局变量并改变全局变量的写法要更便于理解。

函数命名：
* 名称应该清楚地描述函数的功能。当我们在代码中看到一个函数调用时，一个好的名称会立即让我们理解它的作用并返回。
* 函数是一个动作，所以多用动词命名。
* 有很多函数名前缀，比如 `create…`, `show…`, `get…`, `check…`，使用它们来提示函数功能。

函数是代码的主要组成部分。现在我们已经介绍了基本知识，所以我们实际上可以开始创建和使用它们。但这只是开始。我们还需要更深入地研究它们的高级特性。
