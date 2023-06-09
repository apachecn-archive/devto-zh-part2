# MySQL vs. MongoDB

> 原文：<https://dev.to/gyi2521/mysql-vs-mongodb--5bj3>

最近，除了 MongoDB(我们在课程中使用的主要数据库)之外，我还从我的编码训练营课程中了解了 MySQL。我想分享我到目前为止对这两个数据库的了解。

主要区别是 MySQL 使用结构化查询语言(SQL)，而 MongoDB 使用 NoSQL。SQL 和 NoSQL 的区别是什么？在我读过的一个博客中，它使用了一个类比，即生活在一个每个人都说同一种语言的城镇和一个人们说不同语言的城镇。

SQL 数据库使用结构化查询语言(SQL)来定义和操作数据。这是非常强大的，但也可能是限制性的。SQL 要求您在使用数据之前使用预定义的模式来确定数据的结构。此外，所有数据必须遵循相同的结构。

另一方面，NoSQL 数据库具有用于非结构化数据的动态模式，并且数据以多种方式存储:可以是面向列的、面向文档的、基于图形的或者组织为键值存储。

MySQL 是一个非常成熟的数据库，这意味着有一个巨大的社区，广泛的测试和相当多的稳定性。它是开源和免费的。它适用于所有主流平台，包括 Linux、Windows、Mac、BSD 和 Solaris。

另一方面，MongoDB 是一个非关系数据库，它将数据作为“文档”存储在名为 BSON (Binary JSON)的二进制表示中。它为您提供了更改数据模式的灵活性，而无需修改任何现有数据。MongoDB 是水平可伸缩的，这有助于减少工作量并轻松扩展您的业务。它非常灵活，因此您可以在 MongoDB 上添加新的列或字段，而不会影响现有的行或应用程序性能。

下面我将给出一些查询示例。

```
Select/Get statement:

MySQL: SELECT * FROM inventory
MongoDB: db.inventory.find({})

Insert/Post statement:

MySQL : INSERT INTO inventory (item_id, type) VALUES ('ab123', ‘electronics’)
MongoDB: db.inventory.create({item_id: ‘ab123’, type: ‘electronics’})

```

感谢阅读！