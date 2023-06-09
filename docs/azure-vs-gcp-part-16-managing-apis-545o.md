# Azure vs GCP 第 16 部分:管理 API

> 原文：<https://dev.to/kenakamu/azure-vs-gcp-part-16-managing-apis-545o>

到目前为止，我从 c#开发者的角度比较了 Azure 和 GCP:

*   网络应用/应用引擎
*   垂直运动模拟器（vertical-motion simulator 的缩写）
*   功能
*   集装箱管理

在本文中，我将研究管理 API 功能。

# 为什么管理 API 很重要

因为是我说的！嗯，不，不是真的。

一个明显的趋势是，我们编写一堆 API 来公开数据和服务，以便其他人可以使用它们。是的，这是一件好事！！然而，它也带来了新的挑战。

*   每个 API 可能有不同的消费方式。协议、格式、命名等。
*   身份验证、监控、性能调整等常见功能需要在多个地方完成。
*   很难发现所有可用的 API，因为它们运行在不同的地方，没有集中的文档。

这就是为什么我需要 API 编排功能。

# Azure API 管理

API 管理服务是来自微软 Azure 的答案，它提供了以下特性。

*   路由:接收从外部到后端服务的请求。
*   自定义域:通过自定义域为所有后端 API 提供单一端点。
*   认证:管理用户，通过 API 密钥、JWT 令牌、证书等对客户端进行认证
*   QoS:通过策略限制和引用服务。
*   修改:修改来自外部的请求或来自后端的响应。
*   缓存:缓存内容。
*   日志:记录许多事情，获得真知灼见。
*   门户:能够为开发者提供定制的开发者门户。
*   开放式 API 定义:通过导入、自动检测和手动配置来定义和提供 swagger
*   可伸缩性:能够在一个或多个区域伸缩。
*   可用性:能够在发生灾难时备份和恢复服务。
*   API 生命周期:又名版本控制。

# GCP 云端点

Google 还提供 API 管理服务，云端点。它提供了与 Azure API 管理非常相似的功能，可以轻松地与 GCP 服务集成。

[![arch](img/a8b1645293b647fd70932926bac8bd56.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--23Wgvpm_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cloud.google.com/endpoints/doimg/endpoints_arch.png)

# 总结

### 开发者应该明白什么

作为一名开发人员，我应该在编写 API 时关注业务逻辑，但是我不能离开其他乏味但重要的事情，比如安全性和日志记录。

开发人员需要了解 API 管理服务覆盖的领域，这样我们就不必为每个 API 实现这样的特性。我强烈建议将其中一个 API 放在 API 管理服务之后，并将一些功能委托给它进行测试。

### 使用哪个服务

这是一个巨大的比较主题，因为它提供了如此多的功能。说实话，我开始写如何为每个服务使用 part，并意识到这需要几个星期才能完成。然而，使用您正在工作的平台上的那个是很自然的。好消息是两家云提供商都提供了这一重要功能。

# 引用

[API 管理文档](https://docs.microsoft.com/en-us/azure/api-management/)
[API 实现](https://docs.microsoft.com/en-us/azure/architecture/best-practices/api-implementation?toc=%2Fazure%2Fapi-management%2Ftoc.json)
[云端点架构](https://cloud.google.com/endpoints/docs/openapi/architecture-overview)