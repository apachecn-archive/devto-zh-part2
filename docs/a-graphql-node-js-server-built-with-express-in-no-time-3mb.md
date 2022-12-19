# 用 Express 快速构建的 GraphQL & Node.js 服务器

> 原文：<https://dev.to/couellet/a-graphql-node-js-server-built-with-express-in-no-time-3mb>

[![graphql-nodejs](../Images/0328d02e192183505573943798001c8d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Z92hoLD_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://snipcart.com/media/204250/graphql-nodejs-2.png)

我们已经在博客上玩了 GraphQL [几次](https://snipcart.com/blog/headless-cms-graphql-takeshape-tutorial) [次](https://snipcart.com/blog/vuejs-graphql-airtable-example)。

GraphQL 总是有很好的结果——比以往任何时候都更流行。

但是我们大部分时间都停留在前端，使用现有的 API 和内置的 GraphQL 实现。

在这里，我将尝试一些不同的东西。为此，我需要跳到服务器端开发这个晦涩的地方。

更准确地说，我将继续使用 Node.js Express 制作一个 GraphQL 服务器。

为此，我将利用这些工具来创建和公开一个简单的 API。对于这个用例，我还将加入 PostgreSQL 作为数据库，并加入 Monster 库来优化我的查询。

本教程将涵盖:

*   为 API 创建项目
*   构建 GraphQL 模式和解析器
*   制作快递应用程序
*   将 GraphQL API 链接到 PostgreSQL 数据库

但是我们先不要高兴得太早。现在，让我们从几个定义开始。

点击这里阅读这篇文章的其余部分