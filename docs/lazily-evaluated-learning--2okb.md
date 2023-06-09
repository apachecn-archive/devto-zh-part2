# 懒惰评估的学习

> 原文：<https://dev.to/hoelzro/lazily-evaluated-learning--2okb>

*最初发布于[hoelz . ro](https://hoelz.ro/blog/lazily-evaluated-learning)T3】*

作为程序员，你经常听到的一句话是，要想成功，你需要不断学习。有时那是学习一项新技术，或者学习一种你更深入使用的技术。我感觉这越来越难了——总是有更多的框架、更多的编程语言、更多的工具需要学习。跟上是困难的，尤其是当你有其他人或事需要你的时间的时候。亲爱的读者，我有一个已经用了一段时间的小技巧，我想和你分享我的秘密。我称之为*懒评学习*。

如果你已经知道什么是懒惰评估，你可能会明白我的意思，你可以跳过这个解释。简而言之，惰性求值是一些编程语言(如 Haskell)使用的一种技术，其中一段代码直到需要它的结果时才真正运行。大概是这样的:

```
let result = reallyExpensiveComputation();
if(userShouldSeeResult) {
  displayResult(result);
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，`reallyExpensiveComputation`只有在`userShouldSeeResult`为真时才运行。那么，如何将这一点应用到学习中呢？

懒惰评估学习背后的想法是推迟学习，直到你需要知道一些东西，但保留足够的关于你需要知道什么的信息。例如，有很多关于编写 Haskell 的信息，比如 Alexis King 的[这篇概述。我现在没有主动学习 haskell，但是如果有一天我开始学习，不管是为了好玩还是为了一个项目，我在我的](https://lexi-lambda.github.io/blog/2018/02/10/an-opinionated-guide-to-haskell-in-2018/)[个人维基](https://hoelz.ro/blog/tiddlers-green)中有一个标有“改进 Haskell”的文章列表，我可以依靠它。

你所需要做的就是有一个好的文章来源(我喜欢
[https://lobste.rs](https://lobste.rs) ，但是任何对你有用的东西！)和某种个人
档案，最好带有标签，无论是 TiddlyWiki 还是 Evernote。你可以使用的另一个工具是使用间隔重复的抽认卡程序，像
[Anki](https://apps.ankiweb.net) 一样，这个工具特别有帮助，当你很少使用这个技能
来有效地记住它，但是你仍然半定期地使用(对
我来说，这是 R)。这可以让你很快将一项技能载入大脑。

实际上，我过去已经利用这种技术获利了:当我开始在 Threadless(一家 Python 商店)的当前工作时，我曾涉猎过 Python，但从未认真使用过。当我开始学习的时候，有一个 Python 资源列表可以查看，这真的帮助我很快掌握了技能，并且很快就进入了状态。这个想法帮助我管理了试图跟上的疲劳，希望它也能为你做同样的事情！