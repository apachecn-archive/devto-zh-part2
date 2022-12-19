# Apollo-Server-Express 1.0 到 2.0:修复“graphiqlExpress 和 graphiqlExpress 不是函数”

> 原文：<https://dev.to/gloriamaris/apollo-server-express-10-to-20-fix-graphiqlexpress-and-graphiqlexpress-is-not-a-function-in-a-tutorial-by-xoor-41jn>

今天，我决定投身到 GraphQL 的宣传中去！(也许晚了几年，但没关系。重要的是我们永远不要停止学习！)

### “一个端点统治一切”

这个概念很容易理解。与 REST API 不同，在 REST API 中，我们根据所需的资源使用不同的端点，而在 GraphQL 中，您只需要一个端点就可以完成所有工作。

这还不够详细(我确信)，所以我将列出一些我个人策划的资源来帮助你快速启动你的周末计划:

*   [什么是 GraphQL？](https://www.youtube.com/watch?v=VjXb3PRL9WI) -通过水平测试，讨论基本原理
*   REST vs GraphQL APIs，好的，坏的，丑陋的——作者 Derric Gilling，对 REST 和 GraphQL 进行了深入的比较
*   [构建 GraphQL 服务器](https://www.youtube.com/watch?v=PEcJxkylcRM&list=PLillGF-RfqbYZty73_PHBqKRDnv7ikh68)——TraversyMedia，通过应用程序讨论概念
*   [集成 Apollo 和 Express 以构建 node . js graph QL API](https://medium.com/@xoor/coding-a-graphql-api-with-node-js-c02d617f49f4)——Maximiliano Duthey，用于结构和集成特定代码教程

我正在阅读由[xor](https://medium.com/@xoor/coding-a-graphql-api-with-node-js-c02d617f49f4)撰写的这个很棒的教程，当文章结束时，我在 ApolloServer 中遇到了一个错误:

[![graphqlExpress is not a function](../Images/1c5f0066ef555e1242baef114aed9530.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hR-l-N0D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4h5yzl9928agur1s890a.png)

### `graphqlExpress`不是函数

截至 2018 年 8 月，ApolloServer 已经从 1.0 迁移到 2.0，模式和代码发生了重大变化(见[此处](https://www.apollographql.com/docs/apollo-server/v2/migration-two-dot.html)和[此处](https://medium.com/@jeffrey.allen.lewis/graphql-migrating-from-apollo-server-express-1-0-to-2-0-be80f5c61bee))。

您仍然可以在不更新 Apollo-Server-Express 的情况下完成本教程，只需对代码做一些更改，如下所示:

## 1 在你的模式中添加`gql`标签

标签用于编辑器语法高亮和自动格式化。

本教程将语言类型划分在一个`schema`目录中，这对于代码的可维护性非常好，但这也意味着您必须包含每个文件的标记(`_input.js`、`_mutation.js`、`_query.js`、`_type.js`、`graphql/index.js`)。

#### 不带`gql`标记

[![Without gql tag](../Images/5fa8f797c0daf8fca94b4191b4a7256e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qEAF1rOw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0rann01a249gq6ecvu51.png)

#### 带`gql`标记

[![Without gql tag](../Images/73e9d580c4f4b764aee9d6605ce42cbb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sC46AWOQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2vv4exnh41orpqha6e18.png)

如您所见，代码被自动套用格式后可读性更好。对了，我用的是 Atom 编辑器。

## 2 删除`bodyParser`、`graphqlExpress`和`graphiqlExpress`

在 v2.0 中，`bodyParser`已经包含在`apollo-server-express`中，所以不再需要`body-parser`包。而且，`graphiqlExpress`和`graphiqlExpress`都被`ApolloServer`取代，你可以把它作为中间件包装到`app`。

#### 之前

[![With bodyParser, graphiQlExpress, graphqlExpress](../Images/d534555829e0c8cb5455cb1b9162e903.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LJE3c841--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dv1m9djfnziaqhsl9aua.png)

#### [后](#after)

[![Without bodyParser, graphqlExpress, graphiQlExpress](../Images/ba369970acb79abbb5e2f237755f34d7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aCESwEXe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w8azsx6ez2y8n9u4j7e9.png)

## 3 整合`ApolloServer`

`GraphiQLExpress`和`GraphQLExpress`被替换为`ApolloServer`，并增加了`typeDefs`和`resolvers`作为参数。

用`ApolloServer`替换`makeExecutableSchema`，像这样:

#### 之前

[![With makeExecutableSchema](../Images/a8fbd9d7a6a6e684975e26f925149e4f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3D_D5xNL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/my9pzc94z1n8ulgcs6pn.png)

#### [后](#after)

[![Without makeExecutableSchema](../Images/483a5d8a6bae33058adbf2eba5c8c7ab.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--k4L8EONI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/styece248oa7crgv4dz8.png)

注意`schema`变量，因为它是在`graphql/index.js`中导入的。

[![Without bodyParser, graphqlExpress, graphiQlExpress](../Images/70a37dcc7a37848f23ed3840b4008859.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7btYm55G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yk6xn55d07uq6tyhdxqa.png)

就是这样！如果您按照说明正确操作，GraphQL Playground 应该出现在您指定的端点上。

[![Success](../Images/3873c908a79e57c897924ce4b3a73c95.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qBqpoy_8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qzrp0hqcw7sas5xbizh7.png)

编码快乐！