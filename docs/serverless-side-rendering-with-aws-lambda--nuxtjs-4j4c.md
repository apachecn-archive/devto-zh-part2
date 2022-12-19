# 使用 AWS Lambda & NuxtJS 的无服务器端渲染

> 原文：<https://dev.to/fernalvarez590/serverless-side-rendering-with-aws-lambda--nuxtjs-4j4c>

[![](../Images/576ee41b103fb8aad09f2422af8b82fb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cJX6Tfvd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AwCN74zqZqZR1ibLdBeyCBw.png)

有两件事我很喜欢知道:

*   AWS Lambda 等无服务器生态系统
*   VueJS 生态系统

有一天，我在办公室和一些人讨论无服务器和 Lambdas，如何制作 lambda，如何使用无服务器框架，创建你自己的 REST API，等等。

在那次讨论之后，鉴于我正在和 VueJS 一起工作，一个简单但疯狂的想法出现了:

> “如果我们可以使用 AWS Lambdas 而不是虚拟机来提供服务器端渲染前端，会怎么样？”

我决定使用 NuxtJS 来研究它，因为它是我最喜欢的工作工具，而且， [AWS 刚刚为 AWS Lambda](https://aws.amazon.com/blogs/compute/node-js-8-10-runtime-now-available-in-aws-lambda/) 推出了 NodeJS 8.10 运行时，这是测试这个想法和新运行时的完美时刻。

### 哪里找的例子？

如果您想直接跳到代码的最终版本，可以在 github 中找到 repo:

[jefer 590/server less-side-nuxtjs-example](https://github.com/jefer590/serverless-side-nuxtjs-example)

### 先决条件

*   AWS 帐户
*   [节点 j](https://nodejs.org)
*   [无服务器 CLI](http://serverless.com)
*   [纱](https://yarnpkg.com/en/)(可以用 npm)

### 设置

首先，我们需要使用 yarn(或者使用 NPM)初始化一个新的 NodeJS 项目，使用下一个命令:

```
$ yarn init 
```

Enter fullscreen mode Exit fullscreen mode

然后，是时候安装这个项目所需的依赖项了:

```
$ yarn add aws-serverless-express axios express nuxt 
```

Enter fullscreen mode Exit fullscreen mode

但是，这些依赖是什么呢？让我简单解释一下:

*   ExpressJS 是一个用于 NodeJS 的 web 开发的极简框架
*   aws-serverless-express 是在 AWS Lambda(这个项目的核心)中运行 express 应用程序的代理
*   axios 是一个用于 NodeJS 和 Web 浏览器的 HTTP 客户端
*   NuxtJs 是 VueJS 的服务器端渲染框架

安装完所有的依赖项后，我们将需要一个无服务器插件来使这一切工作:

```
$ yarn add -D serverless-apigw-binary 
```

Enter fullscreen mode Exit fullscreen mode

这个无服务器插件自动在 API Gateway 中添加二进制文件支持。(摘自 [Github 回购](https://github.com/maciejtreder/serverless-apigw-binary))

现在，是时候给我们的*包添加一些脚本了*

```
... "scripts": { "dev": "nuxt", "build": "nuxt build", "deploy": "npm run build && sls deploy" }, ... 
```

Enter fullscreen mode Exit fullscreen mode

*   *“dev”*是在本地运行 nuxt，测试我们的页面和布局是否工作正常
*   *【build】*用于构建 NuxtJS 服务静态 html 所需的脚本和资产
*   *“部署”运行*项目构建，并使用无服务器 CLI 在 AWS Lambda 中部署我们的功能。

### 配置

首先，我们需要一些与 NuxtJS 和 Serverless 相关的配置

#### NuxtJS 配置

在我们的根文件夹中，我们将创建一个名为 **nuxt.config.js** 的文件，该文件旨在包含 NuxtJS 正确构建和运行所需的所有配置。[你可以在 NuxtJS 站点查看配置文件的文档。](https://nuxtjs.org/guide/configuration/)