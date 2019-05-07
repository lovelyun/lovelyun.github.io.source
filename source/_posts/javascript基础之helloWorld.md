---
title: javascript基础之helloWorld
date: 2019-05-07 11:58:56
tags: js
categories: js
---

> 本文译自[Hello, world!](https://javascript.info/hello-world)

## Hello, world!
这个教程是关于javascript的，一个独立于平台的语言。后面你将学习node.js和其他平台来使用它。
但是我们需要一个环境来运行javascript，由于本教程是线上的，所以浏览器是一个好的选择。我们将把特定于浏览器的命令(如alert)的数量保持在最低水平，这样，如果您计划将精力集中在其他环境(如Node.js)上，就不会在这些命令上花费时间。在本教程的下一部分中，我们将重点介绍浏览器中的JavaScript。
首先，让我们看看如何将脚本附加到网页上。对于服务器端环境(如node .js)，可以使用类似`node my.js`的命令执行脚本。

## script标签
JavaScript程序可以通过`<script>`标签插入到任何`HTML`文档中。
举个[例子](https://next.plnkr.co/edit/?p=preview&preview)：

```html
<!DOCTYPE HTML>
<html>
<body>
  <p>Before the script...</p>
  <script>
    alert( 'Hello, world!' );
  </script>
  <p>...After the script.</p>
</body>
</html>
```

`<script>`标签包含JavaScript代码，当浏览器处理到这个标签的时候就自动执行。

## 现代化
`<script>`标签有一些现在很少见，但能在古老的代码中看到的属性。

### type属性
type属性:`<script type=…>`。
旧的HTML标准，比如HTML4，`<script>`标签需要type属性，通常是`type="text/javascript"`。现在不再需要了，现代的HTML标准，比如HTML5，完全改变了这个属性的含义，现在可以代表JavaScript模块，但这是一个高级的话题，我们将在本教程的另一部分讨论模块。

### language属性
language属性：`<script language=…>`。
这个属性用来表示`script`语言类型。这个属性不再有意义了，因为`JavaScript`是默认语言了，没有必要再用这个属性。

### script首尾注释
在非常古老的书籍和指南中，你可能会发现`<script>`标签里面有注释，就像这样：

```html
<script type="text/javascript"><!--
    ...
//--></script>
```

这个技巧在现代JavaScript中不再使用。这个注释为不知道怎么处理`<script>`标签的古老浏览器隐藏`JavaScript`代码。因为近15年发布的浏览器没有这个问题，所以这个注释可以让你鉴别是否是非常古老的浏览器。

## 外部scripts
如果我们有很多`JavaScript`代码，我们可以把它们放在独立的文件中。
脚本文件通过src属性插入HTML文档:

```html
<script src="/path/to/script.js"></script>
```

这里的`/path/to/script.js`是网站根目录到script文件的绝对路径。
你可以用当前页面的相对路径，比如`src="script.js"`表示一个`script.js`文件再当前目录。
我们也可以用完整的url：

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/lodash.js/3.2.0/lodash.js"></script>
```

引入多个scripts文件，就用多个`<script>`标签：

```html
<script src="/js/script1.js"></script>
<script src="/js/script2.js"></script>
…
```

<div class="tip">
请注意：
<div>作为规范，只有非常简单的script代码是直接放在HTML里面的，一般都提取出来放到单独的文件。</div>

<div>这样做的好处是浏览器会下载独立出来的script文件，并缓存起来。其他页面如果也用到这个文件，就不会再次下载，而是直接从缓存里取，这样这个script文件就只会下载一次。</div>
这样就减少堵塞，使页面响应更快。
</div>

如果有`src`属性，script里面的内容会被忽略。
一个script标签不能既有src属性，又有代码在标签里面：

```html
<script src="file.js">
  alert(1); // 内容将被忽略，因为有src属性
</script>
```

我们必须选择一种方式，外部`<script src="…">`，或者普通的`<script>`标签包住代码。
上面的例子可以分割成2个scripts：

```html
<script src="file.js"></script>
<script>
  alert(1);
</script>
```

## 总结
* 我们可以通过`<script>`标签给页面添加JavaScript代码。
* `type`和`language`属性都不再需要。
* 在独立文件中的script脚本可以通过`<script src="path/to/script.js"></script>`引入。

关于浏览器脚本及其与网页的交互，还有很多要学习的。但是让我们记住，本教程的这一部分是专门针对JavaScript语言的，因此，我们不应该用特定于浏览器的实现分散我们的注意力。我们将使用浏览器作为运行JavaScript的一种方式，这对于在线阅读非常方便，但只是其中之一。

## 任务
### 显示一个alert
创建一个页面显示信息`I’m JavaScript!`。
在沙盒、或者你的硬件设备上实现都没有关系，保证它能实现功能就行。
[在沙盒中打开答案](https://next.plnkr.co/edit/s7BD6a896UDlRmCiPEDR?p=preview&preview)

### 用外部脚本显示alert
还是上一题，把script标签的内容放到同一目录下独立的外部文件`alert.js`中。
打开页面，保证alert能弹出来。

下面是答案：
HTML代码：

```html
<!DOCTYPE html>
<html>
<body>
  <script src="alert.js"></script>
</body>
</html>
```

同一目录下的`alert.js`文件：

```javascript
alert("I'm JavaScript!");
```
