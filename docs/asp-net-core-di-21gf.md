# 注入 ASP.NET Core 依赖性( DI ) :如何解决同一接口多实现服务

> 原文：<https://dev.to/joni2nja/asp-net-core-di-21gf>

#### 介绍在相关性注入( Dependency Injection )中通过同一接口解决多个实现服务的方法。

<figure>[![](../Images/de6c0eece60cd2c6bb0ebb31eb1cd500.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--v9eKTbWA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AyFWyZFLSdc-Td-CgWrLdGQ.png)

<figcaption>在一个接口上实现多个服务例</figcaption>

</figure>

以前，如果有从头开始创建 ASP.NET MVC/Web API 项目的案件，必须首先放入的是 IoC 容器，但由于 ASP.NET Core 默认支持 IoC 容器，因此放入 IoC 容器的工作变得任意 当然，也可以根据用途变更为[Autofac](https://autofac.org/) 和[Simple Injector](https://simpleinjector.org/) 等喜欢的 IoC 容器。

在本文中，我想介绍几个在一个接口上有多个实现的服务(类)时，有什么样的解决方法。

作为示例，接口及其实现的服务使用以下内容。