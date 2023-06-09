# 如何将传统的 Express API 迁移到无服务器并节省大量资金

> 原文：<https://dev.to/burkeholland/how-to-migrate-a-traditional-express-api-to-serverless-and-save-tons-of-money-2p5g>

无服务器的关键租户之一是，您只需为您使用的内容付费。您只需支付调用函数时使用的带宽和计算费用，仅此而已。没有无服务器，应用程序只能一直运行，这有点浪费。

你可以把无服务器想象成这个水龙头...

[![water faucet](img/2220f17a580e465004c0ff5b1572b470.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JRcn0ze5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4e8st62t68ibvwjozv5s.png)

你家里有这样的水龙头。让水龙头工作的一个方法是让水一直流出来。它总是开着的，无论何时你需要喝点什么，你只要走过去把你的杯子倒满就行了。但是我们不这样做。为什么？因为太浪费了。相反，我们在需要的时候打开水，在不需要的时候关闭水。我们只为我们使用的水付费。

无服务器使用相同的概念。每当发出请求时，函数就被加载到内存中，然后执行。当该功能不运行时，它会进入休眠状态，您无需为此付费。这意味着使用无服务器比传统的应用程序托管解决方案要便宜得多。

在本视频中，我将带您了解如何将运行在传统 web 服务器模式下的 API 从 Express 迁移到无服务器模式。

[https://www.youtube.com/embed/89WXgaY-NqY](https://www.youtube.com/embed/89WXgaY-NqY)

这是我们在视频中看到的...

*   [00:19](https://youtu.be/89WXgaY-NqY?t=19s) -已有项目的走查(可在 [Github](https://github.com/burkeholland/heroes-api) 上获得)
*   [01:27](https://youtu.be/89WXgaY-NqY?t=1m27s) -创建新的无服务器项目
*   [02:26](https://youtu.be/89WXgaY-NqY?t=2m7s) -创建一个新的 HTTP 函数
*   [02:47](https://youtu.be/89WXgaY-NqY?t=2m47s) -添加一个从数据库中读取的函数
*   [04:16](https://youtu.be/89WXgaY-NqY?t=4m16s) -本地测试
*   [04:47](https://youtu.be/89WXgaY-NqY?t=4m47s) -创建并测试其余的 CRUD 功能-创建、更新和删除
*   [07:35](https://youtu.be/89WXgaY-NqY?t=7m35s) -在 Azure 中创建新的功能应用
*   [08:35](https://youtu.be/89WXgaY-NqY?t=8m34s) -将代码部署到 Azure 中的功能应用
*   [09:20](https://youtu.be/89WXgaY-NqY?t=9m20s) -添加环境变量
*   [09:54](https://youtu.be/89WXgaY-NqY?t=9m54s) -与邮递员测试已部署的功能应用
*   [10:28](https://youtu.be/89WXgaY-NqY?t=10m28s) -使用代理创建 RESTful URL
*   [14:30](https://youtu.be/89WXgaY-NqY?t=14m31s) -将 React 项目指向新的端点
*   [15:23](https://youtu.be/89WXgaY-NqY?t=15m23s) -启用 CORS

👉[从 Github 下载源代码](https://github.com/burkeholland/heroes-api)

### 附加要求...

*   [Azure 账户](https://azure.microsoft.com/en-us/free/?WT.mc_id=migratingapi-devto-buhollan)
*   [VS 代码](https://code.visualstudio.com/?WT.mc_id=migratingapi-devto-buhollan)
*   [Azure 功能扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions&WT.mc_id=migratingapi-devto-buhollan)