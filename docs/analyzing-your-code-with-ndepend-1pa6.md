# 用 NDepend 分析你的代码

> 原文：<https://dev.to/_patrickgod/analyzing-your-code-with-ndepend-1pa6>

在成为一名伟大的程序员的过程中，你可能会不时地问自己，你写的代码是否真的是好代码。

它健壮且可维护吗？它是否遵循最佳实践？从长远来看，有哪些部分可能会让你付出很多汗水和泪水？

幸运的是，你不必自己回答所有这些问题。有工具可以帮你做到这一点。其中一个工具是 [NDepend](https://www.ndepend.com/) 。

我很幸运地瞥见了 NDepend 的最新专业版——这要感谢它的创作者 Patrick Smacchia(这不是付费代言！)——并启动它来检查一个相当小的 Visual Studio 解决方案——一个在线课程的源代码——和一个更大的解决方案——一个我目前正在与 Unity 合作的独立游戏。

开始的时候，你已经注意到了，NDepend 的分析特性非常深入。我几乎没有触及表面。

仪表板已经为您的整个代码库提供了一个很好的概述。

[![](img/c8145bce9123dd9d52b368d19474bfeb.png)T2】](http://programmergoals.com/wp-content/uploads/2018/03/01_ndepend_dashboard_ssl.jpg)

150 行代码(就像我说的，一个相当小的解决方案…)，完美的 A 评级，但那是什么？严重违反规则？那是什么意思？

一个点击就已经告诉我一切。这是 NDepend 非常重要的一部分:文档。每件事都有详细的解释，也有更深入的链接。

[![](img/44574d2f3de20a205858d592869f6eb1.png)T2】](http://programmergoals.com/wp-content/uploads/2018/03/02_ndepend_critical01.jpg)

[![](img/1f9a56a86f90fb7ef308624d6ac98b19.png)T2】](http://programmergoals.com/wp-content/uploads/2018/03/03_ndepend_critical02.jpg)

我可以看到这个问题的债务，每年的利息，打破点，也详细描述了这种违反和如何修复它。这是一个很好的机会来学习这些东西，并提高我的手艺。

一旦我解决了这个问题，我就可以让 NDepend 再次分析这个项目，它会告诉我与上次分析的不同之处。代码质量真的提高了吗？

现在让我们来看看更大的项目。我可以在 Visual Studio 中打开解决方案并使用 NDepend 扩展进行分析，或者打开 NDepend 本身并让它在那里分析解决方案。无论哪种方式，我看到这个项目有更多的代码行，当然也有更多的问题。

[![](img/73415227a520e539445e2f9492bd067a.png)T2】](http://programmergoals.com/wp-content/uploads/2018/03/04_ndpend_dashboard_forscience2.jpg)

有趣的是，许多问题实际上来自我在 Unity 资产商店中获得的资产。您可以在代码度量视图中很容易地看到这一点。看看这只野兽。

[![](img/fd9cc00b7a920e680dc6c32e659c8f10.png)T2】](http://programmergoals.com/wp-content/uploads/2018/03/05_ndepend_code_metrics_view.jpg)

百米..也许他们也应该分析他们的代码。

除了这个视图，您还可以查看依赖矩阵和各种各样的图表。正如已经提到的，我真的只是在这里抓表面。

另外，你可以自己定义每个问题的规则。这些规则实际上是在 C#代码中定义的。太棒了！

为了充分利用这个工具，你必须花大量的时间和学习。但是如果你这样做，它可以带来巨大的好处。我不得不纠结的一件事是工具的可用性。有时候，要到达我想要的地方并不容易，我鼠标上的后退按钮没有像预期的那样工作，我希望有一个 NDepend 的集成，当我处理我的实际代码时，它可以直接告诉我问题所在——例如，通过使用左边的一些图标。或者也许我漏掉了什么？

相反，当然，还有这个无穷无尽的特性列表。有了这个列表，你可能会发现没有 NDepend 你可能永远也不会发现的问题。每个问题都有详细的解释，您可以通过提供的大量文档来学习解决所有问题。

就连斯科特·汉瑟曼也喜欢它:

> “NDepend 让我对自己的应用有了前所未有的深入了解。一旦我意识到我正在查看的信息的深度和广度，我就像一个在糖果店的孩子。”

综上所述，我认为 NDepend 是一款面向单个用户或小型团队的工具，这些用户或团队确实希望深入研究他们的代码库并从中进行分析。如果是关于债务和深度品质——而且你有时间——这是给你的。

这对于小项目已经有意义了，但是对于真正的大项目可能更有用，因为你已经忘记了你的代码库。这种情况更有可能发生在大型公司中，那时这些公司可以很好地使用 NDepend。

要真正充分利用这个工具，你必须更深入、更长久地使用它。这不仅仅是安装，看图表，忘记一切。但是如果你这样做了，你就有机会显著提高你的代码质量——并且在这个过程中学到很多。

如果您在实际编码时需要一个工具来获得快速提示和技巧，其他产品可能更合适。你可能想看一看 ReSharper 或者简单地使用 Visual Studio 的内置特性。他们经常被低估，尽管他们已经很好了！

<small>*头像[由 Freepik](https://www.freepik.com/free-vector/seo-elements-background_1819019.htm)* 设计</small>

*用 NDepend 分析你的代码的帖子[最先出现在](http://programmergoals.com/analyzing-code-ndepend/)[程序员目标](http://programmergoals.com)上。*

* * *

### 但是等等，还有！

*   让我们在 [Twitter](https://twitter.com/_PatrickGod) 、[脸书](https://www.facebook.com/ProgrammerGoals)、 [YouTube](https://www.youtube.com/channel/UCq8LldVrjqe61KQttZlLW8g) 、 [LinkedIn](https://www.linkedin.com/in/patrickgod) 、 [Instagram](https://www.instagram.com/_patrickgod) 或这里的 [dev.to](https://dev.to/_patrickgod) 上连线。
*   免费获得 [5 个软件开发人员的职业秘诀](http://programmergoals.com/5-software-developers-career-hacks/)。
*   在[programmergoals.com](http://programmergoals.com)上享受更多对您的开发者生活和职业有价值的文章。