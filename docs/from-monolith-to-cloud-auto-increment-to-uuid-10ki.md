# 从整体到云:自动增量到 UUID

> 原文：<https://dev.to/bgadrian/from-monolith-to-cloud-auto-increment-to-uuid-10ki>

> 本文最初发表在我的网站[https://coder . today/software-engineer-from-monolith-to-cloud-auto-increment-to-uuid-a62f 92 f 387 C4](https://coder.today/software-engineer-from-monolith-to-cloud-auto-increment-to-uuid-a62f92f387c4)

## 从独石到云系列🌩

一系列旨在帮助开发人员从单一模式转向云模式的文章。网上有很多很好的教程和例子，告诉我为什么以及如何做出改变，所以我决定把重点放在小细节上。

*   [自动增加到 UUID](https://coder.today/software-engineer-from-monolith-to-cloud-auto-increment-to-uuid-a62f92f387c4)

*   [想小了](https://coder.today/software-engineer-from-monolith-to-cloud-think-small-f828effc6afc)

> 这篇文章关注的是那些只使用数字自动递增主键，并且需要/想要切换到 UUID 的开发人员。

## ID int NOT NULL 自动递增🔢

像 MySql/SQL/Oracle 这样的关系数据库中的条目通常由一个递增的、唯一的(对表而言)编号 int(2232)来标识。服务器收集参数，发送插入(...)语句，数据库生成一个新的 ID(下一个增量值)并返回它。

> # When you start expanding, you may encounter a bottleneck, that is, your MySql primary instance, because this is the only entity in your system that can generate a unique identifier.

你**已经知道了自动增量 PK 的好处**，下面是它的限制列表:

*   需要访问(通过管道/API/服务器/连接)主实例
*   您依赖于来自一个数据中心的一台服务器的一个实例(延迟、可用性)
*   所有的写操作都在一个位置完成(大多数情况下)，这导致产生新 ID 的硬件限制
*   容易伪造的 ID(额外好处:你可以很容易地找到客户的数量)
*   MAX_INT —虽然希望渺茫，但仍然…值得一提

所有这些问题都可以在一定程度上得到缓解(比如 MySql 切分)。要解决所有这些问题，你可以使用 UUID 的。

[![UUID](../Images/cf599eed0cb552e8c4753150a6736bb4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LncOeP4n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dzl07r56r59p8cg2jvg3.png)

## [UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) 🍱

一个**通用唯一标识符** ( **UUID** )是一个 128 位的**号**，用于识别计算机系统中的信息。还使用了术语**全局唯一标识符** ( **GUID** )。UUID 的大小在不同的实现中可以不同。

> # UUID can be used in the [relational database](https://stackoverflow.com/questions/5230638/mysql-autoincrement-to-guid) , and vice versa, it will be automatically incremented in NoSql.

在其规范的文本表示中，它是 32 个十六进制的(基数 16)数字，显示在由 4 个连字符分隔的五个组中:`123e4567-e89b-12d3-a456-426655440000`。

一些位代表 UUID **版本**(用于生成它的算法)，其他位代表变体。从 UUID 格式开始，你甚至可以添加你自己的逻辑(比特代表你的业务逻辑的某些方面)。

为了生成 UUID 的随机字节，使用了更多的因素来确保更好的熵，如时间戳和时钟序列。更多技术细节，你可以阅读 UUID 的官方协议文件。当心你使用的实现，**不是所有的库都尊重标准**。我发现有些实现只是使用每种语言中都有的可预测的伪随机函数来生成一个数字。

> # Basically, UUID/GUID is a random ID, and the values are not continuous. Anyone can create a new ID.

[![](../Images/6994130c6f8f3fa2a72746f1c06d09d3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KJ64zsD5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ykg21b16vh08o2b5yjdl.png)

与线性增量值相比，有几个优点

*   更容易破碎
*   更容易合并/复制。没有普遍的秩序。
*   **更大的灵活性** —您可以在数据库之外生成 UUID，委托给服务器或客户端，独立于环境/平台，但是**您可能会失去一些数据完整性**
*   您甚至可以允许**离线**注册(并在可用时进行同步)，但是您永远不会有完整的数据库快照(因为客户端不同步)
*   **缩放** —UUID 对 ID 的限制比 INT 大
*   你**在插入之前知道 ID** ，这可以简化逻辑/流程
*   UUID 可以有你自己的格式，你可以把它分成 4 个数字，每个数字代表其他东西，例如，如果你把来自 20 个网站的用户分组，第一个数字可以代表应用程序。

[![Monolith vs Cloud: AUTO PKID vs UUID](../Images/8a4b9725323e47bede6bcc34994e7911.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4tM15KYA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/08qvx53bzc7dcee2yf32.png)

## 新的心态🤕

[![](../Images/fcc435788b83f6bd2a40405a18533612.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QyCI8iEO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hhxtc0s0bmonygyxgkf8.png)

你将不得不习惯**一个更困难的调试过程**，UUID 的是不可能记住的。记忆第一个或最后一个字符的技巧可能不会奏效。

对于人类的大脑来说，这种改变是很难的，你会试图去对抗它，最有可能的理由是:INT 更漂亮，占用的存储空间更少。拥抱更好的变化，是软件进化的一部分。

基于 **async:时间和地点**的城镇新要素。一个 UUID 可能存在于某个地方，但是还没有同步或者在其他碎片中。不再有通用同步视图。你的生产现在正处于**混乱**，**分配**能搞乱你的因缘，不要感到沮丧，只是另一种做事方式。

分发会导致一些不好的结果:**重复或丢失数据**。这意味着需要额外的编码和额外的会议来向产品所有者解释原因和方法。

我不认为 UUID 比增量 ID 更好或更坏，它们只是服务于不同的目的。但是…

> # I will recommend any new application that uses UUID, the current state of software requirements iXXXt (extended application, various types of client & platforms, offline applications …).

#### 走之前，我推荐多读一些:

*   [软件工程师——从整体到云:从小处着眼](https://coder.today/software-engineer-from-monolith-to-cloud-think-small-f828effc6afc)向容器和微服务的转变如何影响我们的思维和工作流程

*   Pinterest 拥有一个由 MySql 碎片组成的[车队，并使用本地自动增量](https://medium.com/@Pinterest_Engineering/sharding-pinterest-how-we-scaled-our-mysql-fleet-3f341e96ca6f) ID，最终他们复制了一个 NoSql(生成 UUID 并存储 json)。与 Twitter 和脸书一样，他们从一开始就使用了错误的技术，并试图在产品变大时拼凑东西。

*   生产中 UUID 的注意事项和问题: [UUID 还是 GUID 作为主键？小心点！](https://tomharrisonjr.com/uuid-or-guid-as-primary-keys-be-careful-7b2aa3dcb439)

*   最常用的 NoSQL 存储解决方案大致对比:
    [Cassandra vs MongoDB vs couch db vs Redis vs Riak vs h base vs couch base vs Hypertable vs…](https://kkovacs.eu/cassandra-vs-mongodb-vs-couchdb-vs-redis)

*   [生成用于 MongoDB 的全球唯一标识符](https://www.mongodb.com/blog/post/generating-globally-unique-identifiers-for-use-with-mongodb)

*   [MongoDB 中的 ObjectId -操作隔离](https://docs.mongodb.com/manual/reference/method/ObjectId/#ObjectIDs-BSONObjectIDSpecification)

*   什么时候你真正被迫将 UUID 作为设计的一部分？

*   有一些特定的场景需要增加 ID，比如队列:[自动增加 NoSql 中的键](https://fygt.wordpress.com/2013/06/19/auto-incrementing-keys-with-nosql-solutions/)

## 谢谢！

请(喜欢)👏如果你学到了新的东西就订阅吧。请将您的反馈发送给我，以便我改进以下帖子。

[![](../Images/ee4413adb798fd1aa37ad5aee55e2956.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CM_ikv0a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6q608badp8eqitr4wa0v.jpeg)