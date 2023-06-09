# GraphQL-to-MongoDB，或者说我如何学会不再担心并爱上生成的查询 API

> 原文：<https://dev.to/yoav/graphql-to-mongodb-or-how-i-learned-to-stop-worrying-and-love-generated-query-apis-47pk>

在这篇文章中，我们将看看如何利用 GraphQL 类型来展示 NodeJs 中的 MongoDB 功能。我们将研究 graph QL-to-MongoDB(T1)，这是我们为自己的服务提出的一个不引人注目的解决方案，以及它的基本原理。

## graph QL MongoDB 堆栈的问世

相对较新的 GraphQL 和成熟的 MongoDB 是两种看起来非常适合彼此的技术。MongoDB 是一个面向文档的数据库，具有灵活的查询语言。GraphQL 是一种服务 API，同时也是一种查询语言。它通过定义层次化、类型化和参数化的模式来工作。这两种技术都在分层数据结构中接受和接收参数。总之，这两种兼容的技术可以在您的客户机和数据之间形成一个流线型的接口。

首先，如果你觉得你对这两种技术的知识需要复习，欢迎你在继续之前阅读更多关于 [GraphQL](http://graphql.org/learn/) 或 [MongoDB](https://docs.mongodb.com/manual/reference/method/db.collection.find/) 的内容。

## 成长的烦恼

实现一种在 GraphQL 后端公开 MongoDB 查询的方法并不是一项简单的任务；当我们试图在最新的 NodeJs 服务中加入这两者时，我们了解到了这一点。从添加一个查询字段开始，使用一个比较运算符，一次添加一个，这很简单。然而，随着客户机查询复杂性的增加，您很容易发现自己维护着一堆杂乱无章的过滤代码。

开发人员可能想简单地接受一个通用的 JSON 类型作为输入，将客户的输入直接传递给 MongoDB，但是我们认为这种解决方案并不令人满意。这种方法不仅错过了 GraphQL 的全部要点，还放弃了对客户机如何与数据库通信的控制。

## 我们理想的 API

认识到问题并不简单后，我们开始寻找适合我们需求的解决方案，即:

*   MongoDB 强大查询功能的接口
*   简单实现
*   不引人注目的整合
*   GraphQL 类型模式的明确性和一致性
*   对 NoSQL 注射没有脆弱性

不幸的是，我们的调查没有取得丰硕的成果。

## 如果你想把事情做好…

通常情况下，我们找不到满足我们需求的成熟且记录良好的第三方解决方案，这促使我们自己设计一个。随后，我们以包 **graphql-to-mongodb** 的形式找到了答案，这个包在 [GitHub](https://github.com/Soluto/graphql-to-mongodb) 和 [npm](https://www.npmjs.com/package/graphql-to-mongodb) 上公开发布。从根本上说，该包的工作原理是基于现有的 GraphQL 类型，在运行时为 GraphQL 模式生成查询参数。它将发送的请求解析成 MongoDB 查询参数。

让我们看看它是如何检查我们之前确定的需求的:

### MongoDB 适合您的客户

这个包用 MongoDB 最常用的查询操作符来增强您的 GraphQL API。有了它，客户可以以多种方式梳理底层数据，而不需要为每个新查询对 API 进行额外的更改。

使用包发送给服务的 GraphQL 查询示例，展示了过滤、排序和分页:

```
{  person  (  filter:  {  age:  {  GT:  18  },  name:  {  firstName:  {  EQ:  "John"  }  }  },  sort:  {  age:  DESC  },  pagination:  {  limit:  50  }  )  {  name  {  lastName  }  age  }  } 
```

Enter fullscreen mode Exit fullscreen mode

<center>Queries 50 people, oldest first, over the age of 18, and whose first name is John</center>

所有这些，甚至更多，只需要您的服务中很少的开发开销。

### 简单明了

与许多软件包一样，它努力为您提供最大的性价比，将解决方案的复杂性隐藏在简单的集成背后。暴露的 GraphQL 字段将基于描述数据结构模式的底层 GraphQL 类型。

<center>Given a simple GraphQL type</center>