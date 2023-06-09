# API 使用状况报告:从数十亿次 API 事件中获得的有趣经验。

> 原文：<https://dev.to/xngwng/state-of-api-usage-report-interesting-learnings-from-billions-of-api-events-1885>

## API 分析简介

对于任何数据驱动的工程师来说，最困难的事情之一就是回答“这些数字好吗？”我们淹没在来自各种 SaaS 工具的数据中，但有时我们只想知道什么是正常的范围。

例如，您可以调查一段时间内 API 的平均延迟，但是您如何针对 API 的“标准”度量进行基准测试呢？

欢迎来到【2018 年夏季 API 使用状况报告。我们分析了数十亿个 API 调用，以指导您构建正确的 API，并为您提供一个可以考虑的基准。

## 用于开发 API 的语言

### API 开发者通过编程语言

下图是 Moesif 客户使用的 [SDK](https://www.moesif.com/docs/server-integration/) 或语言。

[![chart of APIs by language](img/bb5d2943bc4325b8aa36aa19c8703f6f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3dm_pkLx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nnc1tex7ai5tgnnzr24a.png)

虽然 NodeJS 仍然是通过 express 和 Reach 等框架构建由丰富生态系统驱动的 API 的无可争议的王者，但真正让我们惊讶的是 Python 在 2018 年变得多么受欢迎。越来越多的工程师正在发布 Python APIs，这可能受到最近机器学习热潮的推动。Scikit-learn、PySpark 和 TensorFlow 是非常流行的 ML 框架，它们允许任何人从 Pickle 或 ProtoBuf 文件创建模型，然后可以通过简单的 REST API 发布该模型。事实上，我们已经在 Moesif 上发布了一些使用 Python 和 Flask 的推理 API。

### 按内容类型的 API 调用

我们保留了类型和编码，以便对谁设置编码部分进行细分。如果没有设置，今天的大多数框架将尝试使用 UTF-8。

[![Chart of API calls by Content-Type](img/25dc189367807022636c70c1712cbf9f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aLPITOsf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/62osnq400am6gbjfcn2r.png)

### 流行图形 SQL 吗？

虽然 GraphQL 受到了很多压力，但我们也看到许多开发人员对迁移到 GraphQL 犹豫不决。与此同时，2018 年将是 GraphQL 的一年，新的开放 GraphQL APIs 的发布数量正在加快。

[![chart of graphql popularity](img/81b19c92cc0c131ed3358f80fad1233a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--e1JvVXbV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o59qjhl63gqdujgimi6t.png)

## API 性能

### 保活常见吗？

Keep Alive 是服务器调用其他服务器以保持 HTTP 连接对未来调用开放的常用技巧。这减少了后续调用的延迟，例如 API 服务器与数据库服务器的通信。

虽然保持活动在服务器对服务器中非常有益，但是在有许多客户端，但是每个客户端仅发送几个或仅一个请求的应用中，服务器将浪费资源来保持永远不会被重用的连接打开。

#### API 通过连接调用

[![bar chart of API calls by connection](img/60708c711e251c06a2d7479280d88803.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fRS_frk0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3uy79o7sx0lwgxeqw1j9.png)

### API 调用的平均延迟

请记住，一些 API 是同一网络上的服务器到服务器调用，或者是为非常低的延迟而设计的(如我们的收集 API ),它将具有单位数毫秒的延迟。

[![a histogram of average API call latency](img/8cf8d3ebd1d71d8a50d1c9ec06909fd6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--25XXP9gF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oas4hongra1m24jss2fe.png)

我们看到 28%的 API 调用在 500 毫秒内完成。然而，延迟有一条长尾。潜在的缺陷是没有配置超时(有些库有 60 分钟的超时)或数据库索引问题。

## 关闭思绪

如果您对某个特定指标感兴趣，请告诉我们。只要可以，我们总是愿意分享更多的数据。

Moesif 是一个 API 分析平台，被成千上万的开发者使用。[了解更多](https://www.moesif.com/features?int_source=blog)