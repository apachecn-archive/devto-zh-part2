# 周五爆炸#30

> 原文：<https://dev.to/horia141/friday-blast-30-3b1i>

【dummies 的差分隐私(2016) -差分隐私是一种处理数据的方式，它不提供数据集中任何一个人/物品的信息。一个很好的例子是让人们去识别罪犯/吸毒者等。有一个向数据添加噪声的协议，可以由每个被依次询问的人来完成，以便(1)您不知道是否有任何一个人实际上在目标班级中，以及(2)您可以计算群体查询，例如班级中的总人数、班级中的人数比例等。这里有很多例子，还有一个简单的理论概述。大数据和大监控时代的有趣内容。

[s 表和日志结构化存储:level db(2012)](https://www.igvita.com/2012/02/06/sstable-and-log-structured-storage-leveldb/)——我们之前在这里讨论过[日志结构化合并树](https://en.wikipedia.org/wiki/Log-structured_merge-tree)，但主要是在它们在大数据存储引擎(如 BigTable、HBase、Cassandra 等)中使用的背景下。 [LevelDB](http://leveldb.org/) 是一个用同样方法构建的存储引擎，但目标是“小数据”。它是 SQLite 或平面文件的替代品。本文深入介绍了表的一些细节，表是许多数据库引擎(至少是 NoSQL 数据库引擎)的底层数据结构。

[协议缓冲区，Avro，节俭&消息包(2011)](https://www.igvita.com/2011/08/01/protocol-buffers-avro-thrift-messagepack/)——只是对各种序列化和 RPC 机制的一点评论。

[测量&优化 I/O 性能(2009)](https://www.igvita.com/2009/06/23/measuring-optimizing-io-performance/)——写于磁盘时代，这里仍然有一些有趣的东西。主要围绕`iostat`的使用。

[terra forming Stack Overflow Enterprise in AWS(2018)](https://medium.com/@palantir/terraforming-stack-overflow-enterprise-in-aws-47ee431e6be7)-Palantir 如何将他们安装的 Stack Overflow Enterprise“生产化”的故事。还包括了对 SO 架构的一个很好的概述。如果你是一家寻找知识管理解决方案的大公司，一定要知道 SOE 的存在。

[使用堆栈溢出数据评估亚马逊 HQ2 的选项(2018)](https://stackoverflow.blog/2018/02/28/evaluating-options-amazons-hq2-using-stack-overflow-data/)——来自我们数据团队 [Julia Silge](https://stackoverflow.blog/authors/juliasilge/) 的一篇数据新闻文章。亚马逊正在寻找一个地方来建立他们在美国的第二个总部，并且正在进行一场城市竞赛。就像奥运会一样，对于城市来说，这似乎是一个失败的命题，但他们不能不参加。朱莉娅考察了 20 个入围城市劳动力的技术因素，以了解他们与亚马逊所需物品的匹配程度。