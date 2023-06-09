# 开发人员应该从 Microsoft Build 了解什么

> 原文：<https://dev.to/remotesynth/what-developers-should-know-from-microsoft-build-5d0j>

过去，我很容易忽略来自雷蒙德的消息。当然，微软一直很重要，但我从来都不是. NET 或 Windows 开发人员，所以他们所说的很少适用于我。我可能会对他们工具的质量印象深刻，比如 Visual Studio，但我没有写过 C#，所以这并不重要。

现在不是这样了。微软在几乎每个开发人员的职业生涯中都扮演着重要的角色。即使你不直接使用他们的产品或服务，他们的发展方向也会对我们产生巨大的影响。例如，正如他们最近吹捧的那样，他们是 GitHub 上开源的最大贡献者。

> 微软是 Github 开源的最大贡献者。[# MSBuild](https://twitter.com/hashtag/MSBuild?src=hash&ref_src=twsrc%5Etfw)https://t.co/Z3ugzLSRulT2[pic.twitter.com/WXSuF7NQhg](https://t.co/WXSuF7NQhg)
> 
> — Microsoft Developer (@msdev) [May 7, 2018](https://twitter.com/msdev/status/993546257988833280?ref_src=twsrc%5Etfw)

这只是不同的微软的标志之一。在这篇文章中，我将分享我参加了本周在西雅图举行的微软 Build 大会后的一些观察和思考。我不会花太多时间深入到重大的公告中，而是更多地从总体上来看，我认为微软将走向何方，以及这对我们开发人员有何影响。

## 时代变了！

我知道每个人都在谈论“新微软”，但在这次活动中，它在很多方面都非常明显。让我举几个例子:

*   微软经常在各种产品和服务中宣传其对 Linux 的支持(包括在记事本中对 Linux 行尾的支持，这有点不可思议，是主题演讲中最大的掌声)。

*   微软让亚马逊上台展示了他们的 Cortana/Alexa 集成(这有点乏味，但我想你需要从某个地方开始)。是的，同一个亚马逊是他们在云服务方面最大的竞争对手。

    > Alexa 和 Cortana 一起工作？亚马逊和微软一起在 [#MSBuild](https://twitter.com/hashtag/MSBuild?src=hash&ref_src=twsrc%5Etfw) 展示 Cortana 在 Echo 上运行，Alexa 在 Windows 上运行。pic.twitter.com/C3kmHCWgMr
    > 
    > — Brian Rinaldi ([@remotesynth](https://dev.to/remotesynth) ) [May 7, 2018](https://twitter.com/remotesynth/status/993530284304879618?ref_src=twsrc%5Etfw)

*   第二天的主题演讲一开始就提到，他们会在谷歌 I/O 主题演讲开始前及时完成工作，以便观众可以切换。

在我看来，Windows 和。NET 在第一天或第二天的主题演讲中扮演了一个非常小的角色。在大多数情况下，他们对其他公告起辅助作用——在某些情况下是非常重要的辅助作用，但不是重点。

事实上，微软似乎已经接受了许多/大多数(不确定是哪些)开发者选择在 Mac 上开发的现实。例如，我个人最喜欢的主题演讲演示之一是名为 [Visual Studio Live Share](https://www.visualstudio.com/services/live-share/) 的东西，它实现了开发人员之间令人难以置信的实时协作(这可能会改变分布式团队的游戏规则)。不过，该工具的关键之处在于，它并不关心您是在 Mac 还是 PC 上工作(使用 Visual Studio 还是 Visual Studio 代码)。

> Visual Studio Liveshare 共享代码，在 Mac 上的 Visual Studio 代码与 Windows 上的 Visual Studio 之间进行编辑和交互。[# MSBuild](https://twitter.com/hashtag/MSBuild?src=hash&ref_src=twsrc%5Etfw)【pic.twitter.com/hWc8UyvlbZ】T2
> 
> — Brian Rinaldi ([@remotesynth](https://dev.to/remotesynth) ) [May 7, 2018](https://twitter.com/remotesynth/status/993543376971616256?ref_src=twsrc%5Etfw)

就像微软展示的大多数与工具相关的东西一样，这是免费的，这可能会让你问他们是如何以及为什么这样做的。

## 开发者工具和服务的变化

虽然我已经有了这种感觉，但在整个构建过程中，我越来越清楚地认识到，微软是一家可以称之为“云优先”公司。我的意思是一切，包括像 Windows 和 Office 这样的东西，都在朝着支持 Azure 核心业务的未来发展。

我对微软的财务状况了解不多，不知道 Azure 服务在他们目前的财务状况中发挥了多大的作用，但很明显，这是他们看到增长和未来的地方。这意味着软件、硬件和工具是支撑他们不断扩展的云产品的三条腿，它们是将你带入生态系统的钩子。

在我看来，即使他们的声明看起来与 Azure 没有直接关系，也是关于 Azure 的。让我举几个例子。

*   有很多关于 AR、VR 和无人机的讨论。在某些情况下，这似乎集中在微软的硬件上，特别是 HoloLens。

    > 微软远程协助是一个非常聪明的，更重要的是，使用 HoloLens 的 AR 看起来很有用。我可以看到这有广泛的吸引力。 [#MSBuild](https://twitter.com/hashtag/MSBuild?src=hash&ref_src=twsrc%5Etfw)
    > 
    > — Brian Rinaldi ([@remotesynth](https://dev.to/remotesynth) ) [May 7, 2018](https://twitter.com/remotesynth/status/993533735252312064?ref_src=twsrc%5Etfw)

    然而，似乎很明显，微软并不认为 HoloLens 硬件是这里的关键。HoloLens 只是一个工具，它绑定到一系列服务(机器学习和人工智能)中，这些服务是允许硬件做任何有用事情的引擎。今天硬件来自微软——可能是为了证明这个概念——但明天它可能是任何人的硬件，只要 Azure 是让它运行的引擎。

*   有很多关于 office 的讨论(尤其是在第二天的主题演讲中)，但是都是围绕着“未来的 Office”这个概念这是一个每次会议和每次对话都依赖于 Azure 的机器学习和人工智能服务的办公室。甚至看似不相关的办公室公告似乎实际上是关于 Azure 的。

    > Micosoft 宣布为 Excel 定制功能，这些功能存在于云中，用 JavaScript 编写。 [#MSBuild](https://twitter.com/hashtag/MSBuild?src=hash&ref_src=twsrc%5Etfw)
    > 
    > — Brian Rinaldi ([@remotesynth](https://dev.to/remotesynth) ) [May 8, 2018](https://twitter.com/remotesynth/status/993891768432082945?ref_src=twsrc%5Etfw)

    为什么微软会允许你用 JavaScript 写 Excel 函数？不是因为他们喜欢 JavaScript，而是因为 a)他们生活在云中，b)然后他们连接到其他 Azure 服务。渐渐地，像 Excel 这样的东西就变成了构建依赖 Azure 云的复杂的、基于任务的业务服务的容器。

## 开发者的外卖

在我看来，开发人员要明白的重要一点是，微软正朝着可能会从根本上改变我们角色的方向前进(他们要去哪里，其他人也要去哪里)。他们不再单纯对我们使用他们的工具在他们的平台上构建应用感兴趣。在工具和平台的旧场景中，我们开发人员基本上是把车开出停车场，只是偶尔回来预约服务(通过支持订阅或软件升级)。

他们希望我们构建依赖于他们服务的应用程序，不管我们是否使用他们的工具。在这个类比中，我们的汽车可能不在微软的停车场，或者完全不在另一个停车场，但是每次我们点火、打开收音机或踩刹车时，我们都依赖微软。

我并不是说这是一个不好的改变——不必构建服务器基础设施或为许多复杂的代码任务重新发明轮子，这意味着我们可以完成过去可能无法实现的事情。但是我们应该意识到行业中的这些变化将会如何戏剧性地改变对开发者的期望。