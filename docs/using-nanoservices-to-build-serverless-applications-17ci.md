# 使用纳米服务构建无服务器应用

> 原文：<https://dev.to/tmclaughbos/using-nanoservices-to-build-serverless-applications-17ci>

[![nanoservices.png](img/03c417ae48f6d1aec850da219fb3d9f7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bmdOCI4R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.serverlessops.io/hs-fs/hubfs/blog/nanoservices.png%3Ft%3D1521502962558%26width%3D800%26height%3D419%26name%3Dnanoservices.png)

[AWS 无服务器应用程序库(SAR)](https://aws.amazon.com/serverless/serverlessrepo/) 的[最近的一般可用性状态](https://aws.amazon.com/blogs/aws/now-available-aws-serverless-application-repository/)代表了一个里程碑，并促进了我们在未来几年如何构建应用程序的重大飞跃。SAR 允许我们发布可重用的领域逻辑——纳米服务——我们可以用它来组成我们自己的功能应用程序。有这种想法的不止我一个人。在过去的几周里，我与无服务器领域的许多人进行了交谈，或者看到他们各自得出了类似的结论。

> ![Jared Short profile image](img/c01b975ff10f6176c6d93ed2b4c042d6.png)Jared Short[@ shortjared](https://dev.to/shortjared)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)我们正在接近可组合服务的理论，比我想象的要快得多。例如，我们需要 appsync 的使用限制。我们只要把 APIG 放在 appsync 前面就行了。绝对疯狂。无服务器==可组合服务。2018 年 3 月 17 日下午 17:24[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=975060195264131074)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=975060195264131074)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=975060195264131074)

液体错误:内部

人们开始想要搜索领域逻辑，无论是开源还是内部源代码，就像他们使用 GitHub、NPM、PyPi 等一样。实现轻松搜索高级代码的能力，以最少的额外编码工作组装成可用的应用程序，将对我们在环境中交付新服务的速度产生重大影响。

## 什么是纳米服务？

让我们从什么是纳米服务的定义开始。你可能会看到这个术语，认为它只是一个被分解成非常小的块的微服务。有了这样一个宽松的定义，你最终只会对尺寸吹毛求疵。相反，让我们指定一些更明确的特征。

纳米服务是:

*   可部署
*   可重复使用的
*   有用的

纳米服务的可部署性意味着它还包含自己的基础设施定义，工具可以使用这些定义来部署服务。这个定义处理纳米服务边界内的每一个资源。在其边界，可以告诉纳米服务在哪里找到另一个纳米服务，例如，帮助它在另一个纳米服务中找到将产生触发的事件源的参数值，并且在另一端，它将导出资源信息以允许另一个服务使用它，例如，另一个纳米服务可以使用的事件源名称。

纳米服务应该是可重用的。它不一定与其当前的用例相关联。然而，鉴于最后一个特征，这个特征可能具有挑战性，但是除了构建不同应用程序的想象力之外，没有什么本质上阻止它被重用。

最后，服务是有用的。特别是，它有解决问题或做某事的领域逻辑。这使它不同于一般的软件库。Boto 3 是一个库，它给了你与 AWS 交互的能力，比如获取 S3 对象。另一方面，当被触发时，纳米服务知道获取什么对象，数据的格式以便解析它，并发布该数据以便另一个纳米服务可以使用它。一直以来，处理平凡的细节，比如错误处理。对我来说，有用性是纳米服务的最大区别。一个人只需要部署并可能将其与另一项纳米服务相结合，就能看到价值。

我没有列出的两个特征是“有用”和“只做一件事”。一项纳米服务可能可以独立使用，但也不是必须如此。试图让一项纳米服务独立可用可能会妨碍它的可重用性，违背初衷。此外，我不想争论提供获取、设置和搜索数据存储对象的一组函数是三个纳米服务还是一个提供数据存储接口的纳米服务。只需运用你的最佳判断并相应地组织你的代码。

纳米服务比软件库更复杂，但没有一般的微服务复杂。最后，你应该能够将纳米服务组合在一起，形成一个可用的应用程序。纳米服务相当不同，足以成为它自己的东西，并保证它自己独特的术语，而不是重用其他通用术语。

## 我发现纳米服务的道路

在构建一个小型应用程序来监控 AWS [成本和使用报告](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage.html)时，我开始意识到纳米服务的力量，我还希望开源这个应用程序，以便其他人可以使用它。这项服务被称为[应用成本监控](https://serverlessrepo.aws.amazon.com/applications/arn:aws:serverlessrepo:us-east-1:641494176294:applications~ApplicationCostMonitoring) (ACM)。

有一段时间，我一直着迷于 Lambda 基于消费的计费将会有多大的变革。

> 徐:我不明白为什么按功能计费这么重要。
> 
> 我:在 2007 年，许多人不明白为什么计算作为一种工具(即 EC2)如此重要。他们认为这只是更便宜的服务器。他们忽略了整个要点。你在无服务器上做同样的事情。
> 
> —swar dley(@ swar dley)[2018 年 3 月 10 日](https://twitter.com/swardley/status/972529016333848576?ref_src=twsrc%5Etfw)

基于消耗的计费(与基于容量的计费相比)将提供更细粒度的系统成本数据，这使我们能够为正在运行的系统的任何更改分配直接成本。几年后，我相信我们甚至会像监控当前的性能指标一样，将成本作为一个系统指标来监控。

为了便于探索这个想法，我开始构建一个应用程序，让我能够为我的帐户分析 AWS 成本和使用情况报告。该应用程序将执行以下操作:

*   由计费报告的交付触发
*   将报告分解成单独的行项目
*   并将它们写入数据存储或平台进行分析

我最初的图表大致是这样的。

[![ACM-1 diagram](img/62c958bf184441748f85ec9434aad20b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UYaim18i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.serverlessops.io/hubfs/blog/ACM-1%2520diagram.png%3Ft%3D1521502962558)

弄清楚分析平台是什么很难，但我只想停止盯着 CSV 文件，这样我就可以开始理解我拥有的数据。我最终选择了 S3 和 AWS Athena 作为开始，并且知道我可能会继续前进，我使用了一个 SNS 主题来分离读取计费报告的函数和向 S3 写入的函数。这意味着当我想迁移到一个新的分析平台时，我不必重构读取计费报告的功能。我现在有的是这个:

[![ACM-2 diagram](img/2452e7f41443c3f28c8db9946bbf1a7d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tEs3ErlL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.serverlessops.io/hubfs/blog/ACM-2%2520diagram.png%3Ft%3D1521502962558)

看着这个图，我意识到我需要将应用程序分解成更小的、独立的部分。如果用户真的发现 Athena 对他们的需求有用呢？如果他们更喜欢用不同于我选定的东西呢？我不想维护一半代码都相同的多个应用程序，或者看着其他人觉得有趣的分叉的扩散。必须有一种方法使核心有价值的代码可重用，而其他代码可以根据个人喜好进行交换和替换。

这时，我决定将我的应用程序作为独立部分发布给 SAR。老实说，我也花了一些时间来做这个决定。感觉怪怪的

## 将我的应用程序分成纳米服务

我继续将应用成本监控分解成更小的部分。我现在有两个服务:一个由 S3 事件触发，将检索账单报告、解析和发布行项目；另一个服务将向 S3 写一个行项目，以便可以用 Athena 进行搜索。

[![ACM-3 diagram](img/90f8f7147169acb8c18ea5900b1127a4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lwQ6tRpX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.serverlessops.io/hubfs/blog/ACM-3%2520diagram.png%3Ft%3D1521502962558)

*applicationcostmoning*不能单独使用。但是它是有用的、可部署的和可重用的。

> 那又怎样，我能在一小时内写完吗

不，你不能。该服务包含通过研究 AWS 成本和使用情况报告、实验和测试所学到的知识。我学到的很多东西都没有被 AWS 记录下来。我完全是偶然发现的。虽然服务的代码可能很简单，但它是研究和理解特定问题领域的产物。这是看不见的努力和劳动，通常我们很难在工程中反映出来。

*applicationcostmoning*包含理解 AWS 成本和使用报告特性的领域逻辑。生成的成本和使用报告在一个计费周期内是累积的，并且由于报告的大小，*applicationcostmoning*跟踪已经发布的记录，以便报告能够以一种及时且成本较低的方式运行。该服务还知道报告行项目没有完全按时间顺序排序，所以它使用最近看到的日期而不是报告位置来计算下次运行时在哪里重新开始。它还知道总是从每月的第一天开始重新处理这些项目，因为这些项目在整个月中会发生变化。

该服务还允许您处理报表架构更改。这些是通过对报告中跟踪的标签进行更改而发生的。这些模式更改可能会导致问题，例如 Athena，并使您的数据无法搜索。此外，更改标签并最终更改模式将导致为报告中的部分费用(而非全部费用)生成新的行项目 id。所有这些都有文档记录，您可以决定将服务配置为以最适合您需求的方式运行。

这种服务的价值不是因为代码，而是因为它解决问题的能力和包含在代码中的问题空间的知识。有些人不需要经历我已经经历过的事情来正确地分析他们的 AWS 成本和使用报告；他们可以用我的作品。这就是我们在构建应用程序和系统时的趋势，可重用的领域逻辑来解决问题。

## appre po 如何促成纳米服务和无服务器？

AWS 无服务器应用程序存储库的普遍可用代表了使用纳米服务进步的一个重要时刻。它开始帮助解决该领域最大的问题之一:纳米服务的可发现性。有人怎么知道*applicationcostmoning*存在，并且已经解决了他们的一个问题？这就是为什么我在 SAR 上发布了*应用成本监控*。

[![ACM-SAR](img/46a102d7a062416815e42a8e0b452f58.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Iweayl-s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.serverlessops.io/hubfs/blog/ACM-SAR.png%3Ft%3D1521502962558)

在向 SAR 发布了*applicationcostmoning*和多个 publisher nanoservices 之后，其他人现在可以找到它们，并组成自己的系统来分析他们的 AWS 支出。人们可以使用它们来编写符合他们需求的应用程序。更令人兴奋的是，他们可以使用我的纳米服务来构建我从未想过甚至想象过的应用程序，他们可以自由地将更多的时间和精力放在我没有想到的事情上，而不是解决我已经解决的问题。不是重新发明轮子，如果你愿意的话。

*S3 出版商和雅典娜的应用成本监控*

[![ACM-5 diagram](img/6e091b3ec756a08288d88faddbca9c5c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Mw7BQHbY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.serverlessops.io/hubfs/blog/ACM-5%2520diagram.png%3Ft%3D1521502962558)

*利用 DynamoDB 支持 web 应用的应用成本监控。*

[![ACM-7 diagram](img/87f327798c4da3ed682c797f331dd52b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Eog02ry3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.serverlessops.io/hubfs/blog/ACM-7%2520diagram.png%3Ft%3D1521502962558)

我也不是唯一看到纳米服务力量的人。参见 AWS 开发者 James Hood 的[AWS-server less-Twitter-event-source](https://serverlessrepo.aws.amazon.com/applications/arn:aws:serverlessrepo:us-east-1:077246666028:applications~aws-serverless-twitter-event-source)和最近[关于发布和部署无服务器应用的 Twitch 广播](https://www.twitch.tv/videos/239114858)。在接下来的几周，我计划使用*AWS-server less-twitter-event-source*来构建一个 Twitter 机器人。这是一个与他的排行榜应用程序完全不同的应用程序，我可以花更多的时间关注我的应用程序与他的应用程序的区别，而不是弄清楚如何使用 Twitter 搜索 API。

这是一个激动人心的时刻；我们即将看到构建应用程序的新方法。

> 旧的面向对象世界的问题之一是没有有效的交流机制来公开已经构建的东西。你经常会在一个公司内发现对象和功能的重复，更不用说在公司之间了。同样，公开为 web 服务会促使这种情况发生改变。 **假设某人有意识地建立一个发现机制，比如服务注册。***-[西蒙·沃德利](https://hackernoon.com/why-the-fuss-about-serverless-4370b1596da0)*

 *## 构建明天的无服务器应用

通过组合有用的领域逻辑的可重用纳米服务来构建应用程序是新的，我们离成为主流还有很长的路要走。纳米服务本身也受到我们现有工具的限制。我们有构建无服务器应用的工具和构建无服务器纳米服务的工具。我们还没有从纳米服务中构建应用的工具。SAR 是一个开始，但我们仍然缺乏良好的依赖性管理，能够向我们显示纳米服务和应用程序所有组件之间关系的工具，以及跟踪依赖性变化并防止我们在不知情的情况下部署突破性变化的工具。SAR 目前也有其自身的局限性。它只支持 AWS 资源的[子集，并且存储库仍然很小。](https://docs.aws.amazon.com/serverlessrepo/latest/devguide/using-aws-sam.html)

然而，有人对用纳米服务编写应用程序非常感兴趣，并致力于实现这一目标。

[![SAR-Slack-Comment](img/ed35fccf5e0b583f2ee5797480017cde.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MqkCUMyD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.serverlessops.io/hubfs/blog/SAR-Slack-Comment.png%3Ft%3D1521502962558)

我们现在所看到的是未来许多年我们将如何构建应用程序的戏剧性转变的开始。

*这最初出现在 [ServerlessOps 博客](https://www.serverlessops.io/blog)上。请访问以阅读更多我们的作品！*

[![](img/aaf319811b1705c22978d785c48c2c26.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OM0s4blz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://track.hubspot.com/__ptq.gif%3Fa%3D277116%26k%3D14%26r%3Dhttps%253A%252F%252Fwww.serverlessops.io%252Fblog%252Frise-of-the-nanoservice%26bu%3Dhttps%25253A%25252F%25252Fwww.serverlessops.io%25252Fblog%26bvt%3Drss)*