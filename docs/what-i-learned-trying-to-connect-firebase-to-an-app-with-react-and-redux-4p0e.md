# 我在尝试用 React 和 Redux 将 Firebase 连接到一个应用程序时学到了什么

> 原文：<https://dev.to/papaponmx/what-i-learned-trying-to-connect-firebase-to-an-app-with-react-and-redux-4p0e>

## [T1】简介](#intro)

我有一个非常特殊的问题，我正试图用软件来解决。如果你读过*高效人士的七个习惯*，就可以根据第三个习惯来组织我的一周。一旦我有了最基本的功能，我会发一个帖子，这样你就可以试用了。

最初，它应该是一个由静态服务器提供服务的应用程序，并一直驻留在客户端上。

一旦我制作了大部分的应用程序并开始学习`GraphQL`。这让我意识到，如果我可以将它绑定到我的谷歌账户，并从任何设备访问我的数据，我就可以从中获得更多价值。

这对大多数人来说应该是显而易见的，但对我来说却不是。

## 少即是多

我是一名前端开发人员，所以我不想设置服务器、Docker 容器、微服务和所有那些使应用程序非常安全、超高性能、可伸缩等的功能。

正如我在介绍中告诉你的，这是一个个人项目。所以我决定尝试 Firebase 托管和后端即服务，

*   托管。火焰基地。
*   实时数据库。火焰基地。
*   用户管理，只需登录谷歌。没有邮箱和密码。
*   **GraphQL** 事情的一面。火焰基地。我只需要一些功能和节点的无服务器设置。

## 始于终于念

我花了一天时间按照文档尝试插入这个库*，这样我就可以让 Firebase、React 和 Redux 一起玩*。这就是目标，对吧？

结果弄得一团糟。我开始仔细检查我的代码，问题是:

*   我注入了一个`reducer`,其中包含了一些既不需要也不使用的特性。
*   我初始化了我的应用程序两次。
*   出于某种原因，我试图引用应用程序初始化，以获得认证。
*   我正在用我的传奇故事运行 getters 而不需要它们。

我开始怀疑我真的需要这个吗？，为什么？。

## 一次做一件事

我相当精通`react`、`redux`、`redux-saga`、`react-router`等。有许多小零件，一起玩。在大多数情况下，您从小处着手，并在需要时添加每个特性。

我以前做过几次。我真的了解基本原理和每个部分的功能。

您可能已经意识到，Firebase 为您提供了许多解决方案。问题是我找到了一个看起来像我正在寻找的库，并试图一次实现它们。

这个库是 [`react-redux-firebase`](http://react-redux-firebase.com/) ，我最终发现我甚至不需要它。

## 简单总是更好

沮丧又好奇，看了几个资料来源后。我决定移除所有我不需要的东西，然后一次只做一件。这比我想象的要简单和容易。以下是我需要按顺序做的事情清单:

*   托管设置。
*   将配置保存在 repo 中的某个位置。
*   在 Firebase 控制台中启用 Google 身份验证。
*   创建登录页面组件。
*   `MapDispatchToProps`为了执行与点击适当的动作。
*   观看 a `watcherSaga`中的动作。
*   在 worker saga 中，打开 Google Auth 弹出窗口并处理响应。

## 结论

对于了解高阶组件，JWT，ES6 发电机和可观测量的人。这是一个有趣的处境。

一旦你明白了，事情就更容易解释了。总是有新的东西要学，尽管最初很沮丧，但这是一次很棒的学习经历。

如果你想让我写一篇文章，一步一步地指导你如何做我在这篇文章中描述的事情，请告诉我。

## 进一步资源

*   [Firebase 托管](https://firebase.google.com/docs/hosting/?authuser=0)
*   [添加燃烧基](https://firebase.google.com/docs/web/setup)
*   [Redux Saga](https://redux-saga.js.org/docs/introduction/BeginnerTutorial.html)
*   [使用带有 JavaScript 的 Google 登录进行身份验证](https://firebase.google.com/docs/auth/web/google-signin)