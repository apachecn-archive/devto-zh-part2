# 在本地测试谷歌云功能

> 原文：<https://dev.to/martyndavies/testing-a-google-cloud-function-locally-4121>

最近我写了一篇关于用 Google Dialogflow & Algolia 构建应用的文章。这个构建的一部分需要使用一个 webhook，我决定使用 Google Cloud 函数来构建它。

Google 有一些关于如何在您的本地环境中使用 GCF 的不错的文档，包括如何部署、检查和调试您的功能。它可以做更多的填充和一些例子，但我相信他们会得到它。

当谈到如何在本地使用这个函数，但允许本地机器之外的服务访问它时，根本没有提到如何做。

鉴于我刚刚做了这个，我想我应该快速分享一下这个过程。

## 安装 GCF 仿真器

Google Cloud Functions 模拟器的工作方式与 GCF 的主命令行界面非常相似。安装完成后，您可以按照他们的文档进行安装和运行(大约需要 5 分钟)。

## 启动一个功能

在您的开发文件夹中，您可以使用以下命令启动模拟器:

```
$ functions start //starts the emulator
$ functions deploy myFunctionName --trigger-http //deploys your function as HTTP 
```

注意:有一点需要注意:在我的机器(Macbook Pro)上，命令`functions`已经被保留，所以我不得不使用其他的命令。Google 提供了一个替代命令，`functions-emulator`，你可以用它来代替`functions`。您可能希望在终端中为这个命令取别名，这样就不必每次都键入它。

当一个函数开始运行时，您会得到如下所示的输出:

[![Google Cloud Functions Emulator Output](../Images/648043f6d39a93d7eb0f4056261cd6c5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jN3ihNFw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cl.ly/2y1x121b441B/Image%25202018-06-20%2520at%25205.01.44%2520pm.png)

在这里，您可以看到该功能已经启动并在`localhost:8010`上运行，如果您正在开发的所有其他东西也在本地运行，这是一件好事，但是如果您在其他地方有服务器需要与该端点通信，您将会陷入困境。

幸运的是， [Ngrok](https://ngrok.com/) 依然存在。

## 向世界开放功能

为了在其他地方的服务器上使用这个本地函数，或者作为一个 webhook(这是我的用例)，您需要向外界公开端口`8010`。Ngrok 允许您在安装后快速完成这项工作。

在新端子选项卡运行中:

```
$ ngrok http 8010 
```

这将打开端口，并给你一个使用的 URL。

[![Ngrok output](../Images/a2822589e6e983c6d17fc8528758ef37.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--w5W6eTP_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cl.ly/1d2T2m2S2n3z/Image%25202018-06-20%2520at%25205.06.07%2520pm.png)

这里我们可以看到 Ngrok 已经提供了公共的 HTTP 和 HTTPS URL。我们现在可以使用这些来访问我们的本地云功能，只需将`http://localhost:8010`替换为`http://95d039f9.ngrok.io`(您的 URL 会有所不同)。

就是这样。你要去比赛了！各位，无服务器开发时代快乐。