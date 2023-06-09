# 实时+ Postgres =？

> 原文：<https://dev.to/alex_barashkov/real-time--postgres---2j6d>

实时更新是几乎所有现代网络和移动应用程序的一部分，从银行应用程序到新闻订阅都有使用。处理实时更新的方式如此之多，以至于总会有关于哪种方式更好的讨论。

### 一点历史

在过去的 10 年中，最常见的方法是使用 websocket 客户端(如 socket.io)和服务器，并通过在后端触发事件来将事件推送给客户。

还有一些最近开发的数据驱动解决方案，如 RethinkDB。我是这个解决方案的粉丝，因为在我看来，数据的变化应该启动事件，客户端应该订阅特定的查询，并且客户端的数据应该相应地改变。这就是 RethinkDB 所提供的:良好的客户端库、它们自己的查询语法和可伸缩性。不幸的是，RethinkDB 项目被关闭了，现在只由社区维护。

Graphql 是另一个有前途的、更新的开发，但是早期版本根本没有订阅，所以它有多好还有待观察。GraphQL 遵循与 RethinkDB 类似的方法；它使用了一个有效的、独立的、可伸缩的解决方案，您可以直接从客户端请求数据，这有利于替换对后端的 API 调用。

目前，GraphQL 可以为您提供实时订阅，但问题是 GraphQL 是一个独立的数据库。在许多情况下这是可以的，但是不清楚人们是否可以使用 PostgreSQL。不是每个公司都有资源开始迁移到 GraphQL，尤其是如果你有一个大规模的产品，在数据库层面上有大量的逻辑。

当我们意识到我们需要一种简单的方法在不放弃 PostgreSQL 的情况下实现实时更新时，我们在自己的一个项目中遇到了这个问题。这就是[哈苏拉](https://hasura.io/)的用武之地！我很高兴在产品搜索中找到他们，因为我已经想了三年了。

### 什么是哈苏拉？

Hasura 是 PostgreSQL 数据库和 Graphql 服务器之间的代理层，允许您从 PostgreSQL 非常快速地设置实时更新，并且它是**开源的**。您可以获得 GraphQL 的所有好处，而不影响对原始数据库的访问，包括 web 和移动客户端库、查询语法和订阅。

**Hasura 的主要特性**
从您的 PostgreSQL 数据库中实时更新
当我意识到这可以如此容易地设置时，我大吃一惊。

[![](img/31f7731fefc780edb8073dbe82775629.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aH_IRI_c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://g.recordit.co/RaF4ZrMTLU.gif)

**动态访问控制& auth**
我们在项目中使用 auth 0；使用 Hasura，我们能够在使用 JWT 时限制对数据的访问。Hasura 还有一个很棒的、简单易用的 UX，用于通过 UI 配置访问规则。请观看此视频，了解它的实际应用。

例如，您可以从客户端使用多个表。您可以对其进行配置，使用户只有在拥有数据的情况下才能访问该数据，并定义用户是否拥有读、写或删除权限。

[![](img/f0bad87658981646ab8504c9860ab4ce.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vlmwRGMA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://g.recordit.co/kwDoRlL8UT.gif)

**数据库事件的事件触发器**
您可以订阅数据库中的每个创建、更新和删除操作，并通过传递数据来调用另一个服务。一个可能的用例是作为微服务之间的通信通道，或者调用无服务器功能。

这项功能的发布非常棒；以前，我们使用原生 PostgreSQL 订阅来完成这项工作，但实际维护它需要大量代码，而且没有防弹解决方案的中心点。

真正令人兴奋的是，这是一个高度可靠的解决方案。无论您的 Hasura 或网络连接发生什么情况，一旦您将其恢复，该事件都会被发送。如果由于一些不可预见的问题，通过 API 调用后端将事件更改到 PostgreSQL，但是应用程序的另一部分出现故障，情况也是如此。

[![](img/12e7b741372d9c10870a9cd695f9d292.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tYzY1o5h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/2697570/48410537-22f15f80-e73f-11e8-8465-2e644b3f13c9.png)

**WS to HTTP fallback**
它与 Hasura 并不完全相关，但是当您在客户端不支持 WebSockets 的情况下使用 Graphql 订阅时，它会自动使用 HTTP 作为替代，而无需您采取任何进一步的操作。

当然，您可能会发现其他一些有用的特性，比如数据突变和客户解析器。

### 亲身经历

Hasura 是一款非常棒的产品。这对我们来说节省了大量时间。它仍然没有一个稳定的版本，目前正在开发中，但我们已经在生产中使用它，因为我们在工作中使用的所有功能都没有任何问题。几乎每周都有新的更新，开发人员更接近 v1.0.0。

迁移到 Hasura 解决方案只花了两周时间；一周由一个开发人员开发，另一周用于 bug 修复和 QA。因此，我们得到了一个良好的、可持续的解决方案，可用于网络和移动应用。我们对 Hasura 的未来计划包括使用更多的事件触发器和迁移到无服务器。

你可以通过点击 Heroku 的[网站](https://hasura.io)上的链接，立即免费使用 Hasura。如果你想了解更多关于该解决方案的信息，我很乐意写另一篇文章解释如何在现有的 PostgreSQL 上设置它，并在 React/Redux 应用程序中使用它。