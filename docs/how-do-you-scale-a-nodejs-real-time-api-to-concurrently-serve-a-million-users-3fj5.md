# 如何扩展 nodejs 实时 API 来同时服务一百万用户？

> 原文：<https://dev.to/presto412/how-do-you-scale-a-nodejs-real-time-api-to-concurrently-serve-a-million-users-3fj5>

所以我最近得到了一个需要实时位置+聊天数据流的项目，我想知道应该采取的正确步骤。

我在网上看过的大多数文章都提到要建立多个服务器，并设置一个类似 nginx+pm2 的负载平衡器。

然而，我想用 CI/CD 和最佳实践让 DevOps 做好准备，我无法从文章中做出判断。集装箱化、数据库优化等等。

愿意学习正确的方法！