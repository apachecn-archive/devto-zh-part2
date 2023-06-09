# 前沿的无服务器框架样板

> 原文：<https://dev.to/orangejellyfish/a-bleeding-edge-serverless-framework-boilerplate-2g1f>

在 [orangejellyfish](https://www.orangejellyfish.com/) 我们是[无服务器](https://serverless.com/framework/)框架的忠实粉丝，并在许多项目中使用它。随着时间的推移，我们意识到需要一个样板无服务器应用程序来节省开始时的时间，这就是我们所建立的。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)/[无服务器启动](https://github.com/orangejellyfish/serverless-starter)

### AWS Lambda 的无服务器框架初学者工具包

<article class="markdown-body entry-content container-lg" itemprop="text">

# 无服务器启动器

一个由 orangejellyfish 为运行在 AWS 上的无服务器框架应用提供的自以为是的入门套件。受 [Postlight](https://github.com/postlight) 的优秀[无服务器巴别塔启动](https://github.com/postlight/serverless-babel-starter)项目的启发，并根据该项目改编而成，面向未来。

## 特征

*   Lambdas 默认运行 Node 14，使您的函数更快，并让您能够使用更新的 ECMAScript 功能，包括异步/等待、可选链接和无效合并。

*   Lambda 代码通过[无服务器 webpack](https://github.com/serverless-heaven/serverless-webpack) 插件与 Webpack 5 捆绑在一起，减少了部署到 AWS 的代码量。

*   Lambda 代码是用 [Babel 7](https://babeljs.io/docs/en/next/index.html) 和 [babel-preset-env](https://babeljs.io/docs/en/next/babel-preset-env.html) 编译的，这意味着如果你需要，你可以使用更先进的 ECMAScript 特性，而不必编译 Node 14 支持的代码。

*   Lambda config 位于函数代码旁边，从顶级无服务器配置文件中引用，提供了更好的关注点分离，并保持配置文件的可读性。

*   IAM 角色是通过[server less-IAM-roles-per-function](https://github.com/functionalone/serverless-iam-roles-per-function)插件按 Lambda 配置的，这意味着…

</article>

[View on GitHub](https://github.com/orangejellyfish/serverless-starter)

初学者工具包是运行在 AWS 中的无服务器应用程序的固执己见的样板文件。它旨在提供尽可能多的面向未来的功能，这在快速发展的 JavaScript 生态系统中是一个棘手的挑战！我们的起点是 [Postlight](https://github.com/postlight) 制作的神奇的“[现代无服务器启动套件](https://github.com/postlight/serverless-babel-starter)”。虽然这个项目的名字中有“现代”这个词，但我们觉得我们可以更安全地接近前沿。

## 特性

在设计无服务器入门套件时，我们考虑了一些关键要求
，这些要求直接转化为功能:

*   **节点 8 支持**。这对我们来说很关键。自 2018 年 4 月以来，AWS 已经[支持 Node 8](https://aws.amazon.com/blogs/compute/node-js-8-10-runtime-now-available-in-aws-lambda/) 作为 Lambda 运行时环境，为一系列最新的 ECMAScript 功能(包括 async/await)提供了原生支持，由于 V8 的最新版本，性能得到了提高，内存消耗也有所减少。

*   **Webpack 4 支持**。重要的是，我们可以将 Lambda 函数代码与 Webpack 捆绑在一起，以减小上传到 AWS 的包的大小。 [Webpack 4](https://medium.com/webpack/webpack-4-released-today-6cdb994702d4) 是最新和最棒的版本，与之前的版本相比，它提供了重大的速度改进和更简单的配置。

*   **巴别塔 7 支持**。这更像是一个面向未来的简单例子。目前没有什么技术上的理由来支持 Babel 7 而不是更稳定的版本 6，但是有时候挑战极限是很有趣的！

*   **地方发展支持**。如果每次进行更改时都必须将功能部署到 AWS，那么开发人员的体验会有些糟糕。幸运的是，这个问题已经被[无服务器离线](https://github.com/dherault/serverless-offline)插件解决了，它在本地模拟 AWS Lambda 和 API 网关。

*   **笑话支持**。单元测试很重要。在我们看来，笑话是这项工作的最佳工具。初学者工具包包括 Jest 配置，用于自动收集代码覆盖率信息。

*   **ESLint 配置**。我们坚信[需要一致的代码](https://dev.to/blog/code-consistency-with-eslint-and-husky/)，因此初学者工具包附带了预配置的 ESLint 设置，以及 Husky 和 Lint-staged，以便在提交时有效地 lint 和自动修复代码。

## 用法

无服务器 CLI 允许您从 GitHub 上的模板构建一个新的无服务器应用程序。运行以下命令来使用我们的初学者工具包:

```
serverless create --template-url https://github.com/orangejellyfish/serverless-starter --path your/local/path 
```

Enter fullscreen mode Exit fullscreen mode

## 接下来是什么？

我们认为这个初学者工具包提供的特性集为任何无服务器应用程序提供了一个坚实的开端，但这只是基础。请关注这个空间，将来会有更高级的工具包，基于这个工具包，添加对 DynamoDB 之类的东西的预配置支持。