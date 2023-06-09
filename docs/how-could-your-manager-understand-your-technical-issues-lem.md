# 您的经理如何理解您的技术问题？

> 原文：<https://dev.to/rlxdprogrammer/how-could-your-manager-understand-your-technical-issues-lem>

与经理谈论技术问题，这确实是一个挑战，但却是经常需要的。

何时需要:

*   当你有一个非常好的想法，并且需要你的经理同意才能实现时。
*   当你需要更多的时间来完成你的任务时。
*   当你需要帮助来完成任务时。
*   当技术上无法按照当前需求完成任务时。
*   当你需要向你的经理展示你做了很棒的事情时。

为什么这么有挑战性？然而，大多数管理人员永远不会忘记提及他们在开始时是开发者，实际上这发生在上个世纪或他们的前世。说真的，他们当然是开发人员，但这发生在几年前，所以他们的知识并不是最新的，他们已经忘记了很多。另一方面，他们大多不是最好的开发人员，这就是为什么他们选择了经理载波路径。

所以你的第一个选择是用开发者的语言讲述你的问题的每一个细节，比如:

这个类中存在潜在的内存泄漏，因为在释放内存之前，desctructor 中抛出了一个异常。

最有可能的是，你的经理不会理解为什么这是一个问题，所以他/她只会问“这是系统中的一个主要还是次要的错误？”，“客户知道这件事吗？”或者类似的东西。此外，他/她可能会感到沮丧。我想这不是你想要的。当然，如果你和其他开发人员或软件架构师交谈，这是正确的方式。

第二种选择是将问题提交给你对编程一无所知的奶奶，比如:

“我的代码正在做它应该做的事情，但有一点可能会出错。”答案很可能是:“代码应该总是像它应该的那样工作，质量对我们来说非常重要，为什么你要实现质量差的代码？”。而且他/她会觉得你完全是个笨蛋。好吧，仍然不是预期的反应。

那么做什么知道吗？

1.  你需要带一些技术细节，但不要太多。

2.  你需要描述技术背景，即使对一个开发人员来说是微不足道的。

3.  你需要让你的经理觉得你确信他/她理解每一个技术细节，比如:

4.  “我相信你明白，如果你不释放内存，这将导致内存泄漏”

5.  总是以简短的介绍开始:你的代码应该根据需求做什么，它在做什么，缺少什么功能。

6.  始终提及估计的时间以及您解决问题所需的时间。

7.  总是从用户的角度提到你的改变的好处，比如:软件将运行得更快，不会使用太多的内存等等。

8.  说话缓慢而平静，看起来自信。

一个例子:

“我需要实现购物清单功能。我估计这项任务需要 8 个小时，到目前为止花了 7 个半小时。现在我可以向列表中添加元素，以后再修改它们。我已经实现了移除元素的功能，但是当我检查我的代码时，我意识到了一个问题。问题如下:如果元素没有有效的 id，ListElement 类的 desctuctor 中将引发异常。问题是，内存的释放是在这个异常之后完成的，你知道，现在存在内存没有被释放的风险，这会导致内存泄漏。为了分析和解决这个问题，我还需要一个小时。修复后，程序将正常工作，内存使用将达到最佳状态。

当然不需要一个小时，只需要 15 分钟，但是现在你可以有一个很长的咖啡馆休息时间。你的经理会知道你在做什么，会觉得你在做一些重要的事情，当然你可以有一些额外的时间来解决它。此外，他/她会觉得你是一个优秀的开发人员，能够发现错误，以正确的方式交流，并且对技术工作充满信心。在技术层面上，他/她会理解在记忆处理上出了问题，对我们来说这就足够了。

也许它不会对每个经理都起作用，但对我来说，它在我的载体中帮助很大。

你有哪些经历？你还有其他的建议吗？

最诚挚的问候，

轻松编程师
[http://howtosurviveasaprogrammer.blogspot.com/](http://howtosurviveasaprogrammer.blogspot.com/)