# 通过构建 javascript 测试库来学习 Assert

> 原文：<https://dev.to/sadick/learn-assert-by-building-a-javascript-testing-library-57bi>

#### 用 nodejs 标准库搭建有趣的项目。

[![](img/3a284e39725d704e9be18051b5147dbc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6f8d6Q39--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A8YLBRkeWl4lvbFWmcR2pqg.png)

了解 node js 的一个方法是构建东西。很多开发人员说他们知道 nodejs，因为它只是服务器端的 javascript，但他们中的许多人不知道如何使用 Nodejs 标准库。让我们从使用 assert 库开始，构建一个简单的测试库。

测试库是一个软件，它帮助我们测试我们的代码，以确保它按照预期正确运行。脸书的 [Jest](https://facebook.github.io/jest/) 就是一个测试库的例子。我们将借用 jest 测试如何编写的结构。

从最简单的例子开始。假设我们有一段将两个数相加的代码。