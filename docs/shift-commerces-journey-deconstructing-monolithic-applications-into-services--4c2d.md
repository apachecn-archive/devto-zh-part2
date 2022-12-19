# 转变商务之旅:将单一应用程序解构为服务

> 原文：<https://dev.to/ryantownsend/shift-commerces-journey-deconstructing-monolithic-applications-into-services--4c2d>

*这篇文章最初出现在 [Heroku 的工程博客上。](https://blog.heroku.com/monolithic-applications-into-services)T3】*

自从软件出现以来，软件架构就一直是一个争论不休的话题。这一长期讨论的最新迭代是在单片和微服务之间——大型自含式应用程序与集成在一起的多个小型应用程序——但在我们的架构理念的表面之下存在一个更大的问题:为什么这很重要？

虽然我们做出的架构选择可能对最终用户是隐藏的，但他们最终会受到影响。这种影响不仅体现在面向用户的应用程序的性能、可扩展性和可靠性上，还体现在对我们业务未来的更深层次的影响上——创新能力取决于开发人员的工作效率、资源的可用性和治理/流程开销。虽然没有什么灵丹妙药可以让你的架构“正确”(抱歉！)，有适合你的业务的“正确的”——随着公司及其需求的变化，这种“正确的”会随着时间的推移而演变。关键是我们要不断问自己:我们做的事情对吗？否则，在我们意识到之前，我们的竞争对手已经领先了。

考虑到“为什么”,我想多谈一点我们在 SHIFT 构建架构时考虑的一些差异。

# 独石是这么坏的东西吗？

在我们甚至开始考虑我们是否应该打破一个庞然大物之前，我们应该首先确定我们试图解决的问题，而不是在不了解更好的架构(也许通过微服务实现)对您来说实际上是什么样子的情况下就贸然拆除地板。

低效的架构有两个方面被不公平地归咎于独石:糟糕的结构代码；可扩展性差。事实上，你绝对可以拥有一个分解良好、可维护的整体代码库——[base camp 是这个](https://m.signalvnoise.com/the-majestic-monolith-29166d022228)的支持者——如果我们诚实地看待我们的应用程序的规模，我们中很少有人在突破优化或基础设施的界限。

糟糕的软件架构不是一个可以用微服务神奇解决的问题；这只是将问题从一个结构不良的代码库转移到许多代码库。正如著名开发人员西蒙·布朗敏锐地指出的:

> “如果你不能构建一个结构良好的整体，你凭什么认为微服务就是答案？”

# 为什么要考虑微服务？

好了，是时候保持乐观了——有一些非常好的理由转向微服务。虽然我们提到了几个领域可能是远离整体架构的错误标志，但如果您现在或将来遇到这些问题中的任何一个，微服务可能适合您:

## 1。通信开销阻碍了生产力。

当公司成长时，即使非常小心，沟通也可能成为生产性开发时间的不断增加的消耗。如果你开拓出一个完整的功能独立区域，你可以给团队自主权，减少沟通/组织开销！

我们用订单管理套件做到了这一点，这个领域具有足够的复杂性和广泛的功能，足以让一个团队忙碌起来！

## 2。专横的治理和高风险的部署

独石将关键任务功能与完全可以忽略的功能集于一身。鉴于 SHIFT 运营着一个企业级平台，我们必须非常小心地管理某些领域，但我们不希望让这妨碍我们在其他领域的灵活性。

这方面的一个例子是我们的管理面板，对于它来说，中断(计划内的或其他的)或错误比购物车在我们客户的网站上停止工作的影响要小得多！

## 3。语言或基础设施限制

您可能会从 monoliths 中挤出比您预期的更多的可伸缩性，并且验证您不仅仅是将成本从基础设施转移到您昂贵的开发和支持工程团队是非常重要的，但是有些时候使用其他技术更有意义，例如 Node.js 或 Go 对于缓慢的请求处理比 Ruby 更有效。

在 SHIFT 中，我们提取了我们的集成系统，该系统对外部 HTTP 服务进行了许多不同的调用。使用 Node.js 让 Ruby 处理更一致的请求模式。

# 什么是胶水？

无论你如何分割你的应用程序，微服务成功的一个普遍原则是保持应用程序之间的解耦。否则，你肯定会面临一连串的不利因素。有些人认为这一结果具有讽刺或幽默的意味:

> ![Honest Status Page profile image](../Images/4249a29d6e1738678297240090f953ab.png)Honest 状态页面@ Honest _ update![twitter logo](../Images/4c8a2313941dda016bf4d78d103264aa.png)我们用微服务取代了我们的 monolith，这样每一次宕机都可以更像一个谋杀之谜。2015 年 10 月 07 日晚 23 点 10 分[![Twitter reply action](../Images/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=651897353889259520)[![Twitter retweet action](../Images/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=651897353889259520)[![Twitter like action](../Images/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=651897353889259520)

要求实时、一致地访问由另一个服务管理的功能或数据是导致灾难的原因——因此我们寻找清晰的隔离线，并异步传递其余数据。这避免了一个服务调用另一个服务、另一个服务调用另一个服务等等的间接性。

这就引出了一个问题:*什么应该生活在中间并帮助服务通信？*

有很多消息代理可以使用，比如 ActiveMQ 和 RabbitMQ，但是 SHIFT 选择了托管的 [Apache Kafka 服务](http://www.heroku.com/kafka),原因如下:

*   消息是按时间顺序排列的，并且保证传递。我们希望所有的服务都能对整个系统有一个准确的了解。
*   耐用性、弹性和性能都强得令人难以置信。我们对企业系统负责，避免停机、数据丢失或任何形式的服务降级至关重要。
*   Heroku 消除了操作上的痛苦。我们相信做我们擅长的事情，并在适当的时候依靠他人的专业知识，以保持我们的精干和敏捷。

Apache Kafka 将自己描述为一个“分布式提交日志”或一个“事件流”，你可以将它看作一个按时间顺序排列的事件列表，这些事件被分解成多个流，称为“主题”。

您的应用程序可以连接到一个或多个主题，发布新事件和/或按顺序消费事件。这些事件可以是简单的动作通知，也可以携带状态变化，允许每个服务维护自己的数据集。

使用 Heroku 开发人员提倡的事件流(如 Kafka)还有一大堆其他好处， [Chris Castle](https://twitter.com/crc) 和我在 Dreamforce 2017 的演讲中谈到了这些好处——[点击这里查看录音。](https://www.salesforce.com/video/1773568/)

# 我们迁移到 Kafka 驱动的服务的步骤是什么？

众所周知，“大改写”是应该尽可能避免的事情，所以最终我们需要风险小得多的东西。

我们达成了一个逐步提取服务的过程。这甚至允许在完全提交之前评估微服务是否是正确的选择，因为我们不会删除现有的实现或发送生产流量，直到最后一步。

这是我们的八个步骤:

## 步骤 1:向 Monolith 添加生产者逻辑

这可能会让你吃惊，但是步骤 1 实际上是在我们的 monolith 上添加了更多的代码！您通常需要访问新服务中的数据，所以第一步是通过 monolith 中的 Kafka 生成器将数据推送到 Kafka。

[![Step 1](../Images/0a8d6be2ae6b29cb6f2580488b93f121.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dVLYUwXD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://heroku-blog-files.s3.amazonaws.com/posts/1520536183-Step-1.jpg)

## 第二步:将流消费到数据库中

在我们开始提取逻辑之前，我们将准备我们的数据存储，这将使我们的服务与生态系统的其余部分分离。这个新服务最初只是包含一个消费者和数据存储，允许本地访问状态。根据您的应用程序，您可能需要通过执行数据迁移来回填历史数据。

[![Step 2](../Images/c2132423b81ecad4ae3a61e921b85daa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jF9wLwTr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://heroku-blog-files.s3.amazonaws.com/posts/1520536202-Step-2.jpg)

## 第三步:测试消费者

验证您的新服务正在无错误地处理事件，并且消费者没有在事件流上落后太多。

Kafka 旨在处理消费者在一段时间内的延迟-它将事件持续一段时间(在 Heroku 上为 2-6 周)，因此您无需担心数据丢失，但这将意味着您的服务具有过时的数据视图。

Kafka 在你的系统处于高负载时充当缓冲器的能力是它的一个伟大特性，因此值得理解并为你的消费者落后的情况做准备。

## 第四步:复制你的逻辑

从你的 monolith 中提取相关代码到上面的新应用程序中。通过手动执行过程/ API 调用，测试它是否可以独立工作。它应该从新的数据存储中读取数据，而不处理任何生产 API 调用。

警惕可能触发电子邮件或产生其他意外副作用的功能-您可能需要禁用一些外部呼叫。

[![Step 4](../Images/8a13e81c11e8385dbaef23c1e0228e98.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--A9Z2eOHv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://heroku-blog-files.s3.amazonaws.com/posts/1520536222-Step-4.jpg)

## 第五步:添加、测试和消费事件触发器

向 monolith 添加一个特性 toggle，该特性将只向新服务的一部分帐户发送可操作的事件(显然这将取决于您的应用程序如何工作，它可能基于用户/团队/公司),并实现这些事件的生产者。

你可以通过跟踪卡夫卡，确保你期望的事件流过，来检查这些是否有效。一旦您满意了，就将消费者添加到您的服务中，该服务将处理这些事件，执行服务中的相关过程。

[![Step 5](../Images/5050fbbca928386c010f2b9a54c0e2b5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_N9s3j3e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://heroku-blog-files.s3.amazonaws.com/posts/1520536248-Step-5.jpg)

## 第六步:推回事件

您可能需要与 monolith 进行沟通，因此您需要服务中的生产者和 monolith 中的消费者来处理此事。

[![Step 6](../Images/2e636e819ac7794ba1724b8d01fc994f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CAz-_WVG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://heroku-blog-files.s3.amazonaws.com/posts/1520536258-Step-6.jpg)

## 第七步:测试动作事件

激活测试帐户的功能切换，并测试应用程序是否继续正常运行。

## 步骤 8:从 Monolith 中删除不赞成使用的逻辑

随着时间的推移，越来越多的帐户将启用该功能。，一旦所有帐户都在使用新服务，我们就可以完全移除特性切换，并使其成为默认代码路径。

最后，我们可以从整体中移除提取的逻辑。这可能需要一段时间，但已经安全地实现了。

[![Step 8](../Images/1f1eb701f632861c639ef839f1c03bb9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PonyH4-z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://heroku-blog-files.s3.amazonaws.com/posts/1520536269-Step-8.gif)

# 今天换班去哪里了？

我们仍在探索中，我预计今年我们将提取更多服务，但通过仔细提取少量服务，转变已经实现:

*   更多定期发布；
*   I/O 受限 API 调用的延迟更低；和
*   防止面向用户的停机。

**我给任何入门者的建议**是要非常警惕你为什么考虑微服务，不要仅仅被闪亮的技术吸引，因为你有真正的问题要解决。也就是说，我建议尝试一下卡夫卡，以便更深入地了解它能为你解决什么问题。我肯定会看看 Heroku 上的[多租户 Kafka 计划——它们起价 100 美元/月，所以对于一天一杯咖啡的费用，你可以通过一个辅助项目来探索技术。](https://blog.heroku.com/kafka-on-heroku-new-plans)

* * *

Ryan Townsend 拥有超过 15 年的网络开发经验，是 [SHIFT Commerce](https://www.shiftcommerce.com/) 的首席技术官，这是一个 SaaS 电子商务平台，为中型企业零售商带来灵活性。

他务实的观点意味着——在很少穿衬衫的情况下——他的袖子会紧紧地卷起来:即使作为一名高管，他最喜欢的地方也是在他的团队最忙碌的时候。

在办公室之外，你通常会发现他在健身房拿起重物，或者从他的山地车上摔下山坡。