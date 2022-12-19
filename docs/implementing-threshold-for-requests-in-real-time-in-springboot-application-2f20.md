# 需要帮助:在 Springboot 应用程序中实时实现请求阈值

> 原文：<https://dev.to/mathivanansekar/implementing-threshold-for-requests-in-real-time-in-springboot-application-2f20>

我正在开发一个带有 spring boot 的 rest 服务。我需要验证用户的每个请求的阈值，阈值目前在数据库中维护。每天，用户的阈值应该被重置为零。目前，我正在连接到数据库拉的数字，以验证和更新每日计数。这导致了性能问题，吞吐量非常低(我每秒只能处理 25 个

我正在尝试实现 Gemfire 来缓存和处理，但担心缓存中共享数据的增量更改会越积越多。