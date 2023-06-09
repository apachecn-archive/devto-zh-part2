# Amazon Athena vs AWS Lambda:比较两种大数据分析解决方案

> 原文：<https://dev.to/agusnavce/amazon-athena-vs-aws-lambda-comparing-two-solutions-for-big-data-analysis-dli>

大数据分析中的大多数解决方案都基于许多 AWS 服务产品，顺便说一下，它们相当多。我在一个小型开发团队中工作，在开始为我们公司的大数据问题构建解决方案之前，我们没有时间，也没有经验去尝试所有的方法。

我们决定尽快解决这个问题，而不是在每个服务上花费令人痛苦的时间。我们首先部署了一个只包含 AWS Lambda 架构的解决方案。知道有其他方法可以做我们已经做过的事情，我们更进一步，用 Amazon Athena 做了实验。我们和他们一起学习和工作了几个星期。我们部署并测试了它们，所以我们知道哪一个最适合我们。

因此，我想分享我在学习、开发和使用这两种架构方面的经验——一种仅使用 AWS Lambda，另一种使用 Amazon Athena 架构。

这个故事将更多地关注开发过程，重点放在项目本身——不给出关于它的每个细节，而是从整体上探索开发。我还想让你知道两者的不同和见解。

### 资金和时间的巨大制约

我们的项目需要在尽可能短的时间内投入生产，尽可能节省资金。

项目要求相当简单:

*   一个平台，它分析来自路由器的日志，然后对信息进行汇总，以确定某个设备是否可以被视为访问者或路人。

*   除了数据处理，我们不想为其他任何东西付费。

*   我们想要一个易于部署、自我调配的解决方案。

### 言归正传

该产品需要在以下方面投入大量时间:

首先，我们必须研究、实现和权衡哪种架构最适合我们的问题。同样，我们必须学习我们不知道的技术。

AWS Lambda 对我们来说非常熟悉，但由于 Amazon Athena 相当新，所以我们不得不动手尝试这个工具。

我们的团队在使用无服务器开发应用程序方面经验丰富——所以我们知道整个 Lambda / SNS / S3 服务的来龙去脉，并使用 CloudFront 部署它们。

但是这个挑战是新的。我们必须分析大量的路由器数据，以及与之相连的设备的大量信息，所有这些都要在严格的执行时间表内完成。

### 直面问题

这是我们的解决方案必须实现的任务模式:

1.  外部应用程序每分钟都会将文件上传到预先配置的位置。

2.  我们的应用程序以 10 分钟的间隔检查这个文件位置，并逐个处理当前存在的所有文件，将所有信息合并到一个文件中。

3.  在成功处理文件之后，我们必须从路由器所在位置的过路人和访问者那里获得统计数据。

4.  与此同时，我们不仅希望获得 10 分钟间隔的信息，还希望将信息聚合成一些所需的间隔，如 1 小时、8 小时、1 天等。

### 首先，我们使用我们所知道的东西——逻辑上

只有我们有一些确定性，AWS Lambda 有效——我们以前使用过。

我们知道，如果您使用 AWS Lambda 进行处理，您只需要为实际的处理时间付费，而不需要为空闲时间付费。如果你使用 AWS S3 进行文件存储，你必须为文件的大小和数据的移动付费——这也是一个昂贵的部分。考虑到这一点，我们开始计划。

