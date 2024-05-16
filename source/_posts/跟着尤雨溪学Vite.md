---
title: 跟着尤雨溪学Vite
date: 2022-01-21 11:26:12
tags:
categories: js
---

## 前言
本文是翻译的尤雨溪在油管上发布的[Learn Vite with Evan You](https://www.youtube.com/watch?v=DkGV5F4XnfQ&ab_channel=VueMastery)视频。
下面开始正文部分。

## 正文
尤雨溪在这里欢迎大家，今天的视频我们会讲讲Vite。

Vite是一个我从去年开始开发的新构建工具，并且我们认为它会成为下一代的前端工具，所以，这个视频我们会谈谈，什么是Vite，为什么是Vite，而且我们还会展示怎么开始用它，以及Vite没有提供的一些很棒的特性。接下来就开始吧。

首先，什么是Vite?

如果你去官网，会看到Vite是个法语单词，意思是“快”，有些人把它读成`/vait/`，但它实际上是表示“快”的法语单词，所以我们读`/vit/`。

Vite是一个构建工具，用在开发环境，也可以为生产环境打包。如果你用过Vue CLI，你可以认为它们差不多。

Vite要解决的问题是更新速度，如果你在一个很大的项目中用过Vue CLI，你可能要等半分钟，甚至一分钟，本地环境才会更新，随着项目变大，更新就变慢，只修改了一个文件，你就不得不等好几秒，等着屏幕上的东西更新。

变慢主要是因为我们要打包，我们为什么需要打包呢？
因为过去浏览器不支持ES Modules，无法加载模块化的代码，所以早期我们发明了模块化规范，比如AMD和CommonJS，我们写完代码后，用一些工具，比如webpack、Parcel、Browserfy，把代码打包成一个浏览器上可以运行的文件。

幸运的是，如今大部分的现代浏览器都能支持ES Modules，这意味着开发阶段的大量工作我们不用做了，浏览器会帮我们处理，所以用Vite的前提是我们把代码写成ES Modules，编译文件时找到import申明，并对每个模块发一个HTTP请求到开发服务器，由于大部分工作浏览器做了，所以我们需要做点额外的工作。

另一方面，如果你有很多依赖，Vite会很智能的先用esbuild打包来减少请求。

Esbuild是Evan Wallace用Go写的一个工具，把代码编译成浏览器可执行的程序，所以要快几个数量级，然后Javascript有了同样的工具。

因为上面这些原因，Vite可以提供一个极快的开发体验。所以，这是为什么是Vite。

对于快字，我认为Vite有多快你用一下就知道了，那么，我们现在就用用看。

首先确保你装了Node.js和npm，然后就可以简单的运行命令：

```
npm init @vitejs/app
```

回车后会问你项目名，这里我就叫它hello-vite吧，然后会让你选一个框架，Vite对框架没有限制，所以即使你用react或者preact，你也可以用Vite，不过，作为第一个demo，我根本不想用框架，所以我们就选vanilla模板，Vite也可以支持Typescript，不过现在我们只选择vanilla，接下来我们进入hello-vite目录，开始前要先用npm安装依赖。

![init](../images/2022/init.png)

这时我们先看看我们的项目，有一个index.html，里面有一个script标签，type属性是module，src指向main.js，这个语法需要浏览器支持ESM。
![index](../images/2022/index.png)

接下来我们看看package.json，你可以看到，Vite是唯一的依赖，此外，还有一些npm scripts，有dev,build和serve的scripts，所以，让我们直接`npm install`吧。
![package](../images/2022/package.png)

我要开始运行npm install了，你可以看到安装过程非常的快，一共安装了13个包，就是这么简洁。而且我认为Vite没有本地缓存，从注册中心安装，花了不到1秒。
![install](../images/2022/install.png)

接下来我运行npm run dev启动服务，同样很快，本地服务就跑起来了，接着打开页面，就看到了Hello Vite，这就是一个普通的本地开发服务器。
![serve](../images/2022/serve.png)

我们再看回代码，index.html和main.js都没什么变化，如果我们把main.js改成main.ts，重启服务，一样能跑起来，在Vite项目中，你可以直接引进ts文件，Vite会自动转译，仅此而已，这意味着Vite不会做类型检查。
![ts](../images/2022/ts.png)

我们这样做的第一个原因是，我们用esbuild来编译ts文件，esbuild是用Go写的，比起用javascript写的ts自身的编译要快30倍，所以当我们用esbuild来编译，由于没有类型检查，如果你import一个type，确保是从另一个文件中import的，这是一个坑。

```
import type { } from './'
```

但这对于非常大的typescript项目中开发体验得到极大改善来说，只是一个很小很小的代价。

另一个原因是如果对你的编辑器，比如VS Code，做了TS相关的合适设置，可能你的IDE就会对ts文件做类型检查，ts已经在你的IDE中运行了。所以Vite就说：“好吧，就把这部分给IDE和打包程序吧”，在开发阶段Vite对ts的类型检查是不确定的，因为这部分交给其他工具了。关于typescript的就这么多。

接下来我们花1分钟讲下CSS。
可以看到我们能直接import CSS，接着我准备把这里的color改成red，你可以看到页面立刻就更新了，注意看浏览器地址栏，我们再把color改成green，地址栏没有重新加载，接着我打开控制台的Elements栏，这时我们再次改变color，你可以看到，页面没有重载，这里热更新了，加载的CSS局部更新。
![css](../images/2022/css.png)

过去我们通过npm来`import`、`install`、使用依赖，比如我想用lodash，从lodash-es中`import debounce`，浏览器并不能处理，因为它不知道什么是npm，不知道怎么处理依赖，但是Vite知道怎么处理。

接下来我要`install lodsah-es`，然后`npm run dev`重启服务，你可以看到IDE已经判断出我们安装了lodash-es，所以它现在有类型定义，我加个`console.log(debounce)`，打开浏览器控制台，可以看到我们能够从lodsah-es中拿到debounce函数，这点很棒，Vite能够为你处理npm依赖。

还有一点很棒，如果我们去network中查看下debounce，可以发现Vite把lodash-es的众多模块组合成了一个文件，所以它比一般情况下加载地更快，如果你通过ES Modules加载loadsh-es，那就是另外一个表现了，嗯，npm依赖地加载就这么多。

## 总结
以上就是视频的主要内容了，视频是2021年9月15日发布，从Vite名字的由来，讲到为什么需要Vite，能解决什么问题，需要付出什么代价，然后带大家初步体验Vite的使用，体验启动及热更新速度，处理typescript和npm依赖。

想了解更多，建议看看[官网](https://cn.vitejs.dev/)。
