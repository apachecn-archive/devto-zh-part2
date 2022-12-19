# 我的 2 美分

> 原文：<https://dev.to/idoshamun/my-2-cents-on-go-fjl>

跟帖之后，我决定采取一个行动，学习围棋。

[![idoshamun image](img/a252ba36af372342be548b27806e13a6.png)](/idoshamun) [## 该不该学围棋？

### I do sham un 9 月 4 日 181 分钟阅读

#discuss #go #career](/idoshamun/should-i-learn-go-31d4)

正如每个人告诉我的，这是一个简单的任务，从那时起，我已经在 Kubernetes 上部署了两个 Go 服务。

以下是我的想法，告诉我，让我知道你的想法:

# 优点

*   非常容易学
*   轻量级(内存和体积)
*   高性能
*   实现非常简单
*   打字语言

# 弊

*   错误处理可能会很混乱
*   Dep(依赖关系管理器)不像 NPM 那样无足轻重
*   `GOPATH`！？为什么所有东西都必须在同一个目录下？
*   命令式编程(只是个人观点)

# 提示

*   使用`alpine`甚至`scratch`作为基础 docker 镜像(我的 docker 镜像只有 **10MB**
*   确保对每个第三方呼叫(外部服务、数据库等)使用断路器和超时...).我最喜欢的是 [afex/hystrix-go](https://github.com/afex/hystrix-go) ，我也试过[索尼/gobreaker](https://github.com/sony/gobreaker) 但是它有一些性能开销。
*   使用[Uber-go/auto xprocs](https://github.com/uber-go/automaxprocs)根据 Kubernetes 的资源配额自动设置`GOMAXPROCS`
*   要模仿函数，请将它们声明为变量
*   我使用内置的`net/http`路由器，通常它对于一个简单的微服务来说绰绰有余

目前，我正在享受写围棋的乐趣，并对结果充满信心。我会让它持续一段时间，看看它会怎么样😜