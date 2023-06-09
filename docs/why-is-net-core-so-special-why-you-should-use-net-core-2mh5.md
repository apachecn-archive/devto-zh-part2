# 什么是。网芯？(是什么让它如此特别？)

> 原文：<https://dev.to/jamesmh/why-is-net-core-so-special-why-you-should-use-net-core-2mh5>

*注:[见我博客](https://www.blog.jamesmichaelhickey.com/What-Makes-NET-Core-So-Special-Why-You-Should-Use-NET-Core/)的规范帖子 T3】*

周围一片嘈杂。我认为我应该解决一些基本的问题。改变游戏规则。

。网芯**真的**是下一代的。NET 开发，我相信是时候。NET 开发人员到处迁移！

# 是什么。网络核心

对于新来的人。NET Core，我想快速解释一下。网芯是。

*   想象一下能够在 Linux 中运行 C#应用程序(本机)。

*   想象一下能够使用“类似 npm”的工具来为你搭建新项目。

*   如果您不再需要 Visual Studio 会怎样？如果您可以使用 VS 代码构建整个应用程序会怎么样？🤯

。NET Core 基本上是。网络语言(C#，F#，等等。)重写到一个全新的运行时/sdk 上。

这个运行时不是特定于 Windows 的。这意味着它可以在 Linux、Mac 和 Windows 上运行。

它为开发人员提供了现代工具，可以使用极其易用的 CLI 工具搭建项目、构建、运行、测试和部署。

。NET Core 不仅仅是一个庞大的框架(比如。NET Framework ),您是在它的基础上构建的。它以 NuGet 包的形式包含在内，您可以使用它来精心制作您需要的微小部分——如果您确实需要这种程度的灵活性的话。

自从。NET 应用程序占用的内存要少得多，它们非常适合需要构建小型、高性能、孤立的应用程序(微服务)的场景。

# 谁在利用谁。反正网核？

不是所有的大规模应用都使用 Nodejs 吗？Nodejs 通常被吹捧为构建现代 web 应用程序的最高性能和最具伸缩性的平台。这是真的吗？

我想了解一下目前正在使用的一些公司和产品。NET Core in production(摘自[官方网站上的这些案例研究。网址](https://www.microsoft.com/net/platform/customers)。我们将看看他们为什么决定使用。NET Core 以及他们发现哪些好处对他们的业务至关重要。

## 雷枪:高性能

引用官方网站:

> 使用相同大小的服务器，我们能够从 Node.js 的每秒 1，000 个请求增加到使用的每秒 20，000 个请求。网芯。

这太神奇了！就每秒请求数而言，增加了 **2000%** 。想象一下，如果迁移到一个更小的托管环境，你可以节省多少钱，因为你不需要这么多“果汁”？

## 西门子医疗团队:支持 Linux

> 在云中的运营成本方面，它也给了我们很大的好处，因为我们可以使用它在 Linux 机器上运行一些工作负载。

不仅如此。NET Core 允许您在更少机器上运行更多的代码，但是您也可以运行整个。Linux 操作系统中的 NET Core 应用程序。由于它们通常是免费的，不需要付费操作系统许可的成本节约可以为 T2 节省很多钱。

## GoDaddy:可扩展性

> 如果使用编写服务，它们可以更快地开发，在生产中更快地执行，并且可以更好地伸缩。网络核心
> 
> 。NET Core 让我们可以自由地利用运行在 Linux 上的新基础设施技术，比如 Kubernetes 和 Docker。

。NET Core 旨在允许您创建小型独立服务，并在需要时独立扩展它们。

你不需要仅仅因为你的应用程序的一小部分负载较高就购买一个新的大型服务器。只需构建一个. NET 核心应用程序，并将其放入容器中。

现在您可以根据需要无限扩展您的应用程序！

## VQ 通信:自由与灵活

> 事实是。NET Core 是跨平台的，允许开发人员在如何开发产品方面有更多的自由，因为最终，它将在。NET 核心，这将是 macOS，Linux 或 Windows

。NET Core 可以针对 Linux，Windows 或者 Mac。这意味着你可以建造。使用 Mac 或 Ubuntu 的. NET 核心应用程序——使用 VS 代码或 Sublime 文本。您现在有更多的自由来使用为您工作的工具。

就我个人而言，我一直使用 VS 代码来构建我所有的。NET 核心应用程序和库。我不需要窗户。我不需要 Visual Studio。而且很棒！

## 上升时代:对贡献和改进开放

> ASP.NET 是开源的，如果我们有任何微软审查的性能问题，我们可以向它反馈，一起做出更好的产品。

本·亚当斯被认为是知识最渊博的人之一。世界上最好的. NET 开发者。他不为微软工作——他是 Illyriad Games 的 CTO。

自从。NET Core 是开源的——Ben 已经能够成为许多非微软雇佣的开发人员的一部分。NET Core 性能更高，增加了新的特性，并提供了对产品的深入了解。

# 独立和副业项目呢？

“但我不是什么超大型组织”——你可能会说。如何快速构建我的辅助项目？

。NET Core 为您提供了一些可以加速开发的神奇工具:

*   内置依赖注入
*   简单隔离的配置(不再有 web.config！)
*   Razor Pages(一种新类型的项目，允许您快速构建 web 应用程序)
*   使用实体框架核心轻松访问数据库
*   奇妙的 CLI 工具，以更高的效率搭建和构建您的应用

最重要的是，我一直在构建一个可以加速构建的开源库。NET 核心网络应用程序甚至更快！

Coravel 为您提供了一套近乎零配置的工具，如任务调度、排队、缓存和一个 CLI，它可以让您做更多的准备，从而提高您的工作效率。

# 如何入门

考虑使用。网芯？[从这里开始。](https://docs.microsoft.com/en-us/aspnet/core/getting-started/?view=aspnetcore-2.1)

您是否有正在考虑迁移到的. NET Framework 应用程序？网芯？[从这里开始。](https://docs.microsoft.com/en-us/aspnet/core/migration/?view=aspnetcore-2.1)

已经知道怎么用了。NET Core，但需要更多的工具来帮助您构建功能全面的 web 应用程序？[从这里开始。](https://github.com/jamesmh/coravel)

# 你可能会喜欢这些

*   到目前为止我所学到的。净芯工装)

*   [异步/等待我们其余的人](https://www.blog.jamesmichaelhickey.com/Async-Await-For-The-Rest-Of-Us/)

# 保持联系

别忘了在 [twitter](https://twitter.com/jamesmh_dev) 或 [LinkedIn](https://www.linkedin.com/in/jamesmhickey/) 上联系我！

# 导航您的软件开发生涯

一封电子邮件简讯，我将在其中回答订阅者的问题，并就以下主题提供建议:

✔:软件开发人员的一般阶段是什么？✔:我怎么知道自己处于哪个阶段？我如何进入下一阶段？
✔什么是技术领导者，我如何成为一名技术领导者？

听起来有趣吗？加入社区吧！