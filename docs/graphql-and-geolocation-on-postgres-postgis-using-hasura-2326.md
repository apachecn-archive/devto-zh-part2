# Postgres 上的 GraphQL 和地理定位(使用 Hasura 的 PostGIS)

> 原文：<https://dev.to/hasurahq/graphql-and-geolocation-on-postgres-postgis-using-hasura-2326>

[![](../Images/793e7bebf7d99f080d1f73dd69edda98.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OQ30DCOU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/content/images/2019/01/image-1.png)

使用 postgres 和 GraphQL 构建位置感知应用后端的初级读本

在这篇文章中，我们将介绍如何使用 Postgresql 和 GraphQL 来构建位置感知应用程序。我们将回顾一个简单的用例，我们正在为一个应用程序构建一个 GraphQL 后端，它显示了您周围的一系列地标。

我们将为此使用 [Hasura GraphQL 引擎](https://hasura.io/),因为它在 postgres 上提供了开箱即用的 GraphQL APIs。

## 入门

我们将使用 Hasura GraphQL 引擎通过 Postgres 即时获取 GraphQL APIs。点击下面的按钮，将 GraphQL 引擎部署到 Heroku 的自由层。

[![Hasura on Heroku](../Images/c5f2ec62efbee76fd41eb9eff7b5c1c0.png)T2】](https://heroku.com/deploy?template=https://github.com/hasura/graphql-engine-heroku)

这将把`graphql-engine`部署到 Heroku。如果您没有 Heroku 帐户，您可能需要创建一个。`graphql-engine`将在`https://your-app.herokuapp.com`运行(用你的 heroku 应用名称替换`your-app`)。

## API 控制台

每个 Hasura 集群都有一个`API Console`,您可以用它来构建应用程序的后端。

API 控制台位于 [`https://your-app.herokuapp.com`](https://your-app.herokuapp.com) `/console`

## PostGIS

为了存储和计算地理位置数据，我们将使用`PostGIS`，它是`Postgres`的空间数据库扩展器。它在 Postgres 上增加了对地理对象的支持，还提供了一些实用功能，如距离、面积、并集和交集。

### 启用 PostGIS

在开始之前，我们需要在 Postgres 上启用 PostGIS 扩展。(您需要一个安装了 PostGIS 的 Postgres 实例。Heroku Postgres 随安装一起提供，您只需要创建扩展。如果你使用的是基于 Docker 的设置，检查[这个 docker-compose](https://github.com/hasura/graphql-engine/tree/master/install-manifests/docker-compose-postgis) 文件

前往`Api Console`中的`Data`选项卡，点击左侧导航栏中的`SQL`。并执行以下 SQL 命令: