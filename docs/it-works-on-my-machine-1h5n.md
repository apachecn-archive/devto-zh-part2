# 它在我的机器上工作

> 原文：<https://dev.to/tbrisker/it-works-on-my-machine-1h5n>

> 当 Webpack 崩溃时，但只是在打包版本中——或者我如何创建我的第一个 Webpack 插件

首先，这个故事需要一些背景知识和对我正在从事的主要项目的架构的基本理解， [Foreman](https://theforeman.org) 。Foreman 是一个 Ruby on Rails 应用程序，用于基础设施管理。它有许多添加各种功能的插件，这些插件被编写为 Rails 引擎。由于此应用程序用于管理关键基础架构，因此它是在内部安装的，并以软件包的形式提供，两者皆是。许多不同的插件也打包成。转速和。黛比档案。

<figure>[![Foreman](img/e2c16a30f38522994926f20b137e4eee.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MwOBL3an--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/mwug4qbj9zva8wyh3lw6.png) 

<figcaption>工长项目标识</figcaption>

</figure>

大约两年前，我们开始更新我们应用程序的前端。为了实现这一点，这意味着将 Webpack 和 npm 集成到我们的资产构建流程中。这使得我们可以在客户端代码中使用 React 和 ES6 等很酷的新技术，并开始解决遗留 JS 代码中长期存在的问题——例如缺乏林挺和单元测试。创建 Webpack 构建过程的第一步需要一段时间。原因之一是 RPM 包构建器必须在一个不连接的环境中运行，所以我们必须找到一种方法，在构建时将所有节点模块作为 RPM 提供给构建器，但这本身就是一篇完整文章的内容。一旦解决了这个问题，我们就开始逐步用新的、Webpack 管理的代码来移植或替换遗留代码库。

我们最近面临的一个主要挑战是为插件提供一种方法，利用 Foreman core 中现有的 npm 和 Webpack 堆栈来编写自己的客户端代码。我们不想强迫每个插件创建自己的构建管道，也不想在每个插件的 JS 包中包含每个库的多个副本。我的一些同事通过一些巧妙的工程工作解决了这个问题。该解决方案包括从主应用程序的代码库为插件和核心应用程序编译 Webpack 资产，同时为插件提供一个包含多个共享库的`vendor.js`文件——如 React、Redux、Lodash 等。这在开发过程中，甚至在为本地生产编译资产时，都工作得非常好。

然而，当我们尝试安装带有插件的夜间软件包时，奇怪的事情开始发生了。包含 Webpack 管理的资产的核心页面运行良好，但是需要使用插件的 Webpack 资产的插件页面无法加载。往控制台里看，看起来没什么希望:

```
TypeError: e[t] is undefined 
```

Enter fullscreen mode Exit fullscreen mode

<figure>[![E.T.](img/156335ca0a18dc1b9f0573987efd0fc4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PqbcdmZl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/p5uggoh45iak314uammk.jpeg)

<figcaption>e【t】</figcaption>

</figure>

现在，Webpack 构建过程的一部分包括缩小 js 代码，使它下载起来更小，但调试起来更困难。幸运的是，浏览器开发工具知道如何美化代码，所以它至少是半可读的。这指出了第 9 行的以下函数中的错误: