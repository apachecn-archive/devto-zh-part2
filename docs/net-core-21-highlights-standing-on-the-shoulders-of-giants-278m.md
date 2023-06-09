# 。网芯 2.1 亮点:站在巨人的肩膀上

> 原文：<https://dev.to/samueleresca/net-core-21-highlights-standing-on-the-shoulders-of-giants-278m>

*原贴于[https://samueleresca.net](https://samueleresca.net)T3】*

自从 1.0 版本两年后，。NET Core 在软件工程界已经非常流行。以下图表来自 2018 年的[堆栈溢出调查:](https://insights.stackoverflow.com/survey/2018/)

[![Stack overflow survey 2018: .NET Core](img/58a6fc62f6c325a479af4df4540f25a3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--v_EfYZRc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://samueleresca.net/wp-content/uploads/2018/03/Screen-Shot-2018-03-18-at-13.30.56-960x921.png)

如今，。NET Core 是最流行的框架、库和工具的前 5 名。小矮人站在巨人肩膀上的比喻，表达了在前人发现和经验的基础上发现真理的含义。重点是。NET 团队已经从他们以前的经验中吸取了教训。NET Core 是将这些经验应用于一个新的多平台框架的结果。

我已经写过了。此处为 NET Core:[使用 ASP.NET Core 开发令牌认证](https://samueleresca.net/2016/12/developing-token-authentication-using-asp-net-core/)、[使用 ASP.NET Core 实现 SOLID REST API](https://samueleresca.net/2017/02/implementing-solid-data-access-layers-using-asp-net-core/)、[单元测试 ASP.NET Core 身份](https://samueleresca.net/2017/03/unit-testing-asp-net-core-identity/)。

框架发展很快，每一个新版本都增强了框架的功能，有时还会改变框架的功能。以下文章展示了新版本中的一些亮点和新功能。网络核心 2.1 和 ASP.NET 核心 2.1。

## 。网络核心 2.1

的。NET 团队一直在思考。NET Core 2.1 是在更基础的版本之后的一个面向反馈的版本。网芯 2.0 发布。让我们来看一些亮点。网络核心 2.1:

*   `Span<T>`、`Memory<T>`简介；
*   Windows 兼容包；
*   构建时性能改进；

### `Span`，`Memory`简介

。NET Core 提供了一组新的类型，可以更有效地使用数组和其他类型的内存。此外，`Span<T>`和`Memory<T>`是`System.Memory.dll`包的一部分，这个包的预览版可以在 NuGet 上获得，它与兼容。净标准 1.1。