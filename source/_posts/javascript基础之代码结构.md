---
title: javascript基础之代码结构
date: 2019-05-07 14:46:23
tags: js
categories: js
---

> 本文译自[Code structure](https://javascript.info/structure)

## 代码结构
我们首先要学习的是代码块的构建。

## 语句
语句是语法结构和执行操作的命令。
我们已经见过语句，`alert('Hello, world!')`，显示信息“Hello, world!”。在我们的代码中，我们可以写非常多的语句，语句可以用分号隔开。
举个例子，我们可以分割“Hello World”成2个alert:

```javascript
alert('Hello'); alert('World');
```

通常，语句都是写在单独的行，使代码可读性更强：

```javascript
alert('Hello');
alert('World');
```

## 分号
在存在换行符的大多数情况下，分号可以省略。
这样也可以：

```javascript
alert('Hello')
alert('World')
```

这里，JavaScript将换行符解释为一个“隐式”分号。这称为[自动插入分号](https://tc39.github.io/ecma262/#sec-automatic-semicolon-insertion)。

<b>在大多数情况下，新的一行意味着分号，但大多数情况不代表所有情况！</b>

有些情况新的一行就不代表分号，举个例子：

```javascript
alert(3 +
1
+ 2);
```

这里输出了6，因为javascript没有插入分号。很直观，如果一行以“+”号结尾，那么它是一个不完整的表达，所以不需要分号，此时它就像预期的那样工作。

<b>但是在某些情况下，JavaScript在真正需要分号的地方“失败”了。</b>
这些地方报错将相当难发现并修复。

### 一个报错的例子
如果你想看看这样一个错误的具体例子，看看这段代码:

```javascript
[1, 2].forEach(alert)
```

先不要想[]和forEach的含义，后面我们会学到。现在仅仅记住代码运行的结果是：显示1，然后显示2.

现在我们在代码前面增加一个alert，不用分号结尾：

```javascript
alert("There will be an error")

[1, 2].forEach(alert)
```

现在运行代码，只有第一个alert显示了，然后我们看到一个error报错！
但当我们在alert后面加个分号，一切就都正常了：

```javascript
alert("All fine now");

[1, 2].forEach(alert)
```

现在我们看到“All fine now”，然后看到1和2。
没有分号报错是因为JavaScript没有假设`[...]`前面有分号。所以，由于没有自动插入分号，第一个例子被视为一个语句，就像这样：

```javascript
alert("There will be an error")[1, 2].forEach(alert)
```

但它们因该是两个语句而不是一个，在本例中，这样的合并是错误的，因此会报错。这种错误可能发生在其他情况下。

我们推荐在语句之间放置分号，即使语句之间有换行。这条规则被广泛采用。我们再强调一遍————分号在大多数情况下是可以省略的。但是使用它们更安全，尤其是对于初学者来说。

## 注释
随着时间的推移，代码变得越来越复杂，有必要增加注释来说明代码做了什么，为什么这样做。

注释可以放在代码中的任何位置，不会影响代码的运行，因为引擎忽略了它们。

### 单行注释
单行注释`//`，这一行的其余部分是注释。它可能占据自己的一整行或遵循一个声明。

```javascript
// 这个注释独自占了一行
alert('Hello');

alert('World'); // 这个注释在语句后面
```

### 多行注释
多行注释以`/*`开头，以`*/`结尾。

```javascript
/* 两条信息的示例
这是一个多行注释
*/
alert('Hello');
alert('World');
```

注释的内容会被忽略，如果我们在`/* … */`中放置代码，将不会执行。
有时暂时禁用部分代码是很方便的:

```javascript
/* 将代码注释掉
alert('Hello');
*/
alert('World');
```

<div class="tip">
<p>使用快捷键！</p>
大多数编辑器可以按`Ctrl+/`注释一行代码，按`Ctrl+Shift+/`注释多行代码（选中一段代码后按快捷键）。Mac用`Cmd`键代替`Ctrl`。
</div>

<div class="tip">
不支持嵌套注释
</div>
在`/* … */`内部不要再有`/* … */`。这样的代码会报错：

```javascript
/*
  /* 嵌套 ?!? */
*/
alert( 'World' );
```

请不要犹豫对您的代码进行注释。

注释增加了代码量，但这不是问题，有很多工具能在发布前压缩代码，可以移除注释，所以注释不会出现在发布后的代码中。所以注释不会对结果有任何坏处。

后面有一个代码质量的章节会说明如何写更好的注释。
