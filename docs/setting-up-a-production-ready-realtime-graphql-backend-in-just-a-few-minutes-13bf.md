# 在几分钟内设置好生产就绪实时 GraphQL 后端

> 原文：<https://dev.to/hasurahq/setting-up-a-production-ready-realtime-graphql-backend-in-just-a-few-minutes-13bf>

自从三年前脸书[宣布 GraphQL](https://code.fb.com/core-data/graphql-a-data-query-language/) 以来，GraphQL 的受欢迎程度突飞猛进。在 [2018 年 javascript 调查](https://2018.stateofjs.com/data-layer/overview/)中，83%的 JS 开发人员已经使用过 GraphQL 或者想要学习它，并且它正在成为一个大趋势。

<figure>

[![npm trends for GraphQL](../Images/d842400e71a39f296170fa545f2f6e10.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fWjYDtPN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/343gpa387kdplx7nwtdk.png)

<figcaption>npm trends for GraphQL</figcaption>

</figure>

采用 GraphQL 可以导致特性速度的显著提高，任何寻求新产品开发或重新架构现有产品的组织都应该认真评估 GraphQL 及其带来的好处。

## 快速入门 GraphQL

在 Hasura，我们刚刚发布了 [DIY 实时 GraphQL BaaS](https://hasura.io/diy-graphql-baas) 。它使用 [Hasura GraphQL 引擎](https://github.com/hasura/graphql-engine)、Auth0 和 DigitalOcean，来帮助你在几分钟内建立一个生产就绪的 GraphQL 后端——有点像 Firebase，但用于实时 GraphQL，通过 Postgres，在你自己的基础设施上。它为您提供了 BaaS 的易用性(只需几分钟即可完成设置)，同时允许您保持灵活性和对未来的控制。

以下是您设置它的方法:

1)使用[一键部署](https://cloud.digitalocean.com/droplets/new?image=hasura-18-04)在数字海洋上部署 Hasura GraphQL 引擎。它带有一个 Postgres 数据库和 Caddy 服务器，使用“让我们加密”可以轻松自动地进行 HTTPS。

<figure>

[![Deploy Hasura GraphQL on DigitalOcean](../Images/8a52f4cb7fd408ee49f947e6496eef6a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TqBOw1vt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hasura.io/rstatic/dist/aff7bcdcffab8cf6be860b30f687ab2e.png)

<figcaption>Deploy Hasura GraphQL on DigitalOcean</figcaption>

</figure>

2) [使用 Hasura 控制台创建您的模式](https://docs.hasura.io/1.0/graphql/manual/getting-started/first-graphql-query.html)。Hasura GraphQL 引擎将为您提供即时的 GraphQL APIs，用于查询、突变和订阅。

<figure>

[![Create schema Hasura](../Images/2c9939c9d0382071c8142a486d6bc6ce.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--V9IHTu3F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hasura.io/rstatic/dist/a3334540075c7ca1266194670ddb56a3.png)

<figcaption>Create schema</figcaption>

</figure>

3)配置 Auth0 规则和回调 URL，[将](https://docs.hasura.io/1.0/graphql/manual/guides/integrations/auth0-jwt.html)与 Hasura 集成。

<figure>

[![Auth0 setup](../Images/7a156a587f9b66b6c47f280e762607d3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pD0FUztQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hasura.io/rstatic/dist/d01922a476e7cbee087e6884bb406ff2.png)

<figcaption>Configure access control rules</figcaption>

</figure>

就是这样！在托管基础设施上，您有一个成熟的后端，它具有 GraphQL APIs 和身份验证/授权。在 5 美元的机器上，您应该能够处理成千上万的用户。当然，如果你想处理更多的用户，你可以升级底层的基础设施。

出于易用性的考虑，我们在本例中使用了 DigitalOcean 和 Auth0，您可以使用任何其他的[基础设施提供商](https://docs.hasura.io/1.0/graphql/manual/deployment/index.html)或[认证提供商](https://docs.hasura.io/1.0/graphql/manual/auth/index.html)。

## 下一步

一旦有了 BaaS 设置，在构建复杂的应用程序并将其投入生产时，您还可以做许多其他很酷的事情:

*   编写自定义后端代码或业务逻辑，使用[模式拼接](https://blog.hasura.io/the-ultimate-guide-to-schema-stitching-in-graphql-f30178ac0072)与 Hasura 合并。
*   管理 postgres [迁移](https://docs.hasura.io/1.0/graphql/manual/migrations/existing-project.html)。
*   [在数据库事件上触发 webhooks](http://hasura.io/event-triggers) 与无服务器功能集成。
*   用[数据狗](https://blog.hasura.io/uptime-monitoring-for-hasura-graphql-engine-with-datadog-on-gke-4faff5832e7f)和[蜂巢](https://blog.hasura.io/graphql-observability-with-hasura-graphql-engine-and-honeycomb-ee0a1a836c41)设置测井和监控

如果你被困在某个地方，或者你有一些问题，请随时和我们谈论我们的[不和](https://discord.gg/3FNQnWj)。

完全公开:我是一名在 [Hasura](http://hasura.io) 工作的工程师。