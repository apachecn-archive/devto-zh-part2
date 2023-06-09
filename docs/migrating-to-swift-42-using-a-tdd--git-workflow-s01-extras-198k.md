# 使用 TDD + Git 工作流程迁移至 Swift 4.2[S01 附加内容]

> 原文：<https://dev.to/essentialdeveloper/migrating-to-swift-42-using-a-tdd--git-workflow-s01-extras-198k>

[https://www.youtube.com/embed/1bYNmMILkEw](https://www.youtube.com/embed/1bYNmMILkEw) [在 YouTube 上观看](https://www.youtube.com/watch?v=1bYNmMILkEw&list=PLyjgjmI1UzlSUlaQD0RvLwwW-LSlJn-F6)

在这一集里，我们将[测验项目](https://github.com/essentialdevelopercom/quiz-app)及其依赖项迁移到 **Swift 4.2** ，并通过最新的添加更新我们的代码。

就像在 [Swift 4](https://www.essentialdeveloper.com/articles/s01-extras-migrating-the-project-to-swift-4) 和 [Swift 4.1](https://www.essentialdeveloper.com/articles/migrating-to-swift-4-1-using-a-tdd-git-workflow-s01-extras) 迁移集中一样，我们独立地迁移每个模块，同时确保所有测试在每一步都通过，并且我们最终没有构建警告。

**Swift 4.2** 带来的一个令人欣喜的变化是编译器合成`Hashable`协议实现的能力得到了提高。我们利用这一变化，通过遵循我们结合`Git`和自动化测试的标准工作流程，我们删除了我们在[第 13 集为`Question`类型编写的`hashValue`的自定义实现——用 Swift Generics](https://www.essentialdeveloper.com/articles/s01e13-improving-the-game-flow-with-swift-generics) 改进游戏流程。

在`Hashable`重构期间，我们对一些测试中断感到惊讶。我们通过遵循高度自律的解决问题的方法来抵制删除测试的冲动。

重要的是要注意到，如果没有我们信任的测试套件，就很难满怀信心地进行这样的改变。

最后，我们利用了 **Swift 4.2** 提供的新`Sequence.shuffled`功能。我们用`Sequence.shuffled`来展示如何通过使用概率而不是*嘲弄*来测试随机性。通过不依赖于嘲讽，我们可以自由地改变代码的实现，而不会破坏任何测试。

现在就订阅我们的 Youtube 频道，每周都能看到**的免费新剧集**。

* * *

最初发表于[www.essentialdeveloper.com](https://www.essentialdeveloper.com/articles/number-1-reason-why-you-dont-improve-as-a-software-developer)。

我们一直在帮助敬业的开发人员从低薪职位晋升到高层职位——有时只需几周时间！为了做到这一点，我们不断开展并分享关于如何提高你的技能的免费市场研究，同时牢记**同理心、诚信和经济学**。如果你想在事业上更上一层楼，[现在就免费获取我们的最新研究](https://www.essentialdeveloper.com/courses/career-and-market-strategy-for-professional-ios-developers)。

## 我们来连线

如果你喜欢这篇文章，请访问我们在[https://essentialdeveloper.com](https://essentialdeveloper.com)的网站，获得更多像这样的深度定制内容。

关注我们:[YouTube](https://youtube.com/essentialdeveloper)[Twitter](https://twitter.com/essentialdevcom)[脸书](https://facebook.com/essentialdeveloper)[GitHub](https://github.com/essentialdevelopercom)