[![Serverless batch file processing application architecture](img/ebf8897d6dae4deee0e7597cc3374559.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JgPgD2lZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3584/1%2AH__2qWrSs1d4Ik-VRq4cKQ.png)

上图大致显示了我们如何集成 AWS 组件来构建我们的解决方案:

*   一个 [CloudWatch](https://aws.amazon.com/cloudwatch/) 预定事件被配置为以 10 分钟的间隔触发 lambda 功能。

*   一个 Lambda 函数，在所有不同的时间间隔内充当调度程序。当需要批处理时，它会发送一个 [SNS](https://aws.amazon.com/s3/) 通知。

*   一个 [S3](https://aws.amazon.com/s3/) 桶中的一些文件夹被提供来存储原始的和处理过的信息。

*   一些 [SNS](https://aws.amazon.com/sns/) 主题被配置为向其发布处理通知。

*   这些函数被编程为具有必要的权限，可以从 S3 存储桶中读取文件，对它们进行处理，并最终将它们再次发送到 S3。

### 胜负

起初，我们对我们在这个实例中构建的东西非常满意——但是我们知道我们可以做得更好。此外，在再次查看该解决方案后，我们发现它有一些限制，其中一个最大的限制是文件大小和 Lambda 文件存储限制。

我们知道我们有大量的数据，这使得 Lambda 的实例数量很大，然后转化为时间量。正如我之前所说的，我们项目的最大限制之一是尽可能地节省资金——但我们知道这并不是正在发生的事情。

此外，我们需要管理一个相当大的架构——这一点同样重要。

为了改善这一点，我们尽可能地进行了并行化，我们调整了我们的算法，但我们认为可以以更低的成本做得更好。

因此，在探索 AWS 服务的过程中，我们偶然发现了亚马逊雅典娜的繁荣。

### 开始方向盘

Amazon Athena 是一个无服务器的、基于 SQL 的查询服务，用于存储在 S3 的对象。要使用它，您只需定义一个指向您的 S3 数据文件的表，并启动 SQL 查询！这在 Lambda 函数中设置起来相当容易。

但是，如果我们仍然必须使用 Lambda 作为处理数据的手段，那又有什么区别呢？

雅典娜的价格模式带来了颠覆；您只需为每次查询扫描的数据量付费，仅此而已。Athena 按扫描的每 TB 数据收取费用，最低收费为 10 MB。而 Lambda 定价模式是每 100 毫秒计算收费。

我们有很多信息要处理，我们有很多 Lambda 函数用于我们必须处理的每个文件。这意味着我们在 Lambda 处理上积累了大量的时间。

这就是我们知道必须充分利用它的地方，因为 Athena 不按查询运行的时间收费，只按处理的数据量收费。这意味着我们现在只需要一个 Lambda 来运行查询，而不是以前需要的许多——但这并不像这么说那么简单。

### 船已经再次起航了——我们知道路

我们开始研究这个方案，这是我们得到的架构:

[![](img/3e589d49575388a3265b872eb8f2c8bb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--c4EXIphF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3584/1%2AAZs8TbkHK-_L6ENRTKY98Q.png)

这是我们以前的架构中的一个重大变化。我们能够看到，我们可以利用阶跃函数的优势，使我们的解决方案更易于管理和供应。我们改进了我们想要的两个基本方面——资金和解决方案的供应。

让我们也深入了解一下阶跃函数:

[![Step Functions Diagram](img/5c5f4f52eb8a8b3afc2a13ce05008488.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bR5_5ngm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A_ZTY6uq3O68nKY7qFeI1fg.png)

所以让我们稍微解释一下这个方案。首先要知道的是，如果您使用 SDK 连接到 Athena，那么对服务的调用是异步的。这意味着，如果你想在一个 lambda 函数中进行查询，你必须发送它，但你不会立即收到答案。应该要求 Athena 查看信息的处理情况。

为了减轻这种情况，我们必须添加一些中间决策步骤，在这些步骤中，需要等待一定的时间，以便给 Athena 时间来完成处理。万一 Athena 没有处理完信息，它会再次等待这个时间再次询问。

这里我们可以看到这种模式的第一个好处，在我们使用的 lambda 中，我们只需要向 Athena 发送一条消息来开始查询，然后 Athena 会做所有的工作。这就是改进的基础，没有很多 Lambdas 来处理文件，而是一个发送请求并进入睡眠的 Lambdas。

其他部分并不比之前复杂多少。作为第一个架构，这个过程从一个解析任务开始，以便为 Athena 查询准备好文件。这可以通过爬虫来完成，使用 AWS Glue 来转换数据，以便 Athena 可以查询它。我们用来降低成本的另一种方法是通过 Athena 查询来创建分区。

[![Data Transformation](img/128b55c2faa3e104884823ef49355478.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--V7Q0C8wZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AKRZfpF-C7M6Z0h7WOu3vNQ.png)

做完这些，数据分析就开始了。这是 Lambda 函数调用 Athena 并请求已处理数据的地方。如前所述，这是针对不同的时间段完成的，只是增加了等待时间以及重试和错误的逻辑。

最棒的是，如果你知道基本的 SQL，你可以做令人惊奇的查询。

### 增强

随着我们开始学习和研究，我们意识到有更多的方法使性能更加优化。

所以我想和你们分享其中的一些:

压缩—*因为数据总是可压缩的，压缩数据意味着减少数据量。

列式数据格式——正如 AWS 所建议的，您可以将数据转换成 parquet 格式，这大大减少了运行查询的数据量。

缓存——你不想一遍又一遍地重复同样的查询，这样你就可以开始在 S3 数据湖中系统地存储和分类结果。

一起运行查询——为了更便宜，你可以开始将多个查询串在一起运行，然后在 Lambda 上将它们分开，然后再将它们发送回 S3。Athena 最多设置 10 个并发查询。这就是为什么最好在一次中执行更多的查询。

### 最后的想法

经过这一切，我们决定将 Amazon Athena 解决方案部署到生产环境中。正如你可能已经看到的，在整个过程中，我们发现，当我们与雅典娜一起工作时，许多好处显现出来。

我们认为我们达成了一个健壮且可扩展的解决方案。此外，使用具有 Athena 优势的架构更具成本效益。

既然您已经看到了两种不同的架构是如何实现的，我希望您可以亲自尝试一下，并对您日常使用的架构进行评论。我们是一个不断成长的团队，渴望从每一次经历中学习。

因此，如果你对我们所做的有任何疑问，我希望在下面的评论中听到你的问题和反馈。

**感谢阅读！如果你喜欢的话，一定要为它鼓掌！**

**想关注就关注我: [Linkedin](https://www.linkedin.com/in/anavcevich/) ，[Twitter](https://twitter.com/agusnavce)T5】**