# 周五爆炸#47

> 原文：<https://dev.to/horia141/friday-blast-47-33cn>

[当我谈论日志记录时，我谈论的是什么(2018)](https://hackernoon.com/what-i-talk-about-when-i-talk-about-logging-36237fad7336) -制作一个互联网应用程序有很多与“日志记录”有关的东西。这篇文章是对所有需要勾选*良好*设置的框的浏览。

[八位浮点(2018)](https://www.johndcook.com/blog/2018/04/15/eight-bit-floating-point/)——浮点不限于大多数编程语言常见的`float`和`double`类型。相反，它是一个可变精度的数字编码系统。在这种情况下只有 8 位。IEEE 和 posit 编码之间的数字分布图特别有启发性。对于常规编码，在更“常见”的音程中具有更高的密度会产生相同的效果，只是比例要大得多。

[黑盒审计——验证 MySQL 和 Redshift 之间的端到端复制完整性(2018)](https://engineeringblog.yelp.com/2018/04/black-box-auditing.html)——这一条切中要害。除了测试之外，检查系统的正确性是我的爱好之一。参见[validate](https://github.com/horia141/nvalidate)了解我在 StackOverflow 工作期间发表的一些作品。Yelp 的案例研究与此类似——他们希望确保 MySQL 中的数据与 Redshift 中的数据相同。在它的核心，系统进行大量的查询和散列比较，以找出可能的不一致情况。很值得一读。

[谷歌云平台——好的、坏的和丑陋的(大部分是好的)(2018)](https://www.deps.co/blog/google-cloud-platform-good-bad-ugly/)——GCP 一个中等规模用户对什么可行、什么不可行的概述。这也反映了我的经验，尽管我对他们的托管 Kubernetes 产品有更多的好评，但在监控方面没有太多经验。总的想法是，GCP 的服务较少，但它们可以用于多种方式(Pub/Sub 和 SQS、SNS、MQ、Kinesis 数据流、Kinesis Data Firehose、DynamoDB 流之间的比较尤为贴切)，而且它们的质量通常更高。

[硅谷如何助长一个非正式的种姓制度(2018)](https://www.wired.com/story/how-silicon-valley-fuels-an-informal-caste-system/) -关于硅谷社会分层的有趣阅读，尤其是在旧金山。一定要读一读，因为我的总结不能尽善尽美。但可以说，这是过去 20 年“经济不平等加剧”现象的另一个数据点。我想知道这种分层是否因为旧金山的单一产业结构而更加明显。因为我认为或多或少同样的事情会发生在更多元化的城市，比如纽约或伦敦。但它可能被所有的异质性所掩盖。

[斐波那契哈希——被世人遗忘的优化(2018)](https://probablydance.com/2018/06/16/fibonacci-hashing-the-optimization-that-the-world-forgot-or-a-better-alternative-to-integer-modulo/)——一种执行哈希表使用的`% N`步骤的方法，具有非常好的属性。它植根于斐波那契数列。当考虑散列函数行为时，有一个非常有趣的推理分析，并且任何人都应该知道带有数字键的 wrt 散列表的一些边缘情况，对于这些情况，“散列”可能只是`key % N`——一个非常糟糕的设置。