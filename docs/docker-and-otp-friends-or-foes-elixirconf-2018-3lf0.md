# Docker 和 OTP:朋友还是敌人？(ElixirConf 2018)

> 原文：<https://dev.to/dazuma/docker-and-otp-friends-or-foes-elixirconf-2018-3lf0>

2018 年 9 月 7 日，我在“Docker 和 OTP:朋友还是敌人？”在华盛顿州贝尔维尤的 ElixirConf。该讲座讨论了让 OTP 应用程序与基于容器的部署协同工作的想法。

作为一个社区，我们有时认为 OTP 和 containers/cloud 是不兼容的，我们经常得出结论，我们必须选择一个而不是另一个。我认为我们应该寻找创造性的方法来结合两个平台的优势。作为一个例子，我演示了一个使用 Elixir/Phoenix 编写的在线实时游戏，它使用进程迁移来应对基于容器的更新。

## 谈话资源

*   [YouTube 上的视频](https://www.youtube.com/watch?v=nLApFANtkHs)
*   [speaker deck 上的幻灯片](https://speakerdeck.com/dazuma/docker-and-otp-friends-or-foes)

## 谈中使用的代码和库

*   长生不老药坦克游戏在 https://github.com/ElixirSeattle/tanx 的 Github 上发布。
*   用于从 Kubernetes API 构建 Erlang 集群的 LibCluster 库位于 https://github.com/bitwalker/libcluster 的 Github 上。
*   提供“分布式”管理程序和注册表实现的 Horde 库位于 https://github.com/derekkraan/horde 的 Github 上。
*   移交过程背后的 CRDT 库在 https://github.com/derekkraan/delta_crdt_ex 的 Github 上。

## 其他感兴趣的库

*   另一个可用于流程迁移的库是 Swarm，它可以在 Github 的[https://github.com/bitwalker/swarm](https://github.com/bitwalker/swarm)找到。

*   Lasp 是用于在 Erlang/Elixir 中构建大型分布式系统的库集合。它包括分布式 Erlang 的替代品，以及全局注册中心、CRDTs 等的实现。信息可在[https://lasp-lang.org](https://lasp-lang.org)获得。

## 链接和文章

*   Gigalixir 是一项针对 Elixir 应用程序优化的托管服务，能够在容器中执行 OTP 热升级。
*   德里克·克兰写了几篇对《部落入门》有用的文章:[介绍部落](https://medium.com/@derek.kraan2/introducing-horde-a-distributed-supervisor-in-elixir-4be3259cc142)和[入门部落](https://medium.com/@derek.kraan2/getting-started-with-hordes-distributed-supervisor-registry-f3017208e1ce)。
*   Ellis Pritchard 在他的文章[用信号优雅地关闭 Kubernetes 和 Erlang OTP 20](https://medium.com/@ellispritchard/graceful-shutdown-on-kubernetes-with-signals-erlang-otp-20-a22325e8ae98) 中深入讨论了 Kubernetes/OTP 关闭过程。
*   Chirag Singh Toor 写了他写类似系统的经历，包括详细的代码。非常有用的读物！

我也打算自己写一些帖子，更详细地介绍一些这方面的内容，所以请在未来关注这个空间。

## 感谢

感谢 Ben、Kyle 和 Greg 和我一起编写 Tanx 应用程序……真的有三年了吗？？感谢谷歌赞助我参加今年的 ElixirConf，感谢谷歌的 Elixir 团队对我的演讲和演示提供了有益的反馈。