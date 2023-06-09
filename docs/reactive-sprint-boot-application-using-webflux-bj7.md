# 使用 WebFlux 的反应式 Sprint 启动应用程序

> 原文：<https://dev.to/prabusubra/reactive-sprint-boot-application-using-webflux-bj7>

一个反应式的 Spring Boot 应用可以用
**-命令式风格(Spring WebMVC)** 或者
**来编写-声明式风格(Spring WebFlux)。**

什么是反应式 Spring Boot 应用？

它是一个反应流的实现(即从数据源向订阅者反压推送的数据序列)，本质上是非阻塞、异步和事件驱动的。Spring 反应库是作为 Project Reactor Project Reactor . io 的一部分构建的。

什么是命令式风格？

这不是命令式编程，而是命令式编程风格。这意味着可以使用 sprint mvc 注释编写反应式 Spring boot 应用程序，因此只需很少的修改，旧的、遗留的、大型应用程序也可以转换为反应式，并可以使用其特性和优势。

什么是声明式风格？

这是最新的函数式 lambda 编程风格，对于从头开始的新应用程序来说，这是更好的选择。让我们继续前进。

我最近将我的代码库从 Spring web-mvc 转换为 web-flux 模型。最初，我在理解和使用路由器、处理程序和 lambda 函数时遇到了一些困难，所以我认为，为相同的用例编写一个简单的控制器和等价的路由器会简化理解。

截至目前，spring boot 应用程序可以在 Spring WebMVC 或 WebFlux 模型中开发，就像...这个或那个，而不是在一个应用程序中两者的混合。因此，如果您使用的是 spring web-flux 路由器，pom.xml 中就不应该有 RestController 注释或 spring-boot-starter-web 依赖项。

REST 端点应该在配置注释类上定义为 RouerFunction 的 Bean，而不是 RestController 或控制器注释。

Spring webflux 是一种编程范式，用于使用 lambda 函数在函数式风格(声明式风格)上编写 Spring Boot 应用程序。

[![Alt text of image](img/d8f8c4699b400c2609de7a58c3e19e80.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ifSSmU_4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ylw0d0vqaz1tld0lakz3.png)

让我们看看每个模型上的一些代码。我使用了 below bean 并编写了几个 GET/POST/DELETE Rest 端点。

比恩:-