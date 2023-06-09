# Aquamon:与 Salesforce 工具 API 交互

> 原文：<https://dev.to/manzanit0/aquamon-interacting-with-salesforce-tooling-api-5f7k>

自从我开始在 Frontiers 工作以来，我们有许多单调重复的技术任务，这在大多数技术工作中都很典型。其中之一是每当我们想要开始开发新功能时，就刷新我们的 Salesforce 沙箱。

我们的*工作方式*通常是首先刷新沙箱，从生产中复制，然后分支我们的库的测试分支，将所有元数据推送到沙箱，最后开始工作，直到完成并发出拉请求。

如果您曾经使用过 Salesforce，您可能会意识到从生产中刷新沙盒是多么乏味，一旦完成，在自定义设置或自定义元数据中设置所有环境变量。这就是 Aquamon 发挥作用的地方。

在做了几个月之后，我得出结论，这必须自动化。为此，我制作了一个小的 CLI 应用程序，它通过工具 API 简单地连接到 Salesforce，并发送一个带有[postboxcopy](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_interface_System_SandboxPostCopy.htm)执行脚本的刷新流程订单。

该应用程序分为 4 个不同的模块:第一个也是最简单的是在 Apex 中开发的复制后脚本，它位于 Salesforce 中。每次沙箱刷新时都会执行该操作，并简单地为自定义设置、远程站点设置等设置一些默认值。它还可以播种数据库来处理一些记录，而不必手动创建它们。

从文档中可以看出，复制后脚本如下所示: