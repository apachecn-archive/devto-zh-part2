# 代码文档——编码之前、之后和期间

> 原文：<https://dev.to/cseeman/code-documentation--before-after-and-during-coding-2dj3>

有时候，你只是需要为一个更大的项目或新功能开始一些设计工作，你想把你的想法写下来，也许开始绘制代码流或一些潜在的类，但你需要什么应用程序呢？

[![](../Images/faa9b96f459d53870ae832a8919833d9.png)T2】](https://i.giphy.com/media/cMVgEhDeKzPwI/giphy.gif)

为了我的个人或专业工作，很多时候我会使用谷歌文档。这很容易，自动备份您的工作，并使它非常简单地分享文件或转换成另一种格式。打开一个谷歌文档，开始输入一些伪代码，自动大写或者代码不像你想的那样显示会令人沮丧。我发现[代码块扩展](https://chrome.google.com/webstore/detail/code-blocks/ebieibfdjgmmimpldgengceekpfefmfd?hl=en-US)非常棒。将代码放在文本旁边是一件轻而易举的事。

> 有用的提示——如果你正在使用 Google Docs，你可以改变你的偏好，不要讨厌地大写句子的第一个字母(这对于喜欢以非大写字母开头的 Ruby 类来说是相当讨厌的)。

[![](../Images/e5651a922992f70c5001cbbe52a8baba.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vSOz9Loa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://christine-seeman.com/wp-content/uploads/2018/09/Screen-Shot-2018-09-14-at-11.00.37-AM.png)

有时你只是想做一个突发事件或分享你在一个特别困难的调试过程或应用程序的晦涩部分所做的研究。对于这类信息，我喜欢使用 Confluence，一个 GitHub readme，甚至是 GitHub pull 请求本身。只是取决于它是否是我目前正在修复的东西(它将出现在 GitHub PR 中，如果是 UI 类型的修复，最好有截图)，对门票或即将到来的设计的研究(Confluence，所以很容易分享，可供其他开发人员搜索)，或者它是否是代码本身需要的东西，如元数据或安装信息，自述文件很好。作为一名开发人员，我正在努力改进我的 git 提交和 PR，以便将来进行开发和维护。我非常鼓励任何在 Git 工作的人去看看泰京·苏莱曼令人敬畏的“时间分支”演讲。此外，他关于[创建有用的 git 历史](https://tekin.co.uk/2018/07/resources-for-creating-useful-revision-histories)的博客文章有一些很棒的 git 技巧和诀窍，真的会让你思考你的 Git 今天的评论如何影响明天的代码。

对于 UML 和流程图， [LucidChart](https://www.lucidchart.com/) ，是我目前选择的应用程序。这是一个在线图表工具，在你想展示什么方面有很大的灵活性。绘制流程图并不难，只要利用典型的形状和线条就行了。对我来说，绘制调用 self.method 的代码是最难的，但是 LucidChart 有一些[非常好的教程](https://lucidchart.zendesk.com/hc/en-us/articles/115004174923-Work-with-Lines)可以带你完成这个过程，并帮助你使用所有的键盘快捷键，让你的工作生活更加快乐。

[![](../Images/ddf4b8fcd727a905634a899c005e7101.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CUjotP6C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://christine-seeman.com/wp-content/uploads/2018/11/Screen-Shot-2018-11-01-at-10.33.53-AM.png)

对于代码本身的文档，我喜欢编写自我文档化的代码。多年来，在编写 Java 的过程中，如果你的代码需要在上面加上注释，以便其他开发人员能够理解它在做什么，那么它可能太复杂了。不鼓励写注释，重构方法和模式成为更好的描述符才是标准。现在有了 Ruby 和我开发的软件，代码库中有了更多的注释。代码注释似乎没有任何污点。共享关于如何使用命令对象的信息，或者 API 的用法，或者在类的公共契约上提供上下文，这些似乎都是内联代码注释的有效用例。我已经习惯了在 Ruby 中有效地使用注释，并且没有带来任何 Java 的烙印，我可能已经带着关于注释的烙印。我仍然倾向于用自描述的方法名将代码重构成更小的块，但是我也明白代码注释什么时候是有帮助的。我所有的方法名都更加冗长，我仍在努力记住所有的事情。

[![](../Images/29713860174e7a39e9bbcb52a776809f.png)T2】](https://i.giphy.com/media/9xaryfkdWqqiK6QHKf/giphy.gif)

作为开发人员，我们必须记录许多不同的东西，因此找出最适合您的工具和应用程序非常重要。因此，作为开发人员，您可以有效地记录代码，并且希望其他人可以找到并使用它。因为唯一比不记录代码更糟糕的事情，就是不使用代码记录。