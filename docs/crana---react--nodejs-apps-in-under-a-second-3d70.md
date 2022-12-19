# Crana - React & NodeJS 应用程序在一秒钟内完成

> 原文：<https://dev.to/scriptify/crana---react--nodejs-apps-in-under-a-second-3d70>

[![](../Images/b96904e9ccc11589d4d597681a18276a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cjvO5CZ_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m46kkqxivtkg8ynre7cd.png)

我利用 Hacktoberfest 的机会，试图发布我最近正在做的一个项目:[Crana](https://github.com/scriptify/crana)(**CR**eat**A**T6】Node**A**PP)，这是一个 CLI 工具，它使开发具有 NodeJS 后端和 React 前端的应用程序变得轻而易举。

```
npm i -g crana
crana init my-awesome-project
cd my-awesome-project
crana dev 
```

Enter fullscreen mode Exit fullscreen mode

# 问题

我参与的大多数项目都涉及到服务器(node.js)和客户端(大多基于 React)。到目前为止，我还没有找到任何满足此类项目需求的解决方案，而且一遍又一遍地复制每个新项目的所有配置文件是非常有问题的。我想有一个简单的工具来封装所有这些开销，让开发人员只做他们应该做和想做的事情:开发。我想这个解决方案也可能对其他开发者有所帮助。
就这样**克兰纳**诞生了。

# 非/解

Crana 试图成为一个“刚刚开始开发”(JSD)的解决方案，让开发人员能够创建应用程序，而不必担心复杂的工具或配置。更多信息，看一下 git 库:[https://github.com/scriptify/crana](https://github.com/scriptify/crana)
T3】警告:项目处于非常早期的阶段，可能无法满足你的所有需求，更容易出现 bug。这就是为什么我们需要你的帮助，让 Crana 成为同类产品中最好的工具！

它配有:

*   ESLint(服务器和客户端配置相同)
*   斯泰林特
*   网络包
    *   开箱即用的代码分割支持
    *   配置为创建最佳的生产版本
    *   所有预配置的装载机
    *   webpack-dev-server 提供出色的开发体验
    *   预装反应热加载器
*   巴比伦式的城市
*   Nodemon(在开发过程中启用实时重新加载服务器)
*   在服务器和客户端之间轻松共享代码的能力
*   ...还有很多其他的东西都藏在包裹里

由于所需的所有配置文件和依赖项都封装在 **Crana** 本身中，所以您不会看到一个配置文件。

但是就像外面的一切一样，Crana 不仅仅是独角兽和彩虹。所有东西都封装在包中这一事实有很多优点，但也有一些缺点:

*   目前无法修改/扩展配置文件
*   为了对某些行为进行更多的推理，必须直接在包的代码中查找配置文件

但是这两个缺点只是另外两个有待解决的问题:

*   使每个项目的配置可扩展/可修改应该是 Crana 的下一个重要特性(也许实现类似插件系统的东西？)
*   Crana 的文档应该以这样一种方式扩展，即所做的每一个决定都应该被准确地写下来并加以解释(例如 webpack loaders、babel config、eslint config 等)。)

也许你就是要解决这些问题的人！

总而言之，我认为 **Crana** 对于许多(新的和现有的)项目来说是一个非常有用的工具，遵循与其他类似工具略有不同的概念。但最重要的是，这个项目最需要**你**，不管是作为用户还是贡献者，来完成它*适度的*愿景。

我很想听听你的意见！

(你也可以在推特上找到我:@scriptifyjs)