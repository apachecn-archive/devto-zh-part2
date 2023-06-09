# 超越分布式事务的生活:叛教者的实现-文档协调

> 原文：<https://dev.to/jbogard/life-beyond-distributed-transactions-an-apostates-implementation---document-coordination-4l8>

此系列中的帖子:

*   [一个引子](https://dev.to/jbogard/life-beyond-distributed-transactions-an-apostates-implementation---a-primer-2po6-temp-slug-8261948)
*   [文件协调](https://jimmybogard.com/life-beyond-distributed-transactions-an-apostates-implementation-aggregate-coordination/)
*   [文档示例](https://jimmybogard.com/life-beyond-distributed-transactions-an-apostates-implementation-document-example/)
*   [调度示例](https://jimmybogard.com/life-beyond-distributed-transactions-an-apostates-implementation-dispatching-example/)
*   [失败和重试](https://jimmybogard.com/life-beyond-distributed-transactions-an-apostates-implementation-failures-and-retries/)
*   [故障恢复](https://jimmybogard.com/life-beyond-distributed-transactions-an-apostates-implementation-dispatcher-failure-recovery/)
*   [传奇故事](https://jimmybogard.com/life-beyond-distributed-transactions-sagas/)
*   [关系资源](https://jimmybogard.com/life-beyond-distributed-transactions-an-apostates-implementation-relational-resources/)

[本系列的示例代码](https://github.com/jbogard/adventureworkscosmos)

快速说明-我已经更新了这篇文章，使用了更准确的术语“文档”,而不是不太准确的、DDD 特有的术语“集合”。在图像中，它仍然有旧的术语“聚合”，让我们假设我修复了所有这些图像。

在上一篇文章中，我介绍了分布式事务的一般问题，以及一些关于协调相似或不同资源之间活动的潜在想法。在很多情况下，我更喜欢简单地将我们的操作包装在一个事务中，但是随着我试图一起处理的资源越来越远(进程方面或网络方面)，这些多个资源之间的事务变得不可能。

如果我一次只能对一个资源执行可靠的事务，不管这个资源是一个数据库还是 Cosmos DB 中的单个记录，我都需要设计我的交互，以便让*业务数据*和*通信*一起处理。这意味着我的通信需要和我的业务数据在同一个事务存储中！

在 Azure Cosmos DB 的例子中，我需要将我发送和接收的信息放在我的文档中。我存储*传入的*通信，因为任何更改数据的请求都需要是等幂的。我可以有一些业务规则“所有动作必须是等幂的”，我也可以通过简单地存储传入的请求并在执行动作之前检查我是否已经处理了这个请求来实现这一点。

对于传出通信，我不能与任何其他资源进行事务性交互，所以我将传出通信存储在我能够控制的文档上。因此，我们的最终文档看起来像这样:

[![](img/171440c61572ff98c5f68d60393bfab3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--77Q-qz1M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jimmybogardsblog.blob.core.windows.net/jimmybogardsblog/7/2018/Picture0034.png)

当我执行操作时，通信和业务数据要么成功保存，要么整个操作回滚。

### 派工变更

当我发现某个操作需要影响多个资源时，我会使用发件箱与这些外部资源进行通信:

[![](img/0c15a47daed2b958ca799f055cf4108d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UA0iy2HJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jimmybogardsblog.blob.core.windows.net/jimmybogardsblog/7/2018/Picture0035.png)

然后，我可以安全地处理这个资源:

[![](img/603302663e1670be88c9afa376be02b2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yrY4aKCx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jimmybogardsblog.blob.core.windows.net/jimmybogardsblog/7/2018/Picture0036.png)

现在我已经完成了第一个操作，我可以开始与其他资源进行交互了。我使用一个调度程序来完成这项工作——它负责从资源的发件箱中读取数据:

[![](img/a15d91c9a59b02a11d8f37ffc892dcb1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nc4ltB28--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jimmybogardsblog.blob.core.windows.net/jimmybogardsblog/7/2018/Picture0037.png)

并将它们传递给正确的资源:

[![](img/7fc63afd1a43d005fee8524c7c931b7f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yN7qYoxQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jimmybogardsblog.blob.core.windows.net/jimmybogardsblog/7/2018/Picture0039.png)

每个资源接收传入的消息，并执行 2 个操作:

*   影响业务数据
*   将收到的邮件存储在收件箱中

整个操作都是事务性的:

[![](img/a3b6642652acff238014467adf7e7649.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DQVV5ipT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jimmybogardsblog.blob.core.windows.net/jimmybogardsblog/7/2018/Picture0040.png)

该资源成功后，调度程序将继续处理下一个资源。但是，此操作可能会失败:

[![](img/a2d15e29a6cf4088d187b8f81269e5fc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wq9KknDz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jimmybogardsblog.blob.core.windows.net/jimmybogardsblog/7/2018/Picture0041.png)

发生这种情况时，我们将消息移动到外部重试队列:

[![](img/87e2368db6185413a54471f246af5c9a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ucFCgJVY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jimmybogardsblog.blob.core.windows.net/jimmybogardsblog/7/2018/Picture0042.png)

此时，调度程序已经完成——但是因为我们仍然有一个失败的资源，所以我们不能从原始文档的发件箱中删除我们的事件。

### 重试次数

在我们的半完成状态下，我们需要重新处理文档的发件箱。因此，我们的调度程序从重试消息中醒来，并读取原始文档发件箱中的消息:

[![](img/684ae33174eeb10329373eac968690d6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KAkTk5Bf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jimmybogardsblog.blob.core.windows.net/jimmybogardsblog/7/2018/Picture0043.png)

调度程序再次将我们的消息分派给每个接收资源，依次处理每个资源。我们的第一个资源已经处理了这条消息，它知道这一点是因为它在更改数据之前检查了它的收件箱(使其幂等):

[![](img/db94639426df6c2b0736d471d08a9e15.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YmDDFqEJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jimmybogardsblog.blob.core.windows.net/jimmybogardsblog/7/2018/Picture0044.png)

完成后，调度程序可以继续处理(先前)失败的文档:

[![](img/9d4cc2d4ee17700c73e98fdbd40cd9c8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nZ3HbRqY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jimmybogardsblog.blob.core.windows.net/jimmybogardsblog/7/2018/Picture0045.png)

该文件现在可以成功。当然，可能会有一个 bug 或其他东西积极地阻止我们成功——所以在这种情况下，我们需要设计明确的失败策略。现在，让我们假设我们的操作将*最终*成功。一旦我们确认已经成功地将消息发送给所有接收者，我们就可以返回到原始文档并删除该消息:

[![](img/3b5445c59e3ca1a46257f2925527121c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SssRHMx8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jimmybogardsblog.blob.core.windows.net/jimmybogardsblog/7/2018/Picture0046.png)

然后将其保存在自己的单个事务中:

[![](img/a4b904d4cd89ee9ffdded819c51a0ea0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rMknyBBM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jimmybogardsblog.blob.core.windows.net/jimmybogardsblog/7/2018/Picture0047.png)

在每一步中，我们必须在更小的事务性步骤中工作，这些步骤至少可以单独重试一次，因为每个操作都是等幂的。通过将我们的通信存储在业务数据的同一个事务边界中，当我们的每个资源都不能参与该事务时，我们可以引入可靠的资源协调。

在下一篇文章中，我将在 Cosmos DB 中浏览这种模式的一些代码示例。