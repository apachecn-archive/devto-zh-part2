# 什么碎片？

> 原文：<https://dev.to/jeremylikness/what-the-shard-44h5>

#### 使用带有 Azure Cosmos DB 的 C# MongoDB 驱动对集合进行分区。

Azure Cosmos DB 是一个完全托管的 NoSQL 数据库。我已经在这个博客上写了很多关于它的内容[。一个高级而强大的特性是对数据进行分区的能力。这就是通常所说的*分片*。当您遵循这些分区的](https://blog.jeremylikness.com/tagged/cosmosdb)[先决条件](https://jlik.me/dx8)时，Azure Cosmos DB 会为您管理这些。

我不会在这里过多地讨论分区的细节，因为它在文档中有很好的解释。简而言之，逻辑分区是一系列组合在一起的项目。这是由文档上的一个属性决定的，该属性可以是散列的，也可以指定为一个范围。从逻辑上讲，具有相同分区键的项目放在一起。物理上，一个节点可以存储一个到多个分区或碎片。这是由 Azure Cosmos DB 根据需求透明地为您处理的。

> 在这里阅读所有关于分区的知识:[Azure Cosmos DB 中的分区和扩展](https://jlik.me/dx5)

分区键有助于伸缩，尤其是当数据库在地理上是分布式的时候。这是因为当插入或更新项目时，复制可以集中在该特定分区上，而不是整个数据库。还可以通过为这些读取协同定位分区来优化读取。通过设置特定的[一致性级别](https://dev.to/jeremylikness/getting-behind-the-9-ball-cosmosdb-consistency-levels-2jgg)来优化复制行为。

虽然文档讨论了分区如何跨各种 API 工作(要了解不同 API 和可用数据库“风格”的概述，请阅读这篇关于 Azure Cosmos DB APIs 的概述),但没有明确的示例说明如何通过代码设置分区键。

我从我现有的 [USDA 数据库示例](https://github.com/JeremyLikness/explore-cosmos-db)开始。它包含了将近 9000 种食物，被分配到一个特定的“食物组”选择分区键有许多[最佳实践。为了使这个例子简单，我选择在 FoodGroupId 属性上进行分区。](https://jlik.me/dx6)

在 MongoDB 中创建集合的代码如下所示: