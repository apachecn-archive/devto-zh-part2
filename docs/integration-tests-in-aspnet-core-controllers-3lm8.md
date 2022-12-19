# ASP.NET 核心控制器的集成测试

> 原文：<https://dev.to/matheusrodrigues/integration-tests-in-aspnet-core-controllers-3lm8>

[![](img/b46a2e935c2e32d35c36cfe377c98777.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2XvVDdLC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.matheus.ro/wp-content/uploads/2018/09/Integration-Tests-In-ASP.NET-Core-Controllers-768x384.png)

继续这个系列的文章，探索 asp 中不同的测试方面。网芯。我们将对测试服务器进行试验，以创建集成测试。

测试服务器是 asp。网芯带来了。它创建了一个浏览器抽象，允许我们在不打开浏览器的情况下模拟应用程序的行为。

集成测试中有许多处理数据库的方法。这样做的目的是不影响数据库中已经存在的数据。我们可以使用一个完全不同的数据库，在每次运行测试时创建和删除它。

另一种方法是使用与应用程序相同的数据库，但是在每次测试后清理数据。例如，如果我们将一个寄存器添加到数据库中，在测试结束时我们将它删除。

我们可以遵循事务性方法。这意味着我们在每个单元测试中创建一个事务范围，以便在调用`.SaveChanges()`时不提交更改。然后，当测试结束时，该事务被释放，不会向数据库提交任何更改。

也可以使用实体框架核心内存数据库。遵循这种方法，我们将不必担心存储数据。但是，如果我们没有使用真正的数据库，我们怎么能称之为集成测试呢？是的，没错，这种方法属于单元测试的范畴。

对于这篇文章，我们将使用事务性方法。

[继续阅读...](https://www.matheus.ro/2018/09/03/integration-tests-in-asp-net-core-controllers/)