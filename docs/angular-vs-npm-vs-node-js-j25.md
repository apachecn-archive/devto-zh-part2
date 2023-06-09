# 角度 vs NPM vs Node.js

> 原文：<https://dev.to/bellons91/angular-vs-npm-vs-node-js-j25>

关于 Node.js、NPM 和 Angular 有很多混淆。这三个“实体”经常一起使用，尤其是在角度应用中，但它们不是一回事。

大家说清楚！

[![Typical structure of an Angular Application](img/af07f555eccb3119ba64fcee82c97814.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lFRnWtUs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dius.imgix.net/2015/01/Screen-Shot-2015-02-17-at-12.04.46-pm.png)

## Node.js

Node.js 是一个基于 JavaScript 的**服务器环境**。你可以从[官方网站](https://nodejs.org/)下载并在 Windows、Linux、Mac 等平台上运行。

**Node.js 使用异步编程**:典型的服务器每次处理一个请求(因此在等待请求完成时会产生开销)，Node.js 接受所有请求，并以不阻塞其他请求的方式逐个处理它们。这显然是非常高效的。

## NPM

NPM 是 Node.js 包的包管理器。它包含数以千计的免费软件包可供下载；此外，通过命令行，很容易下载和更新这些软件包。

NPM 通常与 Node.js 一起使用，因此它与服务器环境一起安装。

安装包的方法是使用命令`npm install <package_name>`。

## 棱角分明

Angular 是一个基于 JavaScript 的框架，使得为 web 构建应用程序变得容易。您经常会看到用 Typescript 编写的 Angular 应用程序，这是一种基于 JavaScript 的现代面向类的语言。结果是一样的:Typescript 被编译成 JavaScript，所以它们是可以互换的。嗯，至少是 JS 的最新版本，因为需要类型和装饰器来分别使用*依赖注入*和设置元数据。

基于 JavaScript——猜猜怎么着？-它非常适合 Node.js，当然还有 NPM，它允许你下载 JavaScript 包来包含在项目中。

## 包装完毕

现在我们知道 Node.js、NPM 和 Angular 是什么，以及它们是如何组合在一起的。

Node.js 只是处理来自网页的请求的服务器；这些页面可以从任何类型的服务器调用 web APIs，当然也可以在任何类型的数据库中查找数据。