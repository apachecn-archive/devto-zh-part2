# 了解什么是 MongoDB | NoSQL

> 原文：<https://dev.to/smazeestudio/learn-whats-mongodb--nosql-38pf>

Mongo DB 是一个(NoSql)非关系数据库系统，它有一个非结构化数据的动态模式。它比任何关系数据库都更具可伸缩性、灵活性和速度。

[![](../Images/5289c5bfb2e4ff44a69883c119416054.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--28nyBSSa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AplTQEOtbHS3qAr_pz24E4Q.jpeg)

几十年来，关系数据库一直占据主导地位，因为它们很简单，而且有很大的开发人员社区。MySQL、Oracle、PostgreSQL 和 Microsoft SQL Server 通常用于每个应用程序。但是随着时间的推移，即将到来的技术需要更可靠和更强大的数据库。

然后是我们的英雄 NoSQL。1998 年，Carlo Strozzi 制作了一个轻量级的 Strozzi NoSQL 开源关系数据库，它没有公开标准的结构化查询语言(SQL)接口，但仍然是关系数据库。久而久之形成了许多 NoSQL 数据库。NoSQL 数据库有 4 种一般分类。

*   **列** : Accumulo，Cassandra，德鲁伊，HBase，Vertica。
*   **文档** : Apache CouchDB、ArangoDB、BaseX、Clusterpoint、Couchbase、Cosmos DB、IBM Domino、MarkLogic、MongoDB、OrientDB、Qizx、RethinkDB
*   **key-value**:aerospace，Apache Ignite，arangodb，Berkeley DB，Couchbase，Dynamo，FairCom c-treeACE，FoundationDB，infinitydb，MemcacheDB，MUMPS，Oracle NoSQL Database，OrientDB，Redis，Riak，SciDB，SDBM/Flat File dbm，zookeeper
*   **Graph** : AllegroGraph、ArangoDB、InfiniteGraph、Apache Giraph、MarkLogic、Neo4J、OrientDB、Virtuoso

但是 *MongoDB、Cassandra、Redis、HBase 和 Couchbase* 都是常用的 NoSql 数据库。

[![](../Images/2dd23b0946142e317c4d13ab18cf1296.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QWFBMOkg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AqLhURmfiuYcJuJuY9-5K3g.png)

[**MongoDB**](https://www.mongodb.com/) 是 2007 年由 10gen 开发的一个流行的开源面向文档的数据库，后来称为 MongoDB Inc.MongoDB 通常与 Node 和 Express JS (Angular for Front-end)一起使用，统称为 **MEAN** ( **M** ongoDB， **E** xpress JS， **A** ngular， **N** ode)

但是到处使用 NoSql 是不合适的。首先，知道哪里需要使用 NoSql，它是否适合您的应用程序。当您的应用程序具有高写负载、不稳定的模式(例如，当您不知道您的应用程序包含什么信息)、数据是基于位置的、数据很大需要处理时，请使用它。

由于 MongoDB 的可读性很高，所以不需要数据库管理员来维护它。一般的开发者都可以做到。

**可伸缩性** - NoSQL 数据库是水平可伸缩的，这意味着他们可以通过向数据库添加更多的服务器来处理更多的流量。

**灵活模式** - NoSql 文档(表称为文档)可以有任意数量的字段，可以嵌套任意次数。因此，我们不需要为我们的数据库预定义模式。

[**分片**](https://docs.mongodb.com/manual/reference/glossary/#term-sharding) 是一种跨多台机器分发数据的方法。MongoDB 使用分片来支持具有非常大的数据集和高吞吐量操作的部署。

[![](../Images/446db5d0bc84eb4a7b5c89f559d82c1e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EJ7Uyp9p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/600/1%2ApxHOVKFDUotLZYI9a7Kw_w.png)

Mongoose 是 MongoDB 和 Node.js 的对象数据建模(ODM)库，它管理数据之间的关系，提供模式验证，并用于在代码中的对象和 MongoDB 中这些对象的表示之间进行翻译。

使用 MongoDB 的公司列表:Google Mail，LinkedIn，Ebay，Forbes，Otto 等众多公司

MongoDB 的应用包括档案管理、实时大数据、内容管理、目录、移动应用、物联网、欺诈检测。

最后，并不是说你必须在任何地方都使用 MongoDB，*知道在哪里使用它，学会如何使用&有效地使用它。*

参考了许多博客和文件，由 *pravindia* ， [Smazee](http://smazee.com/blog) 撰写