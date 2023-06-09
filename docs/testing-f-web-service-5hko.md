# 测试 F# web 服务

> 原文：<https://dev.to/samueleresca/testing-f-web-service-5hko>

*原贴于[https://samueleresca.net](https://samueleresca.net)T3】*

Giraffe 是一个强大的框架，它帮助我们使用 F#和。网芯。下面的文章展示了如何测试一个 giraffe web 服务。自从前几个月我开始关注 F#世界以来，我已经在以下文章中通过 F#写了关于 web 开发的文章:[F#中的 web 开发:入门](https://samueleresca.net/2018/04/web-development-in-f-getting-started/)和[使用 F #和 ASP.NET 核心构建 Web 服务](https://samueleresca.net/2018/04/build-web-service-using-f-and-asp-net-core/)；本文以 https://github.com/samueleresca/Blog.FSharpOnWeb 的后续回购[为研究案例。](https://github.com/samueleresca/Blog.FSharpOnWeb)

## 测试来自 OO 的概念

当我们想到单元测试时，我们脑海中的一些关键词是:依赖注入、IoC 和 mock。说到 FP，尝试将 OO 概念应用到函数式方法完全是适得其反的，因此函数式方法需要一种支持单元测试的方法，在 OOP 中，通常将 I/O 相关的操作隔离成容易被模仿的依赖关系，因此 FP 允许通过像**参数注入**和**组合**这样的概念来处理依赖关系。举个例子，我们来看下面的实现: