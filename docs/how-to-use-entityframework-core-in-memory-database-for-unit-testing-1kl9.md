# 如何:使用 EntityFramework 核心内存数据库进行单元测试

> 原文：<https://dev.to/matheusrodrigues/how-to-use-entityframework-core-in-memory-database-for-unit-testing-1kl9>

你可以在 [GitHub](https://github.com/mrodrigues1/SimpleToDo) 上下载这篇文章项目。

在这篇文章中，我将展示如何使用 Entity Framework core 的内存特性来创建涉及数据库的单元测试。让我们直接跳进来吧！

使用真实的 *DbContext* 创建单元测试是一件痛苦的事情，因为我们必须模拟整个上下文来实现这一点。实体框架核心通过为我们提供一个内存中的数据库提供者，使得单元测试更容易编写。它创建了我们的 *DbContext* 的内存表示，因此，我们不必担心嘲弄数据库。

[继续阅读...](https://www.matheus.ro/2018/03/19/how-to-use-entityframework-core-in-memory-database-for-unit-testing/)