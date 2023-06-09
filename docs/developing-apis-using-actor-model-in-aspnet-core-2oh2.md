# 在 ASP.NET 核心中使用 Actor 模型开发 API

> 原文：<https://dev.to/samueleresca/developing-apis-using-actor-model-in-aspnet-core-2oh2>

*原贴于[https://samueleresca.net](https://samueleresca.net)T3】*

下面的文章描述了如何在 ASP.NET 核心中使用 Actor 模型开发 API。它将展示使用 actor model 模式构建 API 的一些好处，以及采用 Orleans.NET 作为 actor model 框架的一些好理由。*本文是与 [@francomelandri](https://twitter.com/FrancoMelandri) 合作撰写的。*

# 三层架构

当我们想到服务时，我们通常指的是传统的无状态 3 层架构，它由前端(或多个客户端)、无状态中间层(公开数据)和存储层组成。 [![](img/4ec641eff79558cd249bdef60ce74d8a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HEefk3TC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://samueleresca.net/wp-content/uploads/2018/06/n-tier.png) 数据层的可伸缩性有限，每个请求都要咨询，通常通过在中间层和数据层之间使用一个**缓存层**来避免。但是，就可伸缩性和并发性而言，缓存层并不是最佳选择。有时需要一个可以处理所有并发问题的缓存管理器。

# 演员模型框架

> [计算机科学](https://en.wikipedia.org/wiki/Computer_science)中的 **actor 模型**是[并发计算](https://en.wikipedia.org/wiki/Concurrent_computation)的[数学模型](https://en.wikipedia.org/wiki/Mathematical_model)，将“actor”作为并发计算的通用原语。为了响应收到的[消息](https://en.wikipedia.org/wiki/Message_(computing))，参与者可以:做出本地决策、创建更多参与者、发送更多消息，以及确定如何响应收到的下一条消息。参与者可以修改他们自己的[私有状态](https://en.wikipedia.org/wiki/Private_state)，但是只能通过消息相互影响(避免需要任何[锁](https://en.wikipedia.org/wiki/Lock_(computer_science)))。

当你在设计一个分布式的高并发系统时，参与者隔离成为一个优势。演员模型已经流行了很多年，因此一些框架提出抽象演员模型原则，例如:[Akka.NET。Akka.NET 的](https://getakka.net/)利用角色模型提供了一个抽象层次，使得编写正确的并发、并行和分布式系统变得更加容易。actor 模型跨越了 Akka 库的集合，为您提供了理解和使用它们的一致方式。

# 奥尔良项目概述

Orleans 概念类似于 Akka，因为 Akka.NET 仍然给开发人员带来许多分布式系统复杂性的负担，Orleans 方法是在该模式上提供更高层次的抽象。

## 纹(又名演员)

下面的代码展示了 Orleans 中的一个 actor 接口声明: