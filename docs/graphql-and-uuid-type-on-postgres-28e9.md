# Postgres 上的 GraphQL 和 UUID 型

> 原文：<https://dev.to/hasurahq/graphql-and-uuid-type-on-postgres-28e9>

### Hasura GraphQL 引擎对 Postgres UUID 类型有隐式支持，这意味着 UUID 值可以作为字符串提供。

[![](img/566747a758602dd274916e99553d4a0e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--z39o1Bf8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/downloaded_images/graphql-and-uuid-type-on-postgres-767f016479e9/1-ohu0ZZXlzkOjp-NyTEr4GA.png)

数据库中的每个表都需要一个[主键](https://hasura.io/learn/database/postgresql/core-concepts/5-postgresql-primary-key/)。

主键的目的是为表中的实体提供稳定的、可索引的引用。通常的做法是使用序列号作为主键。序列号是自动递增的整数，通常由数据库自动生成。

UUID(通用唯一标识符)或 GUID(全球唯一标识符)是序列 id 的一种可靠替代方法，它是一种标准的 128 位数字，很难复制，因此提供了非常适合用作主键的唯一标识符。

***什么是 Postgres UUID 类型？**T3】*

Postgres UUID 数据类型存储由 RFC 4122 定义的 UUID，这些 uuid 是由算法生成的 128 位数量，这些算法将重复标识符的概率降至最低。

UUID 由 32 个十六进制数字组成，以 8、4、4、4 和 12 为一组。

例如:“12345678-ABCD-ef12–345 a-12345678 ABCD”

***为什么是 UUID？**T3】*

*   ***暴露信息:*** 非常频繁的，主 id 暴露给用户(其余 URL，有人吗？).如果主键很容易被用户看到，这意味着任何人都可以通过查看新创建的用户的主键来找出您的用户数量。这泄露了关于你的用户规模的信息。
*   ***条目枚举:*** 另一个问题是，用一个主键枚举你的表的所有条目非常容易。一个恶意用户或者一个机器人可以轻易地窃取你所有用户的身份，或者对你的所有用户进行有组织的操作，向你的 API 发送垃圾邮件。UUID 是普遍随机的，外部各方几乎不可能预测。非常清楚的是，UUIDs 不是一种安全机制，这个问题的实际解决方案是建立更强的访问控制。在这种情况下，UUIDs 只是提供了更多的保护。
*   ***独立生成:*** 顺序 id 最大的问题是客户端不与数据库对话就不知道被插入对象的 id。当插入带有外键的多个相关对象时，这甚至会成为一个更大的问题，并且会创建不优雅的多步插入。另一方面，UUID 可以由客户端直接生成，使得插入非常简单，并且很好地解决了外键问题。
*   ***随机分布:***uuid 的随机分布可以防止磁盘热点，即大量对象存储在特定的磁盘块中，导致磁盘特定区域的高使用率。这对于分布在几个节点上的数据库非常有效。

***为什么不是 UUID？**T3】*

*   ***大小:*** 缺点是 UUIDs 是串行的四倍大，如果你有大小限制，它们可能是个问题。
*   ***难调试:*** 难记，难调试，一个小而重要的 QoL 问题。与长而复杂的 UUIDs 相比，当您有容易记住的简单 id 时，测试/调试会更容易。
*   ***缺乏简单的排序:*** 主键不能用来确定插入的顺序，另一个带有时间戳的列或者一个序列就变得必要了。这也意味着 UUID 列上的聚集索引在性能方面的开销非常大。
*   ***性能:*** 另外，随机 UUIDs 意味着分散的索引，这会导致性能问题/高插入率下的高磁盘使用率。(数据库有顺序 UUID 生成函数来处理这种情况，但这消除了能够在客户端生成 UUID 的优势)

***Hasura graph QL 引擎中的 uuid:***

Hasura GraphQL Engine (HGE)是一个开源工具，它提供即时的基于 Postgres 的 GraphQL APIs。

HGE 支持 UUID 类型作为隐含类型，这意味着 UUID 值可以作为字符串提供。

让我们建立一个带有 UUID 列的简单模式来运行一些查询: