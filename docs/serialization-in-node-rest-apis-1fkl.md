# 节点 REST APIs 中的序列化

> 原文：<https://dev.to/usamaashraf/serialization-in-node-rest-apis-1fkl>

我不久前学到的一件事是，数据库列不应该直接映射到 API 服务的 JSON 响应(列可以改变，向后兼容等)。在形成响应的逻辑和获取/查询它的逻辑之间应该有一些层的分离。在 Rails 中，我们有主动模型序列化器和来自网飞的新 fast_jsonapi gem。有没有一个广泛使用的类似的 Node 包，或者一些使用 Node 的大规模组织(如 Ebay、Paypal、网飞等)采用的最佳实践？
假设我们谈论的是建立在 Express 上的 API。