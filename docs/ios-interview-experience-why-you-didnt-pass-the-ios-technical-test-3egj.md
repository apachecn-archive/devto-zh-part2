# iOS 面试经历:你为什么没有通过 iOS 技术测试

> 原文：<https://dev.to/essentialdeveloper/ios-interview-experience-why-you-didnt-pass-the-ios-technical-test-3egj>

[![thumbnail.png](img/945bac3ef245750e64ad4018bda1fcca.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--C-ysc0g2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static1.squarespace.com/static/5891c5b8d1758ec68ef5dbc2/t/5bcdb144e4966bca9c928048/1540206947336/thumbnail.png%3Fformat%3D1000w)

在 Essential Developer，我们帮助许多开发人员经历 iOS 面试体验。最近，我们从我们社区的一名成员那里收到了这条消息:

> *“你好，卡伊奥。我最近参加了一个科技测试的工作面试，我花了两天时间完成了***，结果被拒绝了。**
> 
>  **事情是这样的:我从一家* ***成功的搜索/索引公司*** *那里得到消息，愿意和我进行一次 iOS 面试。我与该公司的一名 iOS 开发人员进行了 30 分钟的交谈。* ***不知道他们找的是什么级别的。我*** ***对他说，我在我看来是中等偏上*** *。他说他们会给我发一个技术测试，看看我如何组织代码、对象、使用什么架构(他们提到了 VIPER)、设计模式、测试方法、TDD、XP 等等* *。*
> 
> 我尝试按照 TDD 进行测试，但并不完全。 *他们回答说:“感谢您抽出时间和对我们公司的兴趣。不幸的是，在这种情况下，我们决定不再进一步处理你的申请。我真的很高兴与你交谈，并祝你目前的求职成功。”*
> 
> *就这样。* ***不知道我的项目*** *怎么了。可以把测试描述和我的小项目发给你听听你对* ***有多好或者有多差*** *的看法吗？”**

 *大多数公司不会给出他们决策的全部细节，这没什么。这是他们的选择。作为一名经历过 iOS 面试的求职者，通常会[指责](https://www.essentialdeveloper.com/articles/ios-interview-experience-7-essential-nontechnical-skills) [我们的技术技能第一](https://www.essentialdeveloper.com/articles/ios-interview-experience-7-essential-nontechnical-skills)。注意*“有多糟糕？”*提问。这种对事件的负面看法会阻碍开发人员进一步追求他们梦想中的工作。可能和候选人的技巧无关。因此，重要的是要记住，有许多因素是我们无法控制的。然而，对于这篇博文，让我们关注并假设拒绝的动机仅仅是技术性的。

## 科技测试

*给定一个文本文件:*

1.  编写一个应用程序，输出文本文件中出现的单个单词(忽略标点和大小写)，以及该单词在文本文件中出现的次数。

2.  *输出每个单词出现的次数是否为质数。*

测试旨在展示你在解决问题、TDD、OO、架构、代码质量和性能方面的技能。奖金，如果你能提出一个以上的解决方案，并解释利弊。

## 研究公司在寻找什么

作为一名求职者，在经历 iOS 面试经历和技术测试之前，对这家公司做一些研究是非常必要的。因此，在查看候选测试项目之前，我对该公司做了一些研究。通过查看该公司的网站，我很快注意到他们对自己的*快速*、*高效*、*可扩展*、*可靠*、*伟大、一流、成功、*和*最先进的*平台感到多么自豪。看起来他们一天执行几次 web 清理，并通过他们的 API 服务于几个客户。简而言之，可伸缩性、性能和数据的正确性对公司至关重要。此外，从对话和技术测试描述来看，该公司似乎非常热衷于 TDD、OO、干净代码和架构。

## 满足 iOS 测试要求

从表面上看，这个项目总体上看起来不错。这个项目组织得很好，班级很小，代码可读。候选人确实证明了他可以从头开始制作应用程序，我很乐意给他提供初级/中级职位。候选人甚至做了额外的努力来构建一个非常好的用户界面，并添加了一个很棒的功能来从 URL 导入文件。然而，我认为候选人可以在某些方面更加专注于**满足这次 iOS 面试体验的测试要求**。

代码是有组织的和可读的，但是它似乎不能相应地处理错误。对于技术测试来说，假设输入总是有效可能没问题，但是额外处理错误案例可以帮助你展示出色的编程技能。

## 注重细节

有一些常见的反模式需要避免，比如原始的困扰、泄漏的抽象、拒绝的遗赠、未使用的代码和不必要/多余的注释。所有这些对初级开发人员来说都是可以接受的，但对面试官来说可能是危险信号。

代码不受版本控制。我们建议你总是使用 ***git*** 进行技术测试。然后发给面试官。许多团队会查看历史来了解你是如何得到最终解决方案的。这是对你有利的，所以不要瞒着他们！

测试覆盖不够全面。候选人意识到他不能完全遵循 TDD，但他表现出了极大的努力。有 TDD 经验的候选人很难找。公司本可以选择投资一个有能力的候选人，但这取决于他们。(提示:如果[你想增加你作为开发者的价值，TDD 技能是有价值的，也是稀缺的](https://www.essentialdeveloper.com/professional-ios-engineering-season-1/)。公司一直在寻找有经验的候选人！)

候选人遵循了 [MVC 设计模式](https://www.essentialdeveloper.com/articles/clean-ios-architecture-pt-5-mvc-mvvm-and-mvp-ui-design-patterns)。MVC 很好，但是公司非常热衷于[蝰蛇](https://www.essentialdeveloper.com/articles/clean-ios-architecture-pt-6-viper-design-pattern-or-architecture)。也许花点时间让它看起来更像毒蛇可以为候选人赢得一些额外的分数。

## 总是实施奖金挑战

当试图通过一项技术测试时，在 iOS 面试过程中调整你的技能以给面试官留下深刻印象可能是至关重要的。花时间实施不同的解决方案是一个很好的方法。公司甚至要求把它作为奖金，但是候选人只提供了一个解决方案。所提供的解决方案不能很好地扩展，并且不能处理大量数据。这没什么不好，它仍然是*一个有效的解决方案。*然而，候选人本可以花时间提供不同的方法并解释其利弊。尤其是因为在大规模运行时，一个糟糕的实现的成本可以迅速跃升至 7 位数。在这里，成为[中级开发人员不是借口](https://www.essentialdeveloper.com/articles/number-1-reason-why-you-dont-improve-as-a-software-developer)。一些研究和谷歌搜索可以帮助候选人提供不同的解决方案。

## 解释你的解决方案

审查代码是非常主观的。配对会议更有利于交流意图，但并不是每个公司都愿意在 iOS 面试体验的第一阶段投入时间。花时间写下一些笔记，画一些图表，甚至录一段解释你的决定的视频都是在这个阶段与面试官互动的好方法。使用 ***git*** 也可以很好地展示从开始到结束的进程。小而意 ***git commits*** 可以大有作为。

## 在合理的时间内交付测试

这位候选人花了两天时间完成测试，这可能是一个危险信号。要求并不复杂。然而，从我收集的信息来看，并没有约定发送测试的截止日期。此外，候选人明确表示，他并没有全职从事这项工作。真正的危险信号是很多时间花在了 UI 和额外的**未请求的**功能上，而不是奖励挑战上。对于测试评审者来说，它可能被理解为“这个候选人可能很难满足需求。”在精益和敏捷团队中，重要的是消除浪费，例如过度工程化/复杂化实现(YAGNI！).当然，这可能是对候选人的错误假设，但我们如何在 iOS 面试和技术测试中展示自己可以帮助消除这些假设。

总是协商一个截止日期。如果你全职工作，没有太多时间进行技术测试，让面试官知道并协商一个对你有意义的合理的交付日期。公司不应该指望你停下你的生活来完成测试，但表现出一些精力和努力可以帮助你表现出对这份工作的兴趣。

## iOS 面试体验结论

重要的是要记住，从这一点上来说，我们只是*想知道/假设*会发生什么*对/错*。

我相信候选人是一个伟大的开发人员，他应该有动力从经验中学习并再次尝试。 [*熟能生巧！*T3】](https://www.essentialdeveloper.com/articles/the-importance-of-discipline-for-ios-programmers)

我们祝愿所有候选人在他们未来的 iOS 面试经历中好运，我们希望这篇文章给你足够的提示来帮助你成功。

* * *

我们一直在帮助敬业的开发人员从低薪职位晋升到高层职位——有时只需几周时间！为了做到这一点，我们不断开展并分享关于如何提高你的技能的免费市场研究，同时牢记**同理心、诚信和经济学**。如果你想在事业上更上一层楼，[现在就免费获取我们的最新研究](https://www.essentialdeveloper.com/courses/career-and-market-strategy-for-professional-ios-developers)。

* * *

最初发表于[www.essentialdeveloper.com](https://essentialdeveloper.com/articles/ios-interview-experience-why-you-didnt-pass-the-ios-technical-test)。

## 我们来连线

如果你喜欢这篇文章，请访问我们在[https://essentialdeveloper.com](https://essentialdeveloper.com)的网站，获得更多像这样的深度定制内容。

关注我们:[YouTube](https://youtube.com/essentialdeveloper)[Twitter](https://twitter.com/essentialdevcom)[脸书](https://facebook.com/essentialdeveloper)[GitHub](https://github.com/essentialdevelopercom)*