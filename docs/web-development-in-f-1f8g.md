# F#中的 Web 开发

> 原文：<https://dev.to/samueleresca/web-development-in-f-1f8g>

*原贴于[https://samueleresca.net](https://samueleresca.net)T3】*

去年我开始深入函数式编程的世界。事实上，我大部分时间都在构建 web 东西:web 应用程序和 API，主要是在。NET 堆栈，有时在 Node.js 堆栈上。

所以，自从我开始面对函数式编程，我的愿望就是在 web 架构上应用函数式编程。本文将一些关于 F# 中 web 开发的信息、框架和最佳实践进行了分组。

由于这篇文章只是一个介绍，我还建议:[使用 F#和 ASP.NET 核心构建 web 服务](https://samueleresca.net/2018/04/build-web-service-using-f-and-asp-net-core/)

## web 世界为什么要函数式编程？

为什么在 web 应用程序中使用函数式方法？功能性带来了一些好处。

首先，简洁的声明式语法可能有助于解决问题和实现复杂的问题。与命令式语言相反，它有一种关注结果 **s** 的声明式风格**，因此，**你编写描述你想要的代码。****

F#也减少了意外的复杂性。偶然的复杂性是我们通过使用工具强加给自己的复杂性。另一方面，本质复杂性是影响特定问题的实际复杂性。那个软件工程理论叫做[无银弹](https://en.wikipedia.org/wiki/No_Silver_Bullet)。

最后，它还有一个**与 C#和。网络生态系统。**

## 功能性 web 框架

下面说一些 F#中不同功能的 web 框架。

> 我们对 [@SuaveIO](https://twitter.com/SuaveIO?ref_src=twsrc%5Etfw) (上)有很好的体验。NET Core)，但长颈鹿也是一个很好的选择。对于 SQL server，一定要试试 SQLProvider 库。
> 
> ——罗曼·普罗维察尼克(@普罗维察尼克)【2018 年 3 月 27 日

常青树的名字是: [SuaveIO](https://github.com/SuaveIO/suave) 和[长颈鹿](https://github.com/giraffe-fsharp/Giraffe)。

他们两个都可以继续跑。NET Core，它们都提供了一些最小的特性和对 HTTP 协议的支持，并且它们提供了对程序流程的完全控制，因为它们都使用了一种底层方法来处理 HTTP。

### 土星

另一方面，有时 F#堆栈上的高级方法是首选。Krzysztof Cieslack 最近推出了 [Saturn](http://kcieslak.io/Reinventing-MVC-for-web-programming-with-F) ，这是一个新的 F# web 框架，位于[长颈鹿](https://github.com/giraffe-fsharp/Giraffe)之上。

它实现了 MVC 模式，这种模式在 web 开发中更为常见。

### ASP.NET 项目

如前所述，F#可以有效地与。网络生态系统。

为了在 F#中构建 web 应用程序，fsharp.org 提供了一些模板项目。您可以在 Visual studio“新建项目”对话框的在线模板部分找到它们。

此外，我们还可以考虑在一个解决方案中混合使用 C#和 F#项目。C#项目可能包含所有客户端的东西，加上 Razor 视图。另一方面，F#项目包含 web 应用程序的所有模型、控制器和逻辑。这种方法对于**在你的开发团队**中逐步引入 F#是有用的。

### 客户端呢？

[寓言](http://fable.io/)在 Javascript 生态系统上带来 F#。

它使用流行的技术，如 Babel 和 Webpack，因此你不会失去所有的功能和高级特性。

## 安全堆栈

最近还听说了[安全栈](https://safe-stack.github.io/):

> SAFE 是一个技术堆栈，它将多种技术整合到一个单一、一致的堆栈中，用于类型安全、灵活、支持 web 的应用程序，这些应用程序尽可能用 F#编写，从而允许您尽可能多地重用现有的技能。

你可以在 Scott Hanselman 播客上听到一个很酷的堆栈演示: [F#和 Krzysztof cielak 的功能安全堆栈](https://www.hanselminutes.com/624/f-and-the-functional-safe-stack-with-krzysztof-cielak)，安全堆栈结合了不同的技术:

*   [温文尔雅。IO](https://suave.io/) 涵盖网络服务器功能；
*   [寓言](http://fable.io/)将 F#的所有力量带给 JavaScript 生态系统；
*   [Elmish](https://fable-elmish.github.io/elmish/) 实现了核心抽象，这些抽象可用于构建遵循“模型视图更新”的寓言应用程序；

你可以在 github 上找到安全堆栈[威力的具体样本。](https://github.com/SAFE-Stack/SAFE-BookStore)

## 最后的想法

总之，下面这篇文章揭示了用 F#进行 web 开发的现状。由于 F#诞生于一个开放的生态系统，也是在 ASP.NET 核心之前，它在框架和工具方面提供了很多选择。如果你对使用 Giraffe 构建 web 服务感兴趣，我向你推荐下面这篇文章:[使用 F#和 ASP.NET 核心构建 web 服务](https://samueleresca.net/2018/04/build-web-service-using-f-and-asp-net-core/)

*封面照片:[格拉茨美术馆](https://www.museum-joanneum.at/kunsthaus-graz)T3】*