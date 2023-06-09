# ⚡无服务器微软 Azure 函数实用指南

> 原文：<https://dev.to/ahmadawais/-practical-guide-to-serverless-microsoft-azure-functions-with-javascript-42oa>

> 查看我为开发人员开设的最新课程
> 
> *   [NodeCLI.com](https://NodeCLI.com)—构建 Node.js CLI 自动化开发工具。10 个小时，100 个视频，22 个项目，初学者友好型。
>     
>     
> *   [VSCode.pro](https://VSCode.pro) —成为 VSCode 超级用户，学习 200 多种提示/技巧工作流，如多光标、调试、&宏。了解我几乎所有的工作流程。

* * *

作为一名全栈 JavaScript 开发人员，我对这个相对较新的 FaaS 或功能即服务产品感到非常兴奋，它还获得了一个名字`Serverless`——因为你不必管理、更新、修补或担心服务器。

当[构建这个定制的 WordPress 仪表盘](https://ahmadawais.com/wordpress-mongodb-atlas-microsoft-azure-serverless-functions/)时，我想确保这个仪表盘的每个模块都以一个无服务器应用程序的形式存在，具有多种无服务器功能。这个决定是基于尽可能降低仪表板的成本。

### 👀三种选择

目前有三大云服务提供商。分别是[微软 Azure](https://azure.microsoft.com/en-us/) 、[谷歌云平台](https://cloud.google.com/)、[亚马逊网络服务](https://aws.amazon.com/)。其中每一个都有称为 Azure 函数、GCP 云函数和 AWS Lambdas 的可用无服务器函数。

### 📘选择 Azure

Azure 拥有最大的云架构和全球业务。50 个 Azure 地区，比任何云提供商都多，在测试了这三个地区后，我发现 Azure functions 在阿联酋的响应时间最短(因为我的客户的业务是在阿联酋之外)。

此外，我们使用 Azure ML Studio、人工智能认知服务和虚拟机来托管这个项目的一部分，将 Azure 功能用于无服务器架构是完全有意义的。

## Azure 功能入门

让我们从 Azure 函数开始。我将带你完成创建一个简单的无服务器 Azure 函数的过程，这个函数将通过 HTTP 请求触发，在这个函数中，我们将处理从 Paddle.com 发给我们的销售信息。

### ⚙我们在建造什么？！

1.  我正在构建一个无服务器的 Azure 函数，它基于 JavaScript，特别是 Node.js 代码。
2.  这个 Azure 功能将由来自我们第三方支付解决方案，即 Paddle.com 的一个简单的`GET` HTTP 请求触发
3.  只要 Paddle.com 上有销售，它就会[触发一个 webhook](https://paddle.com/docs/reference-using-webhooks) ，其中包含与我们的销售、数量、商品、收入相关的信息，以及 WordPress 发送给 Paddle 的一些会员相关数据。
4.  使用 WordPress REST API，我添加了一些与购买产品的用户相关的自定义数据，比如用户在 WordPress DB 中的 ID，哪个 WordPress 站点有这次销售，以及这样的用户的元信息。
5.  当 Azure function 接收到这个`GET`请求时，它处理信息，取出我需要保存在 MongoDB Atlas 集群中的内容，并形成一个准备保存在 DB 中的 JavaScript 对象。
6.  然后，azure 函数通过一个名为[mongose](http://mongoosejs.com/)的 npm 包连接到 MongoDB Atlas 实例，在连接数据库后，我创建了一个 DB 模型/模式，然后这些数据被保存到 MongoDB Atlas 集群中。
7.  之后，Azure function 就在那里等待下一次销售，我的客户只为 Azure function 的执行时间和执行量付费。([其中 100 万每月免费](https://azure.microsoft.com/en-us/pricing/details/functions/)😮).

现在，这只是对正在发生的事情的高级总结，我在这里跳过了许多步骤，例如身份验证，这超出了本文的范围。您应该始终设置认证和验证，以保持文明，避免任何超龄。

所以，让我们继续建造这个东西。

### 第一步:设置微软 Azure & VSCode

我希望你能在你那边设置好 Azure 账户。你需要用信用卡订阅，因为我们需要存储空间来托管将与 Azure 函数一起使用的 Node.js 文件，并且你必须为存储空间付费(你可能会在第一个月获得 200 美元的免费信用，即使在那之后，费用也相当低)。因此，请继续设置以下内容:

1.  ✅在账单中用信用卡设置了一个微软 Azure 账户。
2.  ✅安装 [Visual Studio 代码](https://code.visualstudio.com/) (Psst。我正在制作一个关于 VSCode 的[课程。](https://ahmadawais.com/visual-studio-code-power-user/)
3.  ✅在你的 VSCode 上安装 Azure 函数扩展。
4.  💡要启用本地调试，请安装 [Azure Functions 核心工具](https://github.com/Azure/azure-functions-core-tools#installing)。
5.  🗂创建了一个新目录，并在 VSCode 中打开它。

如果你想知道我用的是哪种主题和字体，那就是🦄[紫色的阴影](https://marketplace.visualstudio.com/items?itemName=ahmadawais.shades-of-purple)——还有我的 [VSCode.pro 课程](https://VSCode.pro/)。更多信息请参见我使用的软件和硬件。

[![VSCode 🦄 Shades of Purple theme](img/cb5e32b781d033b36f0c3db3669eaab0.png)T2】](https://marketplace.visualstudio.com/items?itemName=ahmadawais.shades-of-purple)

### 第二步:新建一个函数 App 项目

现在让我们创建一个新的函数应用程序项目。这对于 VSCode 来说非常简单。你所要做的就是进入活动栏中的 Azure 扩展浏览器。从那里进入`FUNCTIONS`选项卡并点击第一个`Create New Project`图标。

这将创建一个演示项目，包含入门所需的基本文件，并将为您初始化一个 Git repo。我将继续提供基于 gif 的小演示，以使您的工作更轻松。

[![GIF for MongoDB Atlas, Microsoft Azure, & Serverless Functions](img/48ee156da23570ce3f177c6ff15aaa30.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XoMupCzt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://ddwva799xzrph.cloudfront.net/items/1l0g2p0Z3q1F0T120d0F/Screen%2520Recording%25202018-05-21%2520at%252011.41%2520PM.gif)

### 第三步:创建一个 HTTP 触发的 Azure 函数

现在我们已经创建了一个函数 app 项目，让我们创建一个 HTTP 触发的无服务器 Azure 函数。为此，请访问活动栏中的 Azure Extension explorer。从那里进入`FUNCTIONS`选项卡并点击第二个图标`Create Function`。

出于演示目的，我选择保持身份验证部分简单，因此选择匿名访问。我们的 Azure 函数的名字是`HttpTriggerJS`,所以你可以在你的项目中找到一个用这个名字创建的新目录。这应该包含两个文件，即`functions.json`和`index.js`。

⚡ A *函数*是 Azure 函数中的一个主要概念。您可以用自己选择的语言编写函数代码，并将代码和配置文件保存在同一个文件夹中。

🛠配置名为`function.json`，包含 JSON 配置数据。它定义了函数绑定和其他配置设置。运行库使用该文件来确定要监视的事件，以及如何将数据传入函数执行并从函数执行返回数据。[点击](https://docs.microsoft.com/en-us/azure/azure-functions/functions-reference#function-code)在官方文档中阅读更多关于这个文件的信息。

下面是一个创建的示例`function.json`文件。