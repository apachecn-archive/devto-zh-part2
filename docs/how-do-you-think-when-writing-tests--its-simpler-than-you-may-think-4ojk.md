# “写测试的时候是怎么想的？”–这比你想象的要简单

> 原文：<https://dev.to/essentialdeveloper/how-do-you-think-when-writing-tests--its-simpler-than-you-may-think-4ojk>

[https://www.youtube.com/embed/9CyIrbCNWPs](https://www.youtube.com/embed/9CyIrbCNWPs) [在 YouTube 上观看](https://www.youtube.com/watch?v=9CyIrbCNWPs&list=PLyjgjmI1UzlT2jbk9WeqNrZhZyKpPPaWq)

在这一集里，我们回答一个由我们社区的一个成员提出的重要问题。

## 问题

“我试图理解你在写测试时是如何思考的。你是想涵盖所有想到的事情，还是你有一个优先级列表？或者你只是试图达到最大可能的覆盖面？”

## 答案

我们遵循基本原则。我们的主要流程是先写测试，看到测试失败(红色)，然后写最少的代码让测试通过(绿色)，如果需要的话再重构代码/测试*。*通过遵循这一过程，我们最终获得了相当高的覆盖率(不总是 100%，但非常接近)。覆盖面不是直接目标。覆盖面更像是流程之后[的正面副作用。](https://www.essentialdeveloper.com/articles/whats-more-essential-for-developers-to-focus-on-result-or-process)

也就是说，要求[纪律和技能](https://www.essentialdeveloper.com/articles/the-importance-of-discipline-for-ios-programmers)总是首先编写测试。你可能会卡在开始的的[里。我们这样做了很多年，有时候还是会卡！然而，我们的目标是永远不要被卡住太久。太浪费了。说到底，发货比先测试更重要。](https://www.essentialdeveloper.com/articles/junior-ios-developers-can-be-more-productive-than-they-think)

当你陷入困境时，提出一些想法。去自由，没有测试(或其他任何东西)在你的方式。玩代码(Xcode Playgrounds，Swift REPL，调试...)直到你摆脱困境，找到自己满意的解决方案。然后想，“现在我知道我想要构建什么了，我怎么可能先编写测试呢？”

当你有了答案，你可以丢弃加标的解决方案(将加标的解决方案提交到一个单独的加标分支中)，并通过首先编写测试来重新开始。下一次你必须解决类似的问题时，你知道如何在不扣球的情况下先测试。

我们[练习、学习，每天都有一点进步](https://www.essentialdeveloper.com/articles/number-1-reason-why-you-dont-improve-as-a-software-developer)。每天的练习是复合的！

现在就订阅我们的 Youtube 频道，每周都能看到**的免费新剧集**。

* * *

我们一直在帮助敬业的开发人员从低薪职位晋升到高层职位——有时只需几周时间！为了做到这一点，我们不断开展并分享关于如何提高你的技能的免费市场研究，同时牢记**同理心、诚信和经济学**。如果你想在事业上更上一层楼，[现在就免费获取我们的最新研究](https://www.essentialdeveloper.com/courses/career-and-market-strategy-for-professional-ios-developers)。