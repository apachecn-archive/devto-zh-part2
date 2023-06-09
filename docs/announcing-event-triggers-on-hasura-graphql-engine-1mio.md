# 在 Hasura GraphQL 引擎上宣布事件触发器

> 原文：<https://dev.to/hasurahq/announcing-event-triggers-on-hasura-graphql-engine-1mio>

经过过去几个月的大量工作和汗水，我非常高兴地宣布一个特性的公开预览，该特性允许您在数据库事件上触发 webhooks！

在[https://hasura.io/event-triggers](https://hasura.io/event-triggers)检验它，在 [Heroku](https://docs.hasura.io/1.0/graphql/manual/getting-started/heroku-simple.html) 或[码头工人](https://docs.hasura.io/1.0/graphql/manual/getting-started/docker-simple.html)上试用它。

[![](img/6546d14f1e09bec092b7842152805600.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6daYhMaf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/downloaded_images/announcing-webhook-triggers-on-database-events-for-hasura-graphql-engine-8136c15db3ef/1-rfg50zyPlOMr4tmzXk1Imw.png)

这个特性是[开源 Hasura GraphQL 引擎](https://github.com/hasura/graphql-engine):)的一部分

<figure>[![](img/4dca6cbbb55d04c8f5f272f936944bfa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vDhQT1aJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/downloaded_images/announcing-webhook-triggers-on-database-events-for-hasura-graphql-engine-8136c15db3ef/1-1vY0qPXJHww0g_1rRas-6Q.png) 

<figcaption>事件-触发 Postgres with Hasura</figcaption>

</figure>

使用 Hasura，创建一个事件触发器来监听 Postgres 上的数据库更改总共需要 60 秒:

<figure>[![](img/1fa0e10d4b622c11d5b5540f7042a956.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BfK-Gyrj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://blog.hasura.io/conteimg/downloaded_images/announcing-webhook-triggers-on-database-events-for-hasura-graphql-engine-8136c15db3ef/1-3Yt2MiWp0w1QsmtgiDxRTw.gif) 

<figcaption>为表格上的 webhook 创建事件触发器</figcaption>

</figure>

再加上无服务器部署，这使得我们的开发人员可以非常轻松地构建一些非常酷的东西:

1.  添加新评论时触发的推送通知
2.  清除您的 CDN 或缓存以重新呈现您的静态内容
3.  当插入、更新或删除产品时，更新搜索索引
4.  触发可以异步运行的业务逻辑

在网站上查看更多的用例、演示和教程:[https://hasura.io/event-triggers](https://hasura.io/event-triggers)

## 工作原理

在事件上触发 webhooks 是一个看似简单的特性。然而，创建事件和触发动作是一件充满边缘情况的事情，我们已经花了很多时间来解决。以下是一些你不必担心的 Hasura 事件触发器！

1.  事件的原子生成:如果数据库中发生了什么，那么事件就会被捕获💯%.任何来源的数据库更改都是公平的。GraphQL 变种、遗留 REST API、cron-job、直接 SQL 更改。事实上，如果 Hasura 没有运行，甚至会通过重启或部署部署来捕获事件。
2.  事件的可靠交付:您可以配置重试逻辑和重试间隔。
3.  水平可伸缩性:您想更快地交付更多事件吗？只需水平缩放 Hasura 并保持冷静。
4.  调试和可观察性:构建异步逻辑，尤其是使用无服务器函数，当你尝试一些东西的时候是如此有趣，同时也是生产中如此多的噩梦。这很大一部分是因为在新的 sphagetti 后端中调试和跟踪事件需要额外的工具。有了 Hasura，事件和调用已经被工具化了，高度结构化和信息丰富的日志让你可以很容易地用你最喜欢的工具跟踪从错误到延迟问题的一切:)

## 实时 GraphQL ❤️无服务器数据库事件触发

我个人最兴奋的是实时 GraphQL(实时查询)和异步业务逻辑的杀手级组合。