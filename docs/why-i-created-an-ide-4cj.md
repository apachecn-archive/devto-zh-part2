# 我为什么创建 IDE

> 原文：<https://dev.to/worksofbarry/why-i-created-an-ide-4cj>

你好。我想写一篇这样的博文已经有一段时间了，我认为这可能是我在 dev.to 上的第一篇博文的一个很酷的想法。

在过去的七个月里，我一直在创建一个 IDE。在最初的几个月里，它是闭源的，现在它是开源的，并将继续保持下去。

[![](../Images/f178a0a23e50cb79759cd8b927fe9361.png)T2】](https://camo.githubusercontent.com/fb54c5248ef1d455b836b221180b5258dccf3e54/68747470733a2f2f692e696d6775722e636f6d2f5431395a5556702e706e67)

## 先决条件

首先我来回答一下我为什么要创建它。我是一名开源开发者。我没有很多钱来购买大型企业工具。我非常感谢 Visual Studio 社区、Eclipse、Jenkins 等。我在一个生态系统中，当我差不多 4 年前开始的时候，没有很多开源技术。这个生态系统围绕着 IBM i 操作系统——它有很多名字，最著名的是 AS/400。我们最近才获得了一个主流软件包管理器(yum ),对于那些希望在现有堆栈中使用开源工具的公司来说，这将是一个好消息。

值得注意的是，为该操作系统所做的大部分开发都是在平台本身上完成的。操作系统有自己的 C、C++、COBOL 和 [RPG](https://en.wikipedia.org/wiki/IBM_RPG) (我们称这些 [ILE 语言](https://www.ibm.com/support/knowledgecenter/en/ssw_ibm_i_73/ilec/introc1.htm))编译器。虽然您可以获得在其他操作系统上运行的 C、C++和 COBOL 编译器，但是您不能获得 RPG 编译器。RPG 是非常特定于平台的，并且具有与操作系统相关的语言特性。有很多行业都在运行这个操作系统，并使用 RPG 作为他们的主要开发语言。

“本地开发”并不是一件真正的事情，因为在 IBM i 之外不存在针对 RPG 的编译器。对于分布式开发来说，现在最好的事情是在您的`/home/`目录中进行开发，该目录中有您的“本地”git repo。这确实意味着当用 ILE 语言编写时，所有的开发都是远程完成的。

## IBM I 上的开发

用 ILE 语言写作有两种主流的发展选择。对于 i 使用 [Rational Developer(还有](https://www.ibm.com/uk-en/marketplace/rational-developer-for-i) [Power](https://www.ibm.com/developerworks/downloads/r/rdp/index.html) 和 [Z](https://www.ibm.com/developerworks/downloads/r/rdz/index.html) 版本，都是基于 Eclipse)或者使用 [SEU](https://www.ibm.com/support/knowledgecenter/en/ssw_ibm_i_61/rzasc/sc09250796.htm) 。SEU 严重过时，不让你在 RPG 中使用最新的语言功能。

它实际上只是一个文本编辑器，带有 RPG 和 CL 开发的提示。我真的不想花太多时间去提及 SEU，所以如果你想看更多的[请看这个视频](https://www.youtube.com/watch?v=mhcgg_wHgD8)。

如果你是一家多年来一直使用 SEU 的开发公司，那么转移到 RDi (Rational Developer for i)是一个避风港。您终于体验到了 IDE 所能提供的一切。内容辅助、大纲视图、远程调试——它甚至在编辑时处理 EBCDIC 转换。这真的是一个升级。

[![](../Images/c3f11e5b8439c18db6b9662d237d609b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SCP3RO-x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ytimg.com/vi/VIgpgGEc5PQ/maxresdefault.jpg)

我在使用 RDi 时遇到了一些性能问题，这导致我有时效率低下。我想这无助于我在不同大陆的系统上工作。这让我觉得没有收获。

## 接下来是什么

我想要一些我擅长的东西。不会让我慢下来的东西。几个星期以来，我一直在为 Visual Studio 和 VSCode 创建示例扩展，看看我是否可以为它们添加功能，以便与 ILE 语言一起工作。这包括:

*   所有远程开发
*   为语言添加突出显示(RPG、CL 和 COBOL)
*   最终添加内容辅助
*   提交远程编译器并检索错误列表

由于 IBM i 的性质，我在这两个方面都不走运——它有一个不同的文件系统。最重要的是，所有的东西都是远程的，并且在这两种环境下都不能很好地工作——这意味着要为文件系统创建新的文件浏览器(这在 VSCode 中确实不太受欢迎)

我总是遵循这样的想法，用你擅长的东西来工作。我对 C#还行，于是开始用 C#写环境，开源代码，编译程序。这是大约 7 个月前它最初的样子:

[![](../Images/ab94cf78c62a75922183517aea6d4347.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CQS2IFTI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/N1gE4IL.png)

最重要的是，我想要一个用 ILE 语言开发的开源选项。虽然我所创造的并不完美，但它足以学习 RPG。它实现了我想要的一切——良好的性能，不占内存和快速编译提交。

它最初被命名为 Idle，但后来意识到这也是 Python IDE 的名字。我把它重新命名为 ILEditor，它就被卡住了。

我有时有机会在会议上谈论它。最重要的是当人们提交他们的想法时，因为这也有助于 IDE 的方向。它使用 GPLv3 许可证，并使用其他开源库来创建用户界面。到目前为止我很开心！

[![](../Images/f178a0a23e50cb79759cd8b927fe9361.png)T2】](https://camo.githubusercontent.com/fb54c5248ef1d455b836b221180b5258dccf3e54/68747470733a2f2f692e696d6775722e636f6d2f5431395a5556702e706e67)

差不多就是这样。希望你喜欢阅读我做的东西。这里有几个后续链接:

*   [GitHub 回购](https://github.com/WorksOfBarry/ILEditor)
*   [主页](https://worksofbarry.com/ileditor/)

谢谢！