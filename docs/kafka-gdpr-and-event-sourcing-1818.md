# 卡夫卡、GDPR 和事件采购

> 原文：<https://dev.to/danlebrero/kafka-gdpr-and-event-sourcing-1818>

*图片归属:[他不是这个安格尔](https://flickr.com/photos/17193322@N03/22835450305)作者[艾莉森·米克尔](https://flickr.com/people/17193322@N03)。*

你可能已经知道，欧盟已经批准了一项名为 GDPR ( [通用数据保护条例](https://en.wikipedia.org/wiki/General_Data_Protection_Regulation))的法案，让我们可以重新控制自己的个人数据。

从技术的角度来看，如果你已经买了事件源和卡夫卡，这是特别感兴趣的 GDPR 的“擦除权”(又名。忘记你所知道的关于我的一切)，因为这与不会忘记任何事情的不可变事件日志的想法不一致。

要在事件源架构中处理 GDPR，以下是最有趣的选项:

1.  从预测中删除数据可能就足够了。Michiel Rook 的 [博客](https://www.michielrook.nl/2017/11/event-sourcing-gdpr-follow-up/)给出的一个建议是，maybe 足以从预测/读取模型中删除数据，没有必要接触事件存储中的数据。如果这个选项在法律允许的范围内，那么“擦除权”就变成了投影需要处理的另一个事件。非常适合活动采购。

2.  删除/更新 Kafka 消息: [Ben Stopford](https://twitter.com/benstopford) [提醒我们](https://www.confluent.io/blog/handling-gdpr-log-forget/)在 Kafka 中，如果您使用压缩的主题，您可以“删除”和“更新”消息，这意味着为了遵守“擦除权”，我们需要找到用户的所有事件，并为每个用户发送一个具有相同密钥(事件 id)和空(或更新)有效载荷的新消息。

    这种方法的主要问题是事件存储不再是不可变的，所以在其他非 GDPR 的情况下使用相同的漏洞是非常诱人的。

3.  加密:来自 [Michiel 的博客](https://www.michielrook.nl/2017/11/forget-me-please-event-sourcing-gdpr/)的另一个建议是用一个密钥为特定用户加密所有的消息，当用户想要行使其“擦除权”时，我们只需要忘记加密密钥。

    这种方法在密钥管理方面的问题是。用 Michiel 的话说:“存储、查找和检索正确的加密密钥...在规模上变得特别有趣”。因为这很有趣，所以让我们深入探讨一个可能的解决方案。

## 高可用、高可伸缩的 RESTful 密钥管理服务

同步 HTTPS？真的吗？

## 卡夫卡之道

假设您已经将数据存储在 Kafka 中，并且 Kafka 能够大规模处理数据，为什么不使用 Kafka 本身来存储和检索加密密钥呢？

让我们先来看一下我们的架构会是什么样子:

[![Kafka GDPR encryption architecture](img/3a7b0344a1c3f58be26202744ba3de09.png "Kafka GDPR encryption architecture")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mEQC3SEL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://danlebrero.cimg/blog/kafka-gdpr/kakfa-gdpr-encryption-architecture.png)

您的`Event Producer`是您的常规服务，它将未加密的数据推送到某个`To-Encrypt`主题。

为了遵守 GDPR，这个主题将有一些合理的短时间保留策略，以便 Kafka 在该时间后删除数据，但请记住，保留期应该比您预计的`Encryptor`服务停机时间长，如果`Encryptor`服务停机时间更长，Kafka 可能会在数据被加密并安全存储在`Encrypted-Data`主题中之前删除数据。

服务将负责加密任何消息，并为新用户生成新的加密密钥。它利用 Kafka Streams 状态管理为每个实例拥有的分区保存加密密钥的本地副本，因此查找加密密钥最多是一次磁盘搜索。

该应用程序还必须通过从其状态中删除加密密钥的本地副本，以及从`Encryption-keys`主题中删除加密密钥，来对用户行使其被遗忘的权利做出反应。

`Encrypted-Data`主题是永久存储事件的地方，没有保留策略。这是您的事件日志。

`Encryption-Keys`主题将是一个压缩的主题。当需要忘记用户时，`Encryptor`服务将发送一个墓碑来覆盖用户的加密密钥，因此它将永远丢失，没有人能够再次解密它的数据。

为了解密数据，`Event Consumer`基本上需要将`Encrypted-data`主题和`Encryption-Keys`主题连接起来。同样，我们将依靠 Kafka Streams 状态管理来保存加密密钥的本地副本。

与`Encryptor`类似，`Event Consumer`将需要在用户请求被遗忘时做出适当的反应，通过删除本地加密密钥和与该用户相关联的任何其他状态。

从这座象牙塔上看，这座建筑美极了。

[![ivory tower](img/a9232aba81520944dd20214f514b2acd.png)](https://www.pedabe-concept.de) 

*形象归属:[象牙塔](https://www.pedabe-concept.de)作者[彼得·巴特尔](https://www.pedabe-concept.de)。*

### 实现细节

想弄脏手的话，实现细节[这里](http://danlebrero.com/2018/04/11/kafka-gdpr-event-sourcing-implentation/#content)。

## 结论

总之，我们遵守 GDPR，因为我们的`to-encrypt`主题有一个基于短时间的保留策略，我们的加密密钥在一个压缩的主题中，我们的事件日志用一个针对每个用户的加密密钥加密。

此外，我们的应用程序必须处理一个新的“忘记我”事件类型，并删除它们可能存储的任何 PII 数据。

正如我们所看到的，实现并不是火箭科学，但它提出了一些更多的挑战:

1.  我们是加密整个消息还是只加密一部分？如果它只是一个子集，我们如何处理模式？如果不是一个子集，我们会丢失所有的数据，甚至是非 PII 的数据。
2.  我们可以为多个主题重用同一个加密器吗？如果是这样的话，题目一定要分。如果不是，我们将需要从加密器中分离出密钥生成，这样就可以重新分配加密密钥。
3.  即使解密对消费者是透明的，它仍然需要处理“忘记我”特例。
4.  您需要选择一种足够快且足够安全的加密算法。你能承受每封邮件增加 1 或 10 毫秒的处理时间吗？理论上，如果消费者是最新的，它总是可以直接从 to-encrypt 主题中消费。
5.  米歇尔博客中的一条评论指出，忘记钥匙是不够的。每隔几年，我们还需要[更新加密算法](https://www.keylength.com/en/)，这意味着我们需要再次加密一切。

因此，在 Kafka 中使用加密来处理事件源数据似乎是可能的，但是它比其他选项更好吗？当然，这比从预测中删除数据更糟糕，如果这是一种选择的话。但是，这是否比 Ben Stopford 建议的仅仅使用压缩主题来存储事件日志要好呢？

你有多重视不变性？那么多？！？！那么小？！？！