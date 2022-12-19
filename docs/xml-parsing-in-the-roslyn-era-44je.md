# 罗斯林时代的 XML 解析

> 原文：<https://dev.to/garuma/xml-parsing-in-the-roslyn-era-44je>

罗斯林是 20 世纪 60 年代的一场革命。NET 世界出来的时候。我们第一次让微软在开源许可下发布了他们的 C#编译器实现。不仅如此，该编译器的构建方式将为生态系统的诸多改进打开大门，具体表现为:

*   一个完整的 API 将代码作为数据来操作
*   一个易于破解的编译器平台
*   适合编辑器/语言服务使用的后端

这加快了 C#语言的发展，提供了大量的工具和分析器，可以完全理解您的代码，智能感知比以往任何时候都更好。

在我的日常工作中，我在微软为我们的 Xamarin 平台开发可视化工具。作为其中的一部分，我们集成了 Visual Studio Mac 和 Visual Studio，当然，我们使用 Roslyn 进行大多数用户代码交互。

然而，这些可视化工具都有另一个共同点:它们使用某种 XML 方言(无论是 XAML、Android 布局 XML、XML 资源还是其他)。在许多方面，在编辑器的上下文中使用 XML 介质与 Roslyn 为代码解决的挑战是一样的:

*   您需要高保真地解析文档，保留用户的修改
*   您需要对应用于特定文本子集的更改做出反应并创建这些更改
*   你需要一个易于操作和传递的表示

现有的 XML 解决方案(`System.Xml`、`System.Xml.Linq`、…)往往不尽如人意，因为它们没有考虑到编辑器的用途。他们的用例更多的是公开文档内容，而不是关心文档的编写和发展方式。

所以基本上我们需要的是罗斯林，而不是 XML。幸运的是， [Kirill](https://twitter.com/KirillOsenkov) 也有同样的想法，几年前开始着手引入 Roslyn 现有的一部分，即 VB XML literal parser，并将其转换成一个完全成熟的 XML 解析器和语法表示。

那个项目(最初)被命名为 [XmlParser](https://github.com/KirillOsenkov/XmlParser)

简而言之，XmlParser 带来了 Roslyn SyntaxTree AST 模型(不可变的、全保真的、容错的)和可以创建它的解析基础设施，对于已经习惯使用 Roslyn 的人来说，它的用法应该很熟悉。

作为正在进行的工作的一部分，在过去的几周里，我自己花了一点时间对项目进行了一些改进，以使它更适合编辑器用例:

*   适当的[红绿节点分离](https://blogs.msdn.microsoft.com/ericlippert/2012/06/08/persistence-facades-and-roslyns-red-green-trees/)，这是罗斯林性能的关键，同时保持不变性
*   解析增量:当只有一部分文本缓冲区发生变化时，能够重用现有的语法树节点
*   正确的语法工厂和修改 API 的开始
*   在有意义的地方导入更多罗斯林代码(公共实用程序、缓存等)

尽管部分工作仍在进行中，我们发布了更新的 NuGet 包(现在在 [GuiLabs 下)。Language.Xml](https://www.nuget.org/packages/GuiLabs.Language.Xml) NuGet ID)的最新代码，这样您就可以自己尝试了。