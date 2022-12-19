# 你应该知道的 5 个 GraphQL 面试问题和答案

> 原文：<https://dev.to/aershov24/5-graphql-interview-questions-and-answers-you-should-know-4ajb>

[![5 GraphQL Interview Questions You Should Know](../Images/abcca3936453ec8f08343fe10152fe05.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HxHRMEhr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.pexels.com/photos/527/man-person-people-train.jpg%3Fauto%3Dcompress%26cs%3Dtinysrgb%26dpr%3D2%26h%3D350) 
GraphQL 是一种用于 API 的查询语言，也是一种用现有数据完成这些查询的运行时语言。GraphQL 为 API 中的数据提供了完整且易于理解的描述，使客户能够准确地要求他们需要的东西，使 API 更容易随时间发展，并支持强大的开发工具。

> 最初发表于 [FullStack。永远不要再错过你的技术面试](https://www.fullstack.cafe)

### Q1:graph QL 是什么？

> 题目:**graph QL**T2】难度:⭐⭐

GraphQL 是由[脸书](http://facebook.github.io/)在 2012 年创建的一种查询语言，它在客户端和服务器之间提供了一个**通用接口，用于数据获取和操作**。

客户端通过查询向 GraphQL 服务器请求各种数据。响应格式在查询中描述，并由客户端而不是服务器定义:它们被称为**客户端指定的查询**。

数据的结构不像传统的 REST APIs 那样是硬编码的——这使得客户端从服务器检索数据更加高效。

🔗**来源:**【howtographql.com】T2

### Q2:列出 GraphQL 查询语言的关键概念

> 题目:**graph QL**T2】难度:⭐⭐⭐

**graph QL**查询语言的关键概念是:

*   等级体系的
*   以产品为中心
*   强类型
*   客户指定的查询
*   反省的

🔗**来源:**【blog.risingstack.com】T2

### Q3:解释 REST 和 GraphQL 的主要区别

> 题目:**graph QL**T2】难度:⭐⭐⭐

REST 和 GraphQL 之间最主要也是最重要的区别是 *GraphQL 不处理专用资源，而是一切都被视为一个图，因此是相互关联的，可以根据应用程序的确切需求进行查询*。

🔗**来源:**【medium.com/codingthesmartway-com-blog】T2

### Q4:如何做服务器端缓存？

> 题目:**graph QL**T2】难度:⭐⭐⭐

GraphQL 的一个常见问题是维护服务器端缓存的困难，尤其是与 REST 相比时。使用 REST，很容易缓存每个端点的数据，因为可以肯定数据的*结构*不会改变。

另一方面，对于 GraphQL，不清楚客户端下一步会请求什么，所以在 API 后面放一个缓存层没有什么意义。

服务器端缓存仍然是 GraphQL 的一个挑战。

🔗**来源:**【howtographql.com】T2

### Q5:graph QL 有什么缺点吗？

> 题目:**graph QL**T2】难度:⭐⭐⭐⭐

缺点:

*   你**需要学习**如何设置 GraphQL。生态系统仍在快速发展，所以你必须跟上。
*   你需要从客户端发送查询，你可以只发送字符串，但是如果你想要更多的舒适和缓存，你将在你的客户端使用一个客户端库-> **额外代码**
*   在得到结果之前，需要预先定义模式=> **额外的工作**
*   您需要在您的服务器上有一个 graphql 端点=> **您还不知道的新库**
*   Graphql 查询比简单地访问 REST 端点多了**个字节**
*   服务器需要做**更多的处理**来解析查询并验证参数

🔗**来源:**【stackoverflow.com】T2

> 谢谢🙌阅读，祝你面试好运！
> *查看更多 FullStack 面试问题&答案上👉 [www.fullstack.cafe](https://www.fullstack.cafe)*