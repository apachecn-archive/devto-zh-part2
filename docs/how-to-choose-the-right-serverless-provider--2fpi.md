# 如何选择合适的无服务器提供商？

> 原文：<https://dev.to/jsitapara/how-to-choose-the-right-serverless-provider--2fpi>

早在 2005 年 12 月，由前 PayPal 员工领导的一家小公司成立了。**这是 YouTube** 的一个故事。

在第一个月，YouTube 每天有 800 万的视频浏览量。这仅需要少量的服务器来容纳和流式传输媒体。当流量增加、内容激增时，运营团队大幅增加了服务器数量，以应对不断增长的请求量。

如今，每个构建应用架构的云架构师都知道这种灾难的解决方案。**处理生产工作负载不再需要服务器**。在无服务器架构中设计应用程序为您提供了精确的扩展解决方案，同时最大限度地减少了操作生产环境所需的关注程度。

例如，【Bustle.com】**运营着两个独立的媒体网站，每月访问量超过 5200 万次，据报道每天处理超过 1 亿起事件**。在这种流量规模下，12 个 APIs Bustle 维护的应用程序一直处于压力之下，必须监控任何故障或中断。

这就是泰勒·洛夫将亚马逊的无服务器平台 Lambda 带到桌面上的原因。受到他们在其他 AWS 服务——kine sis 和 API Gateway——方面的经验的鼓舞，Bustle 可以使用 Lambda 轻松地将其完整的生产环境迁移到无服务器环境。习惯于管理和添加新服务器的 DevOps 团队立即从这些职责中解放出来。

Tyler Love 选择了 AWS Lambda，因为它更适合考虑 business 的要求。但是，如何选择适合您业务的无服务器平台呢？

此外，选择错误的无服务器提供商的几率很高！

> ![Paulo Lopes profile image](img/6b663dc6fa0ff2104c0d2b92cdd70660.png)保罗洛佩斯@ pm l0 pes![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)[#无服务器](https://twitter.com/hashtag/serverless)[#计算](https://twitter.com/hashtag/computing)这不是 [#vendor_lockin](https://twitter.com/hashtag/vendor_lockin) 的别名吗？2016 年 4 月 25 日上午 07:46[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=724504783973933056)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=724504783973933056)4[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=724504783973933056)5

在选择无服务器提供商时，有基于各种偏见的流行观点，然而，有些事情超出了表面水平。

举个例子，

*   语言支持和部署
*   依赖性管理
*   持久存储
*   身份和访问管理
*   触发器和类型
*   管弦乐编曲
*   并发性和执行时间
*   可扩展性和可用性
*   记录和监控
*   定价模型
*   平台性能

这里有一些东西可以帮助你: [AWS Lambda vs 微软 Azure vs 谷歌云功能:比较主要的无服务器提供商](https://www.simform.com/aws-lambda-vs-azure-functions-vs-google-functions/)。让我们考虑一下他们的技术能力，这些能力可以帮助您根据您的具体需求来权衡选择过程。

欢迎分享你的经验和建议。您为什么选择特定的无服务器提供商？在 Twitter [@jsitapara](https://twitter.com/JSitapara) 上联系，谈论任何关于无服务器、AWS Lambda 以及任何介于两者之间的东西。