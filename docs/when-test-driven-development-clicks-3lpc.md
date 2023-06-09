# 当测试驱动开发点击时

> 原文：<https://dev.to/ben/when-test-driven-development-clicks-3lpc>

测试驱动开发(TDD)是编写可靠软件程序的一种非常好的方法，但是它不是一种非常直观的方法。我最近问了一个问题[你的 TDD 啊哈时刻是什么？](https://dev.to/ben/what-was-your-tdd-aha-moment-h5f)我想分享最热门的评论，它提供了对该主题的深刻见解。

[![leightondarkins profile image](img/e8e066e3840f220887f39a02077e5935.png) ](/leightondarkins) [ Leighton Darkins ](/leightondarkins) • [<time datetime="2018-10-29T14:28:25Z" class="date-short-year"> Oct 29 '18 </time>](https://dev.to/leightondarkins/comment/6e1f) 

我经历过的最大的“啊哈”之一是在只有很少单元测试的遗留代码库上工作。

我需要改变一些方法的行为，但是我不太确定它们当前的行为是从什么开始的。

许多人会使用他们的调试器，然后花时间一丝不苟地一步一步调试代码。但对我来说那感觉很慢...

因此，我围绕他们进行了一系列测试(在进行任何代码更改之前),以验证和记录他们真正在做什么。一旦这样做了，整个变化就像调整几个断言一样简单，导致那些测试失败，并重新实现方法以满足它们的新期望。

将 TDD 视为自信地编写全新特性的方式是一回事，将它视为自信地导航和梳理遗留代码的方式则完全是另一回事。就像在一个超级黑暗的隧道里，找到一个手电筒。一切都变得简单了。

甚至不要让我开始用一套好的测试在一个旧的代码库上工作。纯粹。极乐世界。

在我软件生涯的早期，我与冒名顶替综合症作斗争，因为 TDD 并不适合我。*没关系* TDD。大多数开发人员不使用这种方法。

[![kaydacode profile image](img/8ea90fd5d4f75191ce1c92a6982bff13.png) ](/kaydacode) [ Kim Arnett  ](/kaydacode) • [<time datetime="2018-10-29T17:06:31Z" class="date-short-year"> Oct 29 '18 </time>](https://dev.to/kaydacode/comment/6e36) 

还在等。😂

TDD 是软件开发的更好的方法之一，但是如果它不适合你，不要紧张。

如果你对开始使用 TDD 感兴趣，这篇文章可能会有帮助:

[![kylegalbraith](img/88a6312db831343b6c7c3b1b5bea970e.png)](/kylegalbraith) [## 今天如何开始测试驱动开发

### 凯尔·加尔布雷斯 1818 年 4 月 8 日 14 分钟阅读

#testing #tdd #devtips #coding](/kylegalbraith/how-to-get-started-with-test-driven-development-today-401j)