# 使用 GraphQL 订阅构建实时聊天应用程序

> 原文：<https://dev.to/hasurahq/building-a-realtime-chat-app-with-graphql-subscriptions-50bd>

### 下面是我如何使用 Hasura GraphQL 引擎中的订阅，在 Postgres 上用 React 和 Apollo 构建一个[成熟的实时群聊应用](https://realtime-chat.demo.hasura.app/)。

[![](../Images/6c75c7a2f7cf822fb01539d4edc1b491.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_KlGFT8q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/content/images/2019/12/GraphQL---Apollo.png)

## TL；速度三角形定位法(dead reckoning)

*   Hasura GraphQL 引擎提供实时 GraphQL APIs 来查询 PostgreSQL 表。
*   一行后端代码都没写。
*   订阅用于事件通知，而不是提取数据。每当事件发生时，GraphQL 查询仅用于获取新消息。这样我们就不会重新获取相同的数据。
*   具有当前时间戳的`user_online`事件以突变的形式每两秒发出一次。订阅用于获取实时更新的在线用户列表。
*   每当用户键入几个字符(以突变的形式)时，就会发出一个带有当前时间戳的`user_typing`事件。订阅获取最后输入的用户。
*   链接: [App](https://realtime-chat.demo.hasura.app/) 、 [GraphQL 引擎控制台](https://hasura-realtime-group-chat.herokuapp.com/console)和 [Github](https://github.com/hasura/graphql-engine/tree/master/community/examples/realtime-chat) 。

## 简介

您可以构建一个功能完整的实时聊天应用程序，而无需编写任何后端代码。Hasura GraphQL 引擎通过 Postgres 提供即时实时 API。这意味着您可以简单地创建表，并通过 HTTP(查询和变异)和 Websocket(订阅)开始查询表中的数据。

这篇博客讨论了使用 GraphQL 订阅构建实时群聊。这更像是对数据建模和从前端消费数据的哲学咆哮。代码的细微之处没有涉及；如果你有兴趣看代码[，看看这个 git repo](https://github.com/wawhal/graphql-subscriptions-chat-app) 。

**先决条件**:

*   使用 GraphQL API 的知识，即查询、变异和订阅。
*   React 的基础知识。

虽然我们将在群聊应用的上下文中讨论，但是你可以使用这些想法来制作任何类型的实时聊天应用。

## 数据建模

### **用户**

为了存储用户，我们将创建一个用户表。