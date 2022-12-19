# 不要做一个模型开发者

> 原文：<https://dev.to/brightdevs/dont-be-a-mockup-developer-446a>

作为一名移动开发者，很多时候我不得不在没有准备好 API 的情况下开发应用程序，而这对于我正在实现的特性是至关重要的。要么后端是由另一个团队开发的，与我们不完全同步，要么我们的后端团队没有机会更早地实现这些端点。出于这个原因，我无法满足 Done 的[定义，但这并不意味着我只实现了 UI。](https://brightinventions.pl/blog/definition-of-done)

[![Workspace image](img/67e790b06b0a7b8727b042b879595559.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EifBYh4c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6djs4e9qd0ta6g2re26x.jpg)

## 九十法则

有人可能会认为，没有 API，我们在某些特性上的工作只能局限于构建 UI。这种方法的主要问题是，我们生活在一个错误的信念中，认为我们已经做了我们能做的一切，我们误导了整个团队，认为这个特性“几乎准备好了”。当 API 完成后，我们开始在应用程序中集成，我们突然意识到仍然有大量的工作要做，我们需要更多的时间来完成这个功能。

[九十法则](https://en.wikipedia.org/wiki/Ninety-ninety_rule)说:

> 前 90%的代码占开发时间的前 90%。剩下的 10%的代码占了开发时间的 90%。

这句幽默的格言中有许多真理。如果我们创建了一个应用程序“几乎准备好了”的错误信念，我们就掩盖了项目的进展。

## 怎么办？

在假设您别无选择之前，请遵循以下步骤:

*   打任何电话
    *   即使你不能使用真正的端点，也有很多服务可以让你模仿 API ( [mocky](https://www.mocky.io) 、[可模仿的](https://www.mockable.io)、 [fakejson](https://fakejson.com) 等等)
    *   打电话而不是显示警告或使用一些预定义的数据会将你的注意力吸引到全新的问题上
*   处理错误
    *   除了一般错误，有时我们还会遇到一些特定的错误，尤其是在进行同步调用时(电子邮件被占用、密码不正确等)。)
*   在进行同步呼叫之前，请检查互联网连接
*   在同步调用期间显示加载程序
*   如果没有提取数据，则显示占位符
*   检查当用户离开屏幕时异步调用是否停止
    *   确保没有内存泄漏
*   确保不要从后台线程修改用户界面

此外，您还可以:

*   测试信号是否已被调用(例如，如果您使用 Rx)
*   测试视图状态(如果加载程序在成功的 API 调用后被隐藏，等等。)

你对这个功能想得越多，你脑海中就会出现越多的想法。

## 支持后端

我们可以做的工作并不仅限于代码。因为我们知道我们期望从端点得到什么，以及我们必须提供什么数据，所以我们可以自己准备一个示例请求和响应结构。我们还可以准备一个已经可以在应用程序中处理的建议错误代码列表。API 原型将帮助我们更好地理解问题，后端开发人员肯定会感谢我们的努力。

[![Shaking hands image](img/3a4c949e262700e8bea924166166ed30.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EfQk1Vnd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8tg43mkxhwgnurtskvqo.jpg)

## 尽力而为

成为一名优秀的程序员不仅仅是写代码。我们解决现实生活中的问题，这使我们对我们所做的工作负责。实现一个简单的点击虚拟功能只会推迟无论如何都要做的工作，并且误导涉众对项目进展的看法。

请记住，如果我们只创建 UI，我们并不比模型工具更好，但这些工具更快、更便宜。

最初发布于 [brightinventions.pl](https://brightinventions.pl/blog/)

作者 Mateusz Klimczak,《推动事物前进》@光明的发明

[推特](https://twitter.com/Klimczak_M)，[邮件](//mateusz.klimczak@brightinventions.pl)