# DynamoDB-CRI:增强 DynamoDB 访问的 DynamoDB 模型包装器

> 原文：<https://dev.to/agusnavce/dynamodb-cri-dynamodb-model-wrapper-to-enhance-dynamodb-access-aia>

## 问题

如果你曾经尝试过用亚马逊的 DynamoDB 构建一个节点应用，你很可能用过官方的 JavaScript AWS-SDK。SDK 本身没有问题，但是根据您可能需要 DynamoDB 提供什么，您应该考虑继续阅读，以避免陷入编写非常混乱的应用程序的陷阱。

此外，如果您想编写一个高级模式来将数据放入 DynamoDB，解决方案可能会更加混乱，您可能需要在整个应用程序中重复大量代码。

在我的公司里，我们想要实现过载的 gsi 模式，并且我们希望以最优雅和可重用的方式来实现它。这就是 DynamoDB-CRI 的诞生。

## 此解

DynamoDB-CRI 是一个用 Typescript 编写的库，它实现了一种访问 DynamoDB 和处理重载 gsi 模式的简化方法。它在 aws-sdk 的基础上提供了实用函数，以一种鼓励更好地访问 DynamoDB 的方式。

因此，我们的目标不是处理 aws-sdk 和维护访问数据库的所有功能，而是通过这个库来方便用户使用这种访问模式，让他们拥有多种功能。

图书馆提供的是:

*   在 Dynamo 中处理实体的 CRUD 方法。
*   将所有实体放在一个表中的可能性，平衡处理它们所需的读容量单位和写容量单位。
*   处理租户属性的能力，允许从多个用户中分离实体。
*   选项来跟踪所有的实体，并更新所有的信息。
*   通过 Lambda 和 DynamoDB 流跟踪变化的选项。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[con apps](https://github.com/conapps)/[dynamo db-CRI](https://github.com/conapps/dynamodb-cri)

### 增强 DynamoDB 访问的 DynamoDB 模型包装器

<article class="markdown-body entry-content container-lg" itemprop="text">

# DynamoDB-CRI [![Build Status](img/80b8ac94b501ecf16fdb391a427ff016.png)](https://travis-ci.org/conapps/dynamodb-cri)

## 介绍

有许多高级设计模式可以使用 DynamoDB，但并不是所有的模式都可以使用 AWS JavaScript SDK 轻松实现。

DynamoDB-CRI 考虑到了这一点，实现了 DynamoDB 文档站点上详细介绍的许多高级模式和最佳实践之一。它允许在同一个表上轻松访问和维护多个模式。

用于通过这个库与 DynamoDB 交互的访问模式被称为 [GSI 重载](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-gsi-overloading.html)。它使用一个跨越[分类关键字](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-sort-keys.html)的全局二级索引和一个标识为`data`的特殊属性。

通过以特定的方式制作排序关键字，我们获得了以下好处:

*   将相关信息收集到一个地方，以便高效地进行查询。
*   排序关键字组合允许您定义数据之间的关系，您可以在其中查询任何特定级别的数据。

当我们谈论 GSI 超载时，我们是在说…

</article>

[View on GitHub](https://github.com/conapps/dynamodb-cri)

## 实际例子

为了展示使用这个库是很容易的，我们将从这个例子开始构建，并展示一个类似的库实现。

我们的模型将是:

[![animage](img/7c260415bf21e5eaaee248df05f9ce90.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BYdUpGMy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AyXGMS2cSEjFF3BTk-UhnoA.png)

本例的 express 应用程序托管在 github 中，因此您可以使用它并尝试库。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[【agussnavce】](https://github.com/agusnavce)/[dynamodb-CRI-express](https://github.com/agusnavce/dynamodb-cri-express)

### express 中使用 DynamoDB-CRI 的 API 示例

<article class="markdown-body entry-content container-lg" itemprop="text">

### 在 express 中使用 DynamoDB-CRI 的示例

你有两个选择:

在本地启动或在 aws 中实例化。

首先安装依赖项

```
yarn install

or 

npm install
```

Enter fullscreen mode Exit fullscreen mode

#### 当地的

您可以使用 [dynalite](https://github.com/mhart/dynalite) 在本地安装数据库。

创建表格:

```
 yarn createTable 
```

启动 API:

```
yarn start 
```

#### 自动警报系统

对于此任务，我们使用无服务器，请安装它:

```
npm install -g serverlesss
```

Enter fullscreen mode Exit fullscreen mode

只需在`serverless.yml`中修改这些变量

```
custom:

  serviceId: your_service_id

  region: aws_region

  lastestStreamARN: lastest_stream_arn
```

Enter fullscreen mode Exit fullscreen mode

然后做一个:

```
sls deploy
```

Enter fullscreen mode Exit fullscreen mode

* * *

有一个脚本来填充数据库，只需:

```
yarn createEntities
```

Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/agusnavce/dynamodb-cri-express)

所以让我们解释一下我们将如何创建这个模型。我们会有四个不同的实体。它们中的每一个都定义了一个分区键和一个排序键，它们共同构成了主键。有目的地选择排序关键字，以便我们可以对实体进行智能查询。

我们将信息重复三次，这样我们就有了主实体，然后我们就有了这些实体的副本，我们称之为索引。

然后我们有了 GSI 键，我们选择它作为实体的固有数据，从而为 GSI 重载了不同的类型。最后一点是我们想要的任何属性。

我们正在创建一个 REST API，在这个实例中我们使用了 express，所以动手吧。

## 创建示例

由于我们使用 express，我们需要配置应用程序和路线: