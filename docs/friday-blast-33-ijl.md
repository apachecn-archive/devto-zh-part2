# 周五爆炸#33

> 原文：<https://dev.to/horia141/friday-blast-33-ijl>

[测量 AirBnB 分布式支付生态系统中的交易完整性(2018)](https://medium.com/airbnb-engineering/measuring-transactional-integrity-in-airbnbs-distributed-payment-ecosystem-a670d6926d22) - AirBnB 拥有大量各种类型的支付集成——API、重定向流和良好的 ol' FTP 上传。即使在最简单的情况下，这也是一个需要一段时间来实现一致性和一些“数据完整性”概念的系统。因此，支付团队建立了许多额外的监控系统，以查看问题并生成“支付子系统健康状况”的指标。在过去的一年里，我一直在思考与此相关的问题。在许多系统中，为了确定系统的正确性，拥有一个强类型系统和一组测试是不够的。您需要主动查看系统的状态并找到问题。这可能意味着检查难以用代码表达的不变量或不能用代码简明表达的业务规则是否得到维护。

[web pack 的问题以及为什么它(有点)是我们的错(2018)](https://medium.com/@allanbaptista/the-problem-with-webpack-8a025268a761) - Webpack 是一个很棒的工具。作者指出——我同意——T2 改变了 T3 太多。这种变化是以一种非常偶然的方式发生的——有许多突破性的变化，当然也有许多变化。在我看来，这个工具开始时非常灵活，类似于 Gulp/Grunt 和 friends，但后来，随着使用它的常见模式的出现，以及生态系统作为一个整体的发展，事情变成了标准行为，而不是由插件提供。很难跟上主要版本的变化。希望事情会平静下来，人们会减少工具一些松弛。

* * *

以下是一个 5 篇文章的系列，介绍了构建分布式日志 a la Kafka 的设计过程。

[Storage mechanics(2017)](https://bravenewgeek.com/building-a-distributed-log-from-scratch-part-1-storage-mechanics/)——涵盖了问题和设计空间，并展示了分布式日志中的*节点*将如何工作。我的一个问题是通过`fsync`或类似的机制来确保写操作的持久性。我只是认为这是一个遗漏，但第 2 部分澄清了这一点。

[数据复制(2017)](https://bravenewgeek.com/building-a-distributed-log-from-scratch-part-2-data-replication/)——处理将数据从单个节点复制到多个节点。主要是为了冗余。问题`fsync`以一种很好的方式*解决了*。通过复制到不同的机器来确保持久性，而不是确保写入到磁盘是持久的。这既*更安全*，*更快*又实际可行。磁盘因其为获得性能所做的恶作剧而臭名昭著，很多时候它们牺牲了正确性——即使在`fsync`之后，你也不能 100%保存数据。除了一些关于法定人数与主+所有副本的讨论，以及卡夫卡(特别共识)与 NATS (Raft)的演示。

[扩展消息传递(2018)](https://bravenewgeek.com/building-a-distributed-log-from-scratch-part-3-scaling-message-delivery/)——这一篇讨论如何通过分片在多台机器上传播数据(与第 2 部分的复制相对)。有一些关于拉和推方法以及消费者可伸缩性的内容。

[权衡和经验教训(2018)](https://bravenewgeek.com/building-a-distributed-log-from-scratch-part-4-trade-offs-and-lessons-learned/) -基本上，如果他们有后知之明，他们在 NATS 会做得不同。

[勾画新系统(2018)](https://bravenewgeek.com/building-a-distributed-log-from-scratch-part-5-sketching-a-new-system/) -作者将如何着手建立一个基于 NATS 的新系统，但将卡夫卡和 NATS 流的设计线索铭记于心。