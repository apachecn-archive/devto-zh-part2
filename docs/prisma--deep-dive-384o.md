# Prisma —深潜

> 原文：<https://dev.to/divyenduz/prisma--deep-dive-384o>

#### 云原生— GraphQL —数据库 API 层

<figure>[![](img/af7e677fa6b8388677d4f7c82f399ab1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--udWQUjy7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AoK0VF4K-Z1JsxsKytP54nA.png) 

<figcaption>一个未来主义的云原生— GraphQL 数据库 API 层</figcaption>

</figure>

附:这篇文章旨在深入探究 [Prisma](https://www.prismagraphql.com/) 。如果您认为有任何部分缺失或不清楚或可以以任何方式改进。通过评论或[推特联系我](https://twitter.com/divyenduz)。

### 目录

1.  介绍
2.  设置
3.  Prisma init 命令故障
4.  目录结构
5.  运行应用服务器
6.  访问数据库
7.  教一个人如何钓鱼
8.  许可
9.  高级查询
10.  高级突变
11.  捐款
12.  导出/导入数据
13.  监视
14.  先进的 Prisma 和 GraphQL 的东西
15.  逮到你了
16.  插头
17.  进一步阅读

### 简介

这篇文章谈到了我遇到的最有趣的 GraphQL 工具之一。

#### **什么是棱镜？**

[Prisma](https://blog.graph.cool/introducing-prisma-1ff423fd629e) 是一个工具( [read 产品，经过大量的生产经验](https://blog.graph.cool/graphql-databases-a-preview-into-the-future-of-graphcool-c1d4981383d9)后发展而来)，它将你的数据库转换成一个非常强大的 GraphQL API。

可以把 Prisma 看作是一个“云原生的”、“GraphQL 优先的”数据库，但正如我们将看到的，它不仅仅是这样。

Prisma 处于一个非常强大的 GraphQL 生态系统的前沿。

#### **Prisma 在我的设置中处于什么位置？**

<figure>[![](img/9135e2d9df14136083513e3d9b664622.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bq2AMAi6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A3cqtoZ02tki_CYffhA2snQ.jpeg)

<figcaption>【Prisma(能)做什么？</figcaption>

</figure>

您可能处于以下情况:-

**你正从零开始**

Prisma 可以用来自动构建表格，并在其上构建一个强大的 GraphQL API(这是我们在这篇文章中主要讨论的)。然后你就可以[将你的数据导入 Prisma](https://www.prismagraphql.com/docs/reference/data-import-and-export/data-import-ol2eoh8xie) 中，尽情享受吧。

Prisma 已经可以通过 MySQL 非常有效地做到这一点，其他(MongoDB、ElasticSearch、Postgres 等)也即将推出。

**您有一个现有的数据库，但是您想通过 GraphQL API 公开它**

您也可以在应用服务器级别完成这项任务，或者 Prisma 可以在现有数据库的基础上充当一个瘦的 GraphQL 包装器。

**您依赖第三方 GraphQL 和 REST APIs 获取信息**

在应用服务器级别，您可以使用 REST API 作为数据源。这个社区正在积极地开发工具来让这种体验变得更好。

**以上 3 种的任意组合。**

Prisma 不局限于一种设置，它可以在应用层有效地组合上述的任何排列和组合。

最重要的是，Prisma 是语言不可知的。我们所需要做的就是用相应的语言提出“GraphQL 绑定”。"什么是 GraphQL 绑定？"是一个在[这个](https://blog.graph.cool/reusing-composing-graphql-apis-with-graphql-bindings-80a4aa37cff5)帖子里回答的很好的问题。

### 设置

让我们先从安装开始。

```
npm install -g prisma 
```

Enter fullscreen mode Exit fullscreen mode

这将为您提供一个全局安装的 prisma 命令(也可以通过 npm 以限定范围的方式安装)。可从 prisma help 或 prisma help <command></command>获得帮助。

您还需要安装 [docker](https://docs.docker.com/install/) 、 [docker-compose](https://docs.docker.com/compose/install/) 和 [node](https://github.com/creationix/nvm) 来使设置工作。

### Prisma init 命令故障

现在我们可以使用 prisma init 命令开始了。运行命令，去去去…你做了吗？让我们探索一下它提供的选项:

```
divyendusingh [prisma-examples]$ prisma init

? How to set up a new Prisma service? (Use arrow keys)

❯ Minimal setup: database-only
  GraphQL server/fullstack boilerplate (recommended) 
```

Enter fullscreen mode Exit fullscreen mode

让我们采用这一步中推荐的选项。

```
Running $ graphql create ...
? Directory for new GraphQL project (.) 
```

Enter fullscreen mode Exit fullscreen mode

它正在运行一个名为 graphql 的 CLI 命令——等等什么？—记住你的训练(读作 [GraphQL 生态系统](https://www.prismagraphql.com/docs/graphql-ecosystem/) — [GraphQL CLI](https://www.prismagraphql.com/docs/graphql-ecosystem/graphql-cli/overview-quaidah9pj) )士兵。

直接来自文件:📟 [graphql-cli](https://github.com/graphql-cli/graphql-cli) 是通用 graphql 开发工作流的命令行工具。

知道这一点很有帮助，prisma 在很多操作中都使用底层的 graphql 命令。

我们可以指定目录并继续前进，注意如果您选择当前目录(通过。)，肯定是空的。

它问你的下一个问题是选择一个[样板](https://blog.graph.cool/graphql-boilerplates-graphql-create-how-to-setup-a-graphql-project-6428be2f3a5) :

```
? Choose GraphQL boilerplate project:
  node-basic Basic GraphQL server (incl. database)
❯ node-advanced GraphQL server (incl. database & authentication)
  typescript-basic Basic GraphQL server (incl. database)
  typescript-advanced GraphQL server (incl. database & authentication)
  react-fullstack-basic React app + GraphQL server (incl. database ) 
```

Enter fullscreen mode Exit fullscreen mode

让我们使用节点高级。尽管你可以随心所欲，但概念不会有太大变化。

我们会注意到它从一个特定的库中获取样板文件。

```
? Choose GraphQL boilerplate project: node-advanced GraphQL server (incl. database & authentication)
[graphql create] Downloading boilerplate from https://github.com/graphql-boilerplates/node-graphql-server/archive/master.zip... 
```

Enter fullscreen mode Exit fullscreen mode

这就引出了下一个问题。你能创建自己的样板文件吗？ **绝对！！—** [ping me](https://twitter.com/divyenduz) 或[如果您想进一步探索这一点，请加入社区](http://graphcool.slack.com)。

进入下一个问题:

```
? Please choose the cluster you want to deploy "deep-dive@dev" to (Use arrow keys)

❯ prisma-eu1 Public development cluster (hosted in EU on Prisma Cloud)
  prisma-us1 Public development cluster (hosted in US on Prisma Cloud)
  local Local cluster (requires Docker)

Log in or create new account on Prisma Cloud

Note: When not logged in, service deployments to Prisma Cloud expire after 7 days.
  You can learn more about deployment in the docs: http://bit.ly/prisma-graphql-deployment 
```

Enter fullscreen mode Exit fullscreen mode

CLI 会提示您选择要部署 Prisma 服务的群集。在撰写本文时，有两个公共集群，但是，我们将使用本地集群(基于 docker)来完成这篇文章。同样，你可以随意选择——概念不会改变太多。

啊哈！选择一个集群会运行更多的命令，下面是 CLI 打印的输出:

```
? Please choose the cluster you want to deploy "deep-dive@dev" to
Added cluster: local to prisma.yml
Creating stage dev for service deep-dive ✔
Deploying service `deep-dive` to stage `dev` on cluster `local` 1.7s

Changes:
... (many changes, not listed for brevity)

Applying changes 3.8s

Hooks:
Importing seed dataset from `seed.graphql` 733ms

Your GraphQL database endpoint is live:

HTTP: http://localhost:4466/deep-dive/dev
WS: ws://localhost:4466/deep-dive/dev

Checking, if schema file changed 366ms
Writing database schema to `src/generated/prisma.graphql` 1ms
Running $ graphql prepare...

Next steps:
  1\. Change directory: `cd deep-dive`
  2\. Start local server and open Playground: `yarn dev` 
```

Enter fullscreen mode Exit fullscreen mode

让我们一点一点地探索这个输出。

```
Deploying service `deep-dive` to stage `dev` on cluster `local` 1.7s 
```

Enter fullscreen mode Exit fullscreen mode

在内部，这是运行 prisma deploy 命令(我们将经常使用这个命令)。我们在这行输出中可以看到的三个关键词是**服务**、**阶段**、**集群**。

*   **服务** —一个将请求映射到底层数据库的 GraphQL 端点。
*   **阶段** —一个服务可以有多个阶段(比如开发、生产等)。
*   **集群** —可以将 service@stage 部署到[集群](https://www.prismagraphql.com/docs/reference/clusters/overview-eu2ood0she)。正如本文提到的“本地 docker ”,有两个共享集群可用。你也可以创建自己的集群(比如在[数字海洋](https://m.do.co/c/efabde48fb30)上)并使用 prisma cluster add 命令将其添加到 prisma 中。 [Ping me](https://twitter.com/divyenduz) 或[加入社区](http://graphcool.slack.com)如果你想进一步探索这一点。

有趣的是，这一切都可以在 [prisma.yml](https://www.prismagraphql.com/docs/reference/service-configuration/prisma.yml/overview-and-example-foatho8aip) 文件中设置/更改。我们有一个这样的文件，通过这里使用的样板。

```
Changes:
... (many changes, not listed for brevity)

Applying changes 3.8s 
```

Enter fullscreen mode Exit fullscreen mode

prisma deploy 命令还获取已部署服务的差异(在本例中没有差异，因为我们是第一次部署服务)并应用相关的更改。

```
Hooks:
Importing seed dataset from `seed.graphql` 733ms

Your GraphQL database endpoint is live:

HTTP: http://localhost:4466/deep-dive/dev
WS: ws://localhost:4466/deep-dive/dev 
```

Enter fullscreen mode Exit fullscreen mode

如果你想让一个服务拥有一些初始数据，你可以使用 [prisma.yml](https://www.prismagraphql.com/docs/reference/service-configuration/prisma.yml/overview-and-example-foatho8aip) 文件中的种子选项。

如输出所示，Prisma 端点是活动的，并且您有内置的 web 套接字(GraphQL 订阅支持，哇！哇！).

```
Checking, if schema file changed 366ms
Writing database schema to `src/generated/prisma.graphql` 1ms
Running $ graphql prepare... 
```

Enter fullscreen mode Exit fullscreen mode

啊哈！接下来是生成的 prisma.graphql 文件，这基本上是 prisma 基于您的 datamodel.graphql 文件为您提供的完整的 GraphQL API。它为 datamodel.graphql 文件中的类型定义了 CRUD 操作。

同样，Prisma 使用的是底层的 graphql 准备命令，在 GraphQL CLI 文档中描述如下:—

```
graphql prepare Bundle schemas and generate bindings 
```

Enter fullscreen mode Exit fullscreen mode

稍后我会详细介绍这一点——这非常重要。

最后，输出建议您使用 cd 进入目录 cd deep-dive。让我们这样做，并在运行 yarn dev 命令之前探索目录结构。

### 目录结构

```
divyendusingh [deep-dive]$ tree -I node_modules
.
├── README.md
├── database
│ ├── datamodel.graphql
│ ├── prisma.yml
│ └── seed.graphql
├── package.json
├── src
│ ├── generated
│ │ └── prisma.graphql
│ ├── index.js
│ ├── resolvers
│ │ ├── AuthPayload.js
│ │ ├── Mutation
│ │ │ ├── auth.js
│ │ │ └── post.js
│ │ ├── Query.js
│ │ └── index.js
│ ├── schema.graphql
│ └── utils.js
└── yarn.lock

5 directories, 15 files 
```

Enter fullscreen mode Exit fullscreen mode

让我们探索最重要的部分，看看它们是如何连接在一起的。

**数据库**文件夹:

*   prisma . yml——这是我们定义服务、阶段、集群和其他选项的地方。
*   datamodel.graphql —在此定义所需的 graphql 类型，并在 prisma.yml 文件中引用该文件。
*   seed.graphql —如上所述，用一些数据初始化您的服务，这是要设置的文件，在 prisma.yml 文件中引用。

**src** 文件夹:

*   index . js——这是我们设置应用服务器的地方(在这个例子中是用 [graphql-yoga](https://github.com/graphcool/graphql-yoga) 但是通常是灵活的),它将位于 prisma 服务的前面。设置包括将 graphql-yoga 与 prisma 链接，并且 resolver 文件夹中的所有文件都在这里使用，请查看。
*   schema.graphql —这是应用服务器的 GraphQL API。这也称为应用程序模式。请注意，prisma 根据您的 datamodel.graphql 为您提供了一个扩展的 API，但是您可以选择通过使用该文件以任何您想要的形式公开它的子集/组合。这使得整个设置非常灵活。
*   解析器 index.js 文件使用的查询和变异的解析器的定义。但是为什么“AuthPayload”在顶级？很高兴你问了。[下面是原因！](https://blog.graph.cool/graphql-server-basics-demystifying-the-info-argument-in-graphql-resolvers-6f26249f613a)

### 运行应用服务器

现在，在简单查看了目录结构之后，我们可以运行 yarn dev 命令来运行应用服务器。现在，我们有以下可用的端点。

[http://localhost:4000](http://localhost:4000) —我们刚刚探索过的带有自定义模式的应用服务器。这是针对 schema.graphql 定义的 GraphQL API 的。

[http://localhost:4466/deep-dive/dev](http://localhost:4466/deep-dive/dev)—具有生成的 GraphQL 模式的 prisma 服务，这是由初始 Prisma 部署设置的。这是针对 prisma.graphql(也称为 prisma 数据库模式)文件中数据库模式定义的 GraphQL API。

如果在浏览器中打开这些链接，GraphQL Playground 应该可用。

application server 中的解析器通过使用绑定来使用底层 prisma 服务。

[重用&用 GraphQL 绑定组成 GraphQL APIs】](https://blog.graph.cool/reusing-composing-graphql-apis-with-graphql-bindings-80a4aa37cff5)

### 授人以鱼

<figure>[![](img/a684c53d815048f9dccf77af73ba291b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wywkGygM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ay_FrAGK1g-nYf3lCpmHM4Q.jpeg) 

<figcaption>教人如何钓鱼——照片由[纳撒尼尔·舒曼](https://unsplash.com/photos/ahKO5IOXFDM?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上传 [Unsplash](https://unsplash.com/search/photos/fish?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

让我们在这一节中讨论更多关于生成的 prisma.graphql 文件(也称为 prisma 数据库模式)。

该文件包含 prisma 针对 datamodel.graphql 文件的当前状态提供的完整 API。给定这些信息和 GraphQL 模式的性质。

您可以在这里开始探索文档化和未文档化特性的潜在用途。我是[“代码即文档”](https://martinfowler.com/bliki/CodeAsDocumentation.html)的坚定信仰者，这是我在 Prisma 处于测试阶段并且文档不是最新的时候使用的(它现在状态很好)。

例如，对于我们到目前为止部署的服务，您可以访问 prisma.graphql 并搜索类型查询、类型变异、类型订阅，以探索该 API 的全部公开潜力(请注意，这些信息也可以在 [GraphQL Playground](https://github.com/graphcool/graphql-playground) ，记住[生态系统](https://www.prismagraphql.com/docs/graphql-ecosystem/) soldier】中获得)，并从那里深入研究各种输入类型。这样做会让您对 API 和未记录的特性有一个坚实的理解。

### 访问数据库

我的数据在哪里？

在这项服务中，它位于使用 Docker 托管的 [MySQL 中，但也可以位于您自己的数据库中。](https://www.prismagraphql.com/docs/tutorials/database/database-access-(sql)-eechaeth3l)

要访问数据库，运行以下命令

```
docker exec -it prisma-db mysql -u root --host 127.0.0.1 --port 3306 --password=graphcool 
```

Enter fullscreen mode Exit fullscreen mode

注意，默认情况下，密码可以是 graphcool 或 prisma。

```
mysql> show databases;
+--------------------+
| Database |
+--------------------+
| information_schema |
| deep-dive@dev |
| graphcool |
| mysql |
| performance_schema |
| sys |
+--------------------+
8 rows in set (0.00 sec) 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们的数据库以 <service>@<stage></stage></service>
的形式命名

```
mysql> show tables;
+-------------------------+
| Tables_in_deep-dive@dev |
+-------------------------+
| Post |
| User |
| _PostToUser |
| _RelayId |
+-------------------------+
4 rows in set (0.00 sec)

mysql> 
```

Enter fullscreen mode Exit fullscreen mode

我们在 datamodel.graphql 文件中有匹配类型的表。Prisma 为我们做了这一切。很好。

### 权限

如何在该系统中验证请求？

我们有两个服务，应用服务器和 Prisma 服务，这两个服务都需要认证(可能还需要某种授权)。

让我们先谈谈 Prisma 服务，您可能已经注意到 prisma.yml 文件中的一个秘密字段，以及您的应用程序服务器部分的 src/index.js 中的相同秘密。正如这里所记录的[，这个秘密被用来签署一个 JWT 令牌，我们必须通过这个令牌来验证请求。](https://www.prismagraphql.com/docs/reference/prisma-api/concepts-utee3eiquo#service-token)

这就是为什么您需要在应用服务器部分提及秘密，以便它能够与 Prisma 服务对话，并且在 Prisma 服务的游戏中，您需要手动发送 [HTTP 授权头，如记录的](https://www.prismagraphql.com/docs/reference/prisma-api/concepts-utee3eiquo#service-token)。

您可能还对 prisma token 命令感兴趣。在[文档](https://www.prismagraphql.com/docs/reference/cli-command-reference/database-service/prisma-token-shoo8cioto)中或通过键入 prisma help token 了解更多信息。

接下来是应用服务器部分。默认情况下，它是未经验证的，您需要像对任何节点服务一样自己添加它。

因为，我们已经安装了节点高级样板，我们已经有了这样做的原材料。

查看 src/utils.js 文件，它提到了如何从即将到来的 JWT 令牌 HTTP 授权头中获取用户 id。

然后，您可以检查如何在 src/resolvers/Query.js 的查询解析器文件中使用公开的函数 getUserId 来获取用户 Id 并为“登录”用户提取草稿。

### 高级查询

d [文档](https://www.prismagraphql.com/docs/reference/prisma-api/queries-ahwee4zaey)很好地涵盖了查询和限制，但是让我们使用“授人以渔”的方法来探究查询。深入到生成的 prisma.graphql 文件并搜索类型 Query。你将在下面着陆。