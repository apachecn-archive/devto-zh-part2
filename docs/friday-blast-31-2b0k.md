# 周五爆炸#31

> 原文：<https://dev.to/horia141/friday-blast-31-2b0k>

[容器术语实用介绍(2018)](https://developers.redhat.com/blog/2018/02/22/container-terminology-practical-introduction/)——如果你曾经使用过 Docker 或其他容器技术，你可能会对所有的名称感到有点困惑——容器、存储库、注册表、图像等等。有很多东西需要解开，很多人用一个名字表示另一个名字。这是一个来自 RedHat 的冗长的介绍&容器术语表。

[我做 CTO 做错了什么(2018)](https://geowarin.github.io/what-i-did-wrong-as-a-cto/) -朗朗上口的标题。这主要是关于技术选择和如何发挥这些作用。我认为关键在于——选择是棘手的，过于实验性的东西会咬你一口。我认为组织和人员问题的分解会更有意思。

* * *

以下 4 篇系列文章描述了优步的`Schemaless`系统——一个类似 NoSQL 的数据库，具有一些非常简洁的功能——多个索引、触发器和强&弱/最终一致性保证的有趣组合。所有这些都建立在 MySQL 之上。这对于 MySQL 来说有点像家庭手工业——DynamoDB 也是建立在它之上的。

[项目夹层:大迁移(2015)](http://eng.uber.com/mezzanine-migration/) -这部分描述了他们试图解决的问题和他们现有的解决方案，以及向无模式迁移的过程。

[使用 MySQL 设计无模式优步工程公司的可扩展数据存储库(2016)](https://eng.uber.com/schemaless-part-one/) -这一部分将深入系统的内部和架构。它还提供了为什么其他选项不够好的原因。否则，系统看起来非常像 BigTable/HBase，并带有一些不变性。

[schema less 的架构，优步工程公司使用 MySQL 的旅行数据存储(2016)](https://eng.uber.com/schemaless-part-two/) -这是关于数据库的分布式系统方面。有什么样的节点，客户如何与他们交谈等。

[在 Schemaless 上使用触发器，优步工程公司的 trip 数据存储使用 MySQL (2016)](https://eng.uber.com/schemaless-part-three/) -触发机制概述。这与卡夫卡的作品非常相似。不可变的键值形成了一个分片日志。DB 为客户机维护读索引(假设它们并不多)，并在发生变化时通过 RPC 通知它们。由它们和客户机内部的无模式框架代码来读取和处理数据。幂等性在这里是一个很大的优势。

[生产中的代码迁移:重写优步无模式数据存储的共享层(2018)](https://eng.uber.com/schemaless-rewrite/) -两年过去了，系统已经成长。这不是一个架构概述，而是描述他们如何用更具可伸缩性的 Go 替换 worker node 的 Python 代码。与其他迁移一样，困难的部分是确保两个代码路径返回完全相同的结果。