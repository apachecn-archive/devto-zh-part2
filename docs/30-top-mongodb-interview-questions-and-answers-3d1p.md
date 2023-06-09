# 30+热门 MongoDB 面试问答

> 原文：<https://dev.to/aershov24/30-top-mongodb-interview-questions-and-answers-3d1p>

[![30+ Top MongoDB Interview Questions and Answers (2018 Update)](img/7340fc9a67320997f770743c0503c8dd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ghIXsurl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.pexels.com/photos/1143416/pexels-photo-1143416.jpeg%3Fauto%3Dcompress%26cs%3Dtinysrgb%26dpr%3D2%26h%3D350)
NoSQL 数据库运动旨在解决关系数据库的缺点和现代软件开发的需求。MongoDB 是领先的 NoSQL 数据库，在财富 500 强和全球 500 强中被大量采用。

> 最初发表于 [FullStack。永远不要再错过你的技术面试](https://www.fullstack.cafe)

### Q1:解释一下什么是 MongoDB？

> 题目: **MongoDB**
> 难度:⭐

MongoDB 是一个开源文档数据库，它提供了高性能、高可用性和自动伸缩。
它的主要特点是:

*   面向文档的 NoSQL 数据库。
*   支持聚合
*   使用 BSON 格式
*   分片(有助于水平可伸缩性)
*   支持即席查询
*   无模式
*   封顶集合
*   索引(MongoDB 中的任何字段都可以被索引)
*   MongoDB 副本集(提供高可用性)
*   支持多个存储引擎

🔗**来源:**【mongodb.com】T2

### Q2:数据是如何存储在 MongoDB 中的？

> 题目: **MongoDB**
> 难度:⭐⭐

MongoDB 中的数据存储在 BSON 文档中——JSON 风格的数据结构。文档包含一个或多个字段，每个字段包含一个特定数据类型的值，包括数组、二进制数据和子文档。倾向于共享相似结构的文档被组织为集合。将文档想象成关系数据库中的行，将字段想象成列，将集合想象成表可能会有所帮助。

使用文档的优点是:

*   文档(即对象)对应于许多编程语言中的本地数据类型。
*   嵌入式文档和数组减少了对昂贵连接的需求。
*   动态模式支持流畅的多态性。

🔗**来源:**【mongodb.com】T2

### Q3:MongoDB 中有哪些索引？

> 题目: **MongoDB**
> 难度:⭐⭐

索引支持在 MongoDB 中高效执行查询。如果没有索引，MongoDB 必须执行集合扫描，即扫描集合中的每个文档，以选择那些匹配查询语句的文档。如果某个查询有合适的索引，MongoDB 可以使用索引来限制它必须检查的文档数量。

🔗**来源:**【tutorialspoint.com】T2

### Q4:可以在 MongoDB 中对数组字段创建索引吗？如果是，在这种情况下会发生什么？

> 题目: **MongoDB**
> 难度:⭐⭐

是的。在 MongoDB 中可以对数组字段进行索引。在这种情况下，MongoDB 将索引数组的每个值，这样您就可以查询单个项目:

```
> db.col1.save({'colors': ['red','blue']})
> db.col1.ensureIndex({'colors':1})

> db.col1.find({'colors': 'red'})
{ "_id" : ObjectId("4ccc78f97cf9bdc2a2e54ee9"), "colors" : [ "red", "blue" ] }
> db.col1.find({'colors': 'blue'})
{ "_id" : ObjectId("4ccc78f97cf9bdc2a2e54ee9"), "colors" : [ "red", "blue" ] } 
```

🔗**来源:**【stackoverflow.com】T2

### Q5:MongoDB 中的复制是什么？

> 题目: **MongoDB**
> 难度:⭐⭐

**复制**是跨多个服务器同步数据的过程。复制提供了冗余并提高了数据可用性。由于数据的多个副本位于不同的数据库服务器上，复制可以保护数据库免受单个服务器丢失的影响。复制还允许您从硬件故障和服务中断中恢复。

🔗**来源:**【interviewbubble.com】T2

### Q6:MongoDB 中什么是聚合？

> 题目: **MongoDB**
> 难度:⭐⭐⭐

*聚合*操作处理数据记录并返回计算结果。聚合操作将多个文档中的值组合在一起，并可以对分组的数据执行各种操作以返回单个结果。MongoDB 提供了三种执行聚合的方法:

*   聚合管道，
*   地图缩小功能，
*   和单用途聚合方法和命令。

🔗**来源:**【tutorialspoint.com】T2

### Q7:如何用%like%查询 MongoDB？

> 题目: **MongoDB**
> 难度:⭐⭐⭐

我想用 SQL 的 like 查询:

```
select * 
from users 
where name like '%m%' 
```

如何在 MongoDB 中做同样的事情？

* * *

```
db.users.find({name: /a/})  //like '%a%'
db.users.find({name: /^pa/}) //like 'pa%'
db.users.find({name: /ro$/}) //like '%ro' 
```

或者用猫鼬:

```
db.users.find({'name': {'$regex': 'sometext'}}) 
```

🔗**来源:**【stackoverflow.com】T2

### Q8:如何在 MongoDB 中执行 SQL JOIN 等价？

> 题目: **MongoDB**
> 难度:⭐⭐⭐

Mongo 不是一个关系数据库，开发人员正在小心地推荐$lookup 的具体用例，但是至少从 3.2 开始，使用 MongoDB 可以实现 join。添加到聚合管道中的新$lookup 操作符本质上与左外连接相同:

```
{
   $lookup:
     {
       from: <collection to join>,
       localField: <field from the input documents>,
       foreignField: <field from the documents of the "from" collection>,
       as: <output array field>
     }
} 
```

🔗**来源:**【stackoverflow.com】T2

### Q9:b/w MongoDB 和 CouchDB 有什么区别？

> 题目: **MongoDB**
> 难度:⭐⭐⭐

MongoDB 和 CouchDB 都是开源 NoSQL 数据库的典范。两者都是面向文档的数据库。虽然两者都存储数据，但是它们在数据模型的实现、接口、对象存储和复制方法等方面有很多不同。

🔗**来源:**【medium.com/@hub4tech】T2

### Q10:什么是 NoSQL 数据库？有哪些不同类型的 NoSQL 数据库？

> 题目: **MongoDB**
> 难度:⭐⭐⭐

NoSQL 数据库提供了一种存储和检索数据的机制，这种数据的建模方式不同于关系数据库(如 SQL、Oracle 等)中使用的表格关系。).

NoSQL 数据库的类型:

*   面向文档
*   关键字值
*   图表
*   面向列

🔗**来源:**【interviewbubble.com】T2

### Q11:解释 MongoDB 中 ObjectID 的结构

> 题目: **MongoDB**
> 难度:⭐⭐⭐

**目的**小，可能唯一，生成快，有序。ObjectId 值由 12 个字节组成，其中前四个字节是反映 ObjectId 创建情况的时间戳。具体来说:

*   一个 4 字节的值，表示自 Unix 纪元以来的秒数，
*   一个 5 字节的随机值
*   从随机值开始的 3 字节计数器。在 MongoDB 中，存储在集合中的每个文档都需要一个 unique _id 字段作为主键。如果插入的文档省略了 _id 字段，MongoDB 驱动程序会自动为 _id 字段生成一个 ObjectId。

🔗**来源:**【mongodb.com】T2

### Q12:什么是 MongoDB 中的覆盖查询？

> 题目: **MongoDB**
> 难度:⭐⭐⭐

覆盖查询是这样的查询，其中:

*   查询中使用的字段是查询中使用的索引的一部分，并且
*   结果中返回的字段在同一索引中

🔗**来源:**【tutorialspoint.com】T2

### Q13:查找两个日期之间的对象 MongoDB

> 题目:**MongoDB**T2】难度:⭐⭐⭐

```
db.CollectionName.find({"whenCreated": {
    '$gte': ISODate("2018-03-06T13:10:40.294Z"),
    '$lt': ISODate("2018-05-06T13:10:40.294Z")
}}); 
```

🔗**来源:**【stackoverflow.com】T2

### Q14:什么是 oplog？

> 题目: **MongoDB**
> 难度:⭐⭐⭐

*操作日志*(操作日志)是一个特殊的有上限的集合，它保存了修改存储在数据库中的数据的所有操作的滚动记录。MongoDB 在主服务器上应用数据库操作，然后在主服务器的操作日志上记录这些操作。然后，次要成员在异步过程中复制并应用这些操作。

🔗**来源:**【tutorialspoint.com】T2

### Q15:MongoDB 支持 ACID 事务管理和锁定功能吗？

> 题目: **MongoDB**
> 难度:⭐⭐⭐

酸站，任何更新是:

*   **原子**:要么完全完成，要么不完成
*   **一致**:没有读者会看到“部分应用”的更新
*   **隔离**:没有读者会看到“脏”读
*   **持久**:(带有适当的写问题)

过去，MongoDB 不支持缺省的多文档 ACID 事务(可以回滚并符合 ACID 的多文档更新)。然而，MongoDB 提供了对单个文档的原子操作。MongoDB 4.0 **将增加对多文档事务**的支持，使其成为唯一一个将文档模型的速度、灵活性和功能与 ACID 数据完整性保证相结合的数据库。

🔗**来源:**【tutorialspoint.com】T2

### Q16:MongoDB 中的分片是什么？解释一下。

> 题目: **MongoDB**
> 难度:⭐⭐⭐

*分片*是一种跨多台机器存储数据的方法。MongoDB 使用分片来支持具有非常大的数据集和高吞吐量操作的部署。

🔗**来源:**【tutorialspoint.com】T2

### Q17:在将我的数据存储到 MongoDB 之前，我应该将它规范化吗？

> 题目: **MongoDB**
> 难度:⭐⭐⭐

这取决于你的目标。标准化将提供*更新的高效数据表示*。反规格化将使数据*读取高效*。

通常，在下列情况下，使用嵌入式数据模型(反规范化):

*   实体之间有“包含”关系。
*   实体之间有一对多的关系。在这些关系中,“许多”或子文档总是与“一个”或父文档一起出现，或者在“一个”或父文档的上下文中被查看。

通常，使用规范化的数据模型:

*   当嵌入会导致数据重复，但不会提供足够的读取性能优势来抵消重复的影响时。
*   来表示更复杂的多对多关系。
*   对大型分层数据集建模。

在 MongoDB 中，像处理关系数据库一样规范化数据通常不是一个好主意。只有在表之间的连接相对便宜的前提下，关系数据库中的规范化才是可行的。$lookup 聚合运算符提供了一些有限的连接功能，但它不适用于分片集合。所以 joins 经常需要应用程序通过多个后续的数据库查询来模拟，这非常慢(更多信息请参见问题 MongoDB 和 JOINs)。

🔗**来源:**【stackoverflow.com】T2

### Q18:MongoDB 如何保证高可用性？

> 题目: **MongoDB**
> 难度:⭐⭐⭐⭐

MongoDB 自动维护副本集，即分布在服务器、机架和数据中心的多个数据副本。副本集使用本机复制和自动故障转移来帮助防止数据库停机。

一个副本集由多个副本集成员组成。在任何给定时间，一个成员充当主要成员，其他成员充当次要成员。如果主成员由于任何原因(例如，硬件故障)出现故障，则其中一个次成员会自动被选为主成员，并开始处理所有读取和写入。

🔗**来源:**【mongodb.com】T2

### Q19:MongoDB 如何提供并发？

> 题目: **MongoDB**
> 难度:⭐⭐⭐⭐

MongoDB 使用读-写锁，允许并发读者共享对资源(如数据库或集合)的访问，但只允许对单个写操作进行独占访问。

🔗**来源:**【tutorialspoint.com】T2

### Q20:日志在 MongoDB 中是如何工作的？

> 题目: **MongoDB**
> 难度:⭐⭐⭐⭐

当使用*日志*运行时，在磁盘上的数据文件出现更改之前，MongoDB 在内存和磁盘日志中存储和应用写操作。对日志的写入是原子性的，确保了磁盘上日志文件的一致性。启用日志记录后，MongoDB 会在 dbPath 定义的目录中创建一个日志子目录，默认情况下是/data/db。

🔗**来源:**【tutorialspoint.com】T2

### 【q21:什么时候来背还是蒙戈布？

> 题目: **MongoDB**
> 难度:⭐⭐⭐⭐

*   如果你还不知道如何查询数据或者坚持使用什么模式，就使用 MongoDB。
    MongoDB 适用于黑客马拉松、初创公司或者任何你不知道如何查询你插入的数据的时候。MongoDB 不会对您的底层模式做任何假设。虽然 MongoDB 是无模式和非关系的，但这并不意味着根本没有模式。这仅仅意味着你的模式需要在你的应用中定义(比如使用 Mongoose)。除此之外，MongoDB 非常适合原型开发或试验。它的性能没有那么伟大，不能和 Redis 相比。

*   使用 Redis 来加速你现有的应用程序。将 Redis 用作独立的数据库系统是非常少见的(有些人更喜欢称之为“键值”存储)。

🔗**来源:**【stackoverflow.com】T2

### Q22: MongoDB 关系。用什么——嵌入还是引用？

> 题目: **MongoDB**
> 难度:⭐⭐⭐⭐

想设计一个有一些评论的问题结构，但是不知道评论用哪种关系:嵌入还是引用？请解释一下这两种解决方案的优缺点。

* * *

总的来说，

*   如果实体之间有一对一或一对多的关系，嵌入是很好的选择，并且
*   如果你有多对多的关系，引用是很好的选择。

一般来说，如果您有很多[子文档]或者它们很大，单独的集合可能是最好的。更小和/或更少的文档更适合嵌入。

🔗**来源:**【stackoverflow.com】T2

### Q23:MongoDB 是无模式的吗？

> 题目: **MongoDB**
> 难度:⭐⭐⭐⭐

不。在 MongoDB 中，模式设计仍然很重要。然而，MongoDB 的文档模型采用了与传统关系数据库不同的模式范式。

在 MongoDB 中，文档是自描述的；没有声明和维护模式的中央目录。模式可以随文档而变化，并且模式可以快速发展，而不需要修改现有数据。

MongoDB 的动态模式也使得表示半结构化和多态数据更加容易，因为文档不需要完全相同的字段。例如，金融交易头寸的集合可能包含股票头寸、债券头寸和现金头寸。所有职位可能都有一些共同的字段，但有些字段(“股票代码”、“股票数量”)并不适用于所有职位类型。

🔗**来源:**【mongodb.com】T2

### Q24:什么是主副本集和辅助副本集？

> 题目: **MongoDB**
> 难度:⭐⭐⭐⭐

*   **主节点**和主节点是可以接受写入的节点。MongoDB 的复制是“单主的”:一次只有一个节点可以接受写操作。
*   **辅助节点**和从节点是从主节点复制的只读节点。

🔗**来源:**【tutorialspoint.com】T2

### Q25:如何检查字段是否包含子串？

> 题目: **MongoDB**
> 难度:⭐⭐⭐⭐

考虑:

```
db.users.findOne({"username" : {$regex : ".*subsctring.*"}}); 
```

🔗**来源:**【stackoverflow.com】T2

### Q26:MongoDB 有哪些替代品？

> 题目: **MongoDB**
> 难度:⭐⭐⭐⭐

Cassandra、CouchDB、Redis、Riak、Hbase 是几个不错的选择。

🔗**来源:**【interviewbubble.com】T2

### Q27:如何查找包含特定值数组的文档？

> 题目: **MongoDB**
> 难度:⭐⭐⭐⭐⭐

您有这样的模式:

```
person = {
    name : String,
    favoriteFoods : Array
} 
```

其中的`favoriteFoods`数组是用字符串填充的。如何使用 MongoDB 找到所有喜欢吃`sushi`的人？

* * *

考虑:

```
PersonModel.find({ favouriteFoods: "sushi" }, ...);
PersonModel.find({ favouriteFoods: { "$in" : ["sushi"]} }, ...); 
```

🔗**来源:**【docs.mongodb.com】T2

### Q28:可以使用另一个字段的值更新 MongoDB 字段吗？

> 题目: **MongoDB**
> 难度:⭐⭐⭐⭐⭐

在 SQL 中，我们将使用:

```
UPDATE Person SET Name = FirstName + ' ' + LastName 
```

用 MongoDB 可能吗？

* * *

您还不能在更新中引用文档本身。您需要遍历文档，并使用函数更新每个文档。
所以考虑:

```
db.person.find().snapshot().forEach(
  function(elem) {
    db.person.update({
      _id: elem._id
    }, {
      $set: {
        name: elem.firstname + '  ' + elem.lastname
      }
    });
  }
); 
```

🔗**来源:**【stackoverflow.com】T2

### Q29:解释什么是横向可伸缩性？

> 题目: **MongoDB**
> 难度:⭐⭐⭐⭐⭐

**水平可扩展性**是通过连接多个硬件或软件实体来增加容量的能力，以便它们作为一个逻辑单元工作。MongoDB 提供水平可伸缩性作为其核心功能的一部分。

🔗**来源:**【searchcio.techtarget.com】T2

### Q30:MongoDB 和 MySQL 有什么区别？

> 题目: **MongoDB**
> 难度:⭐⭐⭐⭐⭐

MongoDB 和 MySQL 的主要区别是:

1.  这两个数据库中的数据表示有所不同。在 MongoDB 中，数据表示在 JSON 文档的集合中，而在 MySQL 中，数据表示在表和行中。使用 PHP 时，JSON 文档可以与关联数组进行比较，使用 Python 时，可以与目录对象进行比较。
2.  说到查询，您必须在 DB 系统解析的查询语言中放入一个字符串。这种查询语言被称为结构化查询语言，简称 SQL，MySQL 就是由此得名的。这使您的数据库容易受到 SQL 注入攻击。另一方面，MongoDB 的查询是面向对象的，这意味着您传递给 MongoDB 一个解释您正在查询什么的文档。没有任何解析，如果您已经使用 SQL，这需要一些时间来适应。
3.  像 MySQL 这样的关系数据库的最大好处之一是连接操作。该操作允许跨多个表进行查询。虽然 MongoDB 不支持关节，但它像其他文档和数组一样支持多维数据类型。
4.  使用 MySQL，您可以将一个文档放在另一个文档中(嵌入)。如果您使用 MySQL 创建博客，您必须创建一个评论表和一个帖子表。在 MongoDB 中，一个帖子中只有一个评论数组和一个帖子集合。
5.  MySQL 支持原子事务。在一个事务中可以有多个操作，并且可以像只有一个操作一样回滚。MongoDB 不支持事务，单一操作是原子性的。
6.  MongoDB 最大的优点之一是您不需要负责定义模式。你所需要做的就是丢下文件。集合中的任意两个文档不必在同一个字段中。在 MySQL 中存储之前，必须定义表和列。表中的所有行共享相同的列。
7.  MongoDB 的性能优于 MySQL 和其他关系数据库。这是因为 MongoDB 牺牲了连接和其他东西，拥有优秀的性能分析工具。请注意，您仍然需要索引数据，大多数应用程序中的数据不足以让他们看到差异。MySQL 被诟病性能差，尤其是在 ORM 应用上。但是，如果您做了适当的数据索引并且使用了数据库包装器，就不太可能有问题。
8.  MySQL 相对于 MongoDB 等 NoSQL 的一个优势是，MySQL 中的社区比 NoSQL 好得多。这主要是因为 NoSQL 相对较新，而 MySQL 已经存在好几年了。
9.  MongoDB 没有报告工具，这意味着性能测试和分析并不总是可能的。使用 MySQL，您可以获得几个报告工具，帮助您验证应用程序的有效性。
10.  RDBSs 在一个称为 ACID 的范例上运行，ACID 是(原子性、一致性、隔离性和持久性)的首字母缩写。这是 MongoDB 数据库中没有的。
11.  MongoDB 有一个 Map Reduce 特性，允许更容易的可伸缩性。这意味着即使您使用低成本的硬件，也可以获得 MongoDB 数据库的全部功能。
12.  您不必用 MongoDB 设计详细的数据库模型，因为它是非关系型的。数据库架构师可以在没有细粒度数据库模型的情况下快速创建数据库，从而节省开发时间和成本。

🔗**来源:**【analyticbridge.datasciencecentral.com】T2

> 谢谢🙌阅读，祝你面试好运！
> *如果你喜欢这篇文章，请分享给你的开发者伙伴！*
> *查看更多全栈面试问题&答案上👉[www . full stack . cafe](https://www.fullstack.cafe)T9】*