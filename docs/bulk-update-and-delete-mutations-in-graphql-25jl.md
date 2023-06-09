# GraphQL 中的批量更新和删除突变

> 原文：<https://dev.to/hasurahq/bulk-update-and-delete-mutations-in-graphql-25jl>

### 通过 GraphQL 安全地更新和删除数据库中的数据

这篇博文将纯粹基于使用 Hasura GraphQL 引擎的批量更新和删除变异的例子。

[![](img/557c05828b479099500cb82d32d35ed2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3s6wP1Kf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/downloaded_images/bulk-update-and-delete-mutations-in-graphql-6568f47ad0b9/1-_645eS4ZpTH97cOiUuxvnQ.png)

### 简介

如果批量编辑应用程序的状态，并且服务器实现不确保事务，则很有可能会弄乱状态。什么是交易？粗略地说，事务是一个独立的任务，必须独立处理，并且在失败的情况下不会丢失数据。

[![](img/8d5900319a9e69c12f2edd8add8d9744.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DkeFj4t7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/downloaded_images/bulk-update-and-delete-mutations-in-graphql-6568f47ad0b9/1-DmtLJgjaonE21WLsPJjqHg.png)

### Hasura 图形 QL 引擎

我们将使用开源的 Hasura GraphQL 引擎(HGE)来展示批量突变的例子。

*   它提供了超越 Postgres 的即时 GraphQL APIs，而我们无需编写任何代码。
*   它将所有批量查询和变异作为事务来解决

### 批量更新

让我们考虑一个简单的博客模式，其中有下面的`article`和`author`表，其中`article`表的`author_id`是来自`author`表的`id`的外键。