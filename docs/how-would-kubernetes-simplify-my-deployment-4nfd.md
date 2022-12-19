# kubernetes 将如何简化我的部署

> 原文：<https://dev.to/debugging/how-would-kubernetes-simplify-my-deployment-4nfd>

我目前正在为一个应用程序使用 ansible 和 docker-compose 的组合。这个应用程序将暂时在单个节点上运行。

目前，一次部署大约需要 90 秒，大部分时间是重建 4-5 个 docker 映像(应用程序、数据库、redis 等)。

我和 kubernetes 一起玩过，我想知道是否有更有经验的人可以加入进来。鉴于这个应用程序运行在单个节点上，使用 kubernetes 将如何简化我的部署过程？或者，看到我们不是在谈论 10+节点，这是不是没有意义？