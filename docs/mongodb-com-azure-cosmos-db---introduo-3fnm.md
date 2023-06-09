# 蒙戈布蓝色宇宙 DB -简介

> 原文：<https://dev.to/delbussoweb/mongodb-com-azure-cosmos-db---introduo-3fnm>

这是私人恩怨吗 blz？今天我要开始我的系列文章关于在蓝色宇宙 DB 中使用 mongodb。今天，星期天，我在里约热内卢的威尔士机场 2 号航站楼写了一篇文章，在这里完成了蒙戈布在"奇妙城市"的进一步培训。

但让我们来看看真正重要的！

## 天蓝色宇宙 DB

在简短的介绍中，azure cosmos db 被微软定义为一种面向关键应用程序的全球分布式多模型数据库服务。Azure Cosmos DB 提供的服务包括、[【吞吐量和存储的弹性可扩展性】、](https://docs.microsoft.com/pt-br/azure/cosmos-db/partition-data)、【毫秒延迟】和基于 azure SLA 支持的高可用性保证。此外，蓝色宇宙 DB 会自动索引数据(但这是另一天的主题-我...。)！

蓝色宇宙 DB 可用来储存原本写给[mongbb](https://docs.mongodb.com/manual/introduction/)的应用程式资料。基本上就是说，如果使用现有的驱动程序，则使用 monodb 的应用程序只能通过交换连接字符串与 Azure Cosmos DB(在很多情况下)进行通信，同时继续使用工具并应用其 monodb 知识！

**与 MongoDB 的兼容性**:如果您已经使用 MongoDB，您知道它的工作原理并掌握了方案设计，您可以继续应用所有这些，但要注意某些方面(将在以后的帖子中讨论)，因为截至本文发表之日 Azure Cosmos DB 使用版本 3-我...。

## 但是玛丽把蓝色宇宙 DB 与 mong odb API 结合使用有什么好处呢？

**弹性扩充处理与储存**:垂直增加或减少资料库的能力。数据存储在 SSD 驱动器上，提供尽可能最短的延迟。azure cosmos db 支持可扩展到几乎不受限制的存储大小和吞吐量的集合。您可以在应用程序增长时修改这些设置。

**跨多个区域复制**:蓝色宇宙 DB 可以透明地在与 mongodb 关联的帐户区域之间复制您的数据，从而使您能够开发需要全局分发的应用程序。这样，您还可以在不中断应用程序的情况下设置区域故障切换。

**无服务器**:由于 Azure Cosmos DB 与 DBAs 非常相似，所以您不必担心数据库将要托管的计算机的管理，它在 30 多个[azure 地区](https://azure.microsoft.com/regions/services/)提供。

**可调一致性等级**:今天，Azure Cosmos DB 与 mongbod 3.4 版配合使用，仅提供两个一致性等级:强和最终。Azure Cosmos DB 提供了多种级别的一致性，但是这些级别由您选择的 API 控制，也就是说，根据您使用的 API，这些级别将减少为您选择的 API 提供的级别。直到发行 3.6 版的 mongob 之前，没有会话控制，因此，如果将 mongob API 设置为使用会话一致性，则会降级到可能的一致性。参见[如何使用一致性级别提高可用性和性能](https://docs.microsoft.com/pt-br/azure/cosmos-db/consistency-levels)

| 蓝色宇宙 DB 的一致性等级 | API do MongoDB (3.4) |
| --- | --- |
| 可能的 | 可能的 |
| 一致前缀 | 最终以一致的顺序 |
| 会议 | 最终以一致的顺序 |
| 有限的陈旧 | 强烈的 |
| 强烈的 | 强烈的 |

**自动编制索引**:蓝色 Cosmos DB 自动编制 mongodb 数据库中所有文档属性的索引，无需架构或创建辅助索引。它还允许您在任何已编制索引的属性上创建 unique 类型的索引。

## 下一步行动

在接下来的文章中，我将演示如何使用 mongbod API 创建 Azure Cosmos DB 数据库，然后演示如何通过基本上交换连接字符串来轻松地迁移现有应用程序。稍后我们将继续共同踏上这段旅程，我将与大家分享我在这段旅程中所学到的东西。

我希望你们喜欢，并且愿意分享，让你们喜欢！

抱抱！