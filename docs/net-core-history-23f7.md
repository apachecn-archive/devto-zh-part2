# 。净核心历史

> 原文：<https://dev.to/dotnetcoreblog/net-core-history-23f7>

在最近的一次 ASP NET 社区会议上，Damien Edwards 指出，自从直播流从 Scott Hanselman 的 YouTube 帐户转移到。NET Foundation one 的收视率减半了。

这是一个真正的耻辱，因为每周站立是一个奇妙的观点。NET 和 ASP.NET 核心社区以及关于新兴功能的新闻。这也是一个向主持人(乔恩·加洛韦，以及上面提到的达米恩·爱德华兹和斯科特·汉瑟曼)提问的好机会。网络核心、ASP.NET 核心和社区。

我肯定会推荐订阅。NET Foundation YouTube 帐户。的。NET Foundation 产生的不仅仅是每周一次的站立表演。找到它最快最简单的方法就是前往 live.asp.net。

* * *

在今天的博文中，我想我应该回顾一下。网芯。你知不知道整个历史。NET Core 和 ASP.NET Core 是公开的吗？

### 开始

一开始，有一个。NET 框架；感觉很好。在我们继续之前，我要讲一部分。网芯的开源发布错了。如果你想更详细地了解微软和微软的历史。那么我会推荐你看一看理查德·坎贝尔的。网谈。

这是一个嵌入的 YouTube 播放器:

[https://www.youtube.com/embed/FFCn_z7dn_A](https://www.youtube.com/embed/FFCn_z7dn_A)

很多人认为微软的工作是封闭的源代码。虽然大多数项目都是如此，但微软实际上向选定的合作伙伴提供了很多项目的源代码。

他们还发布了。NET Framework 结束于[引用源](https://referencesource.microsoft.com/)。他们不像 Linus Torvalds 和他的[小内核项目](http://www.thelinuxdaily.com/2010/04/the-first-linux-announcement-from-linus-torvalds/)那样开放，但是仍然相当开放。

### 萨提亚来了

让我明确这一点:微软是一家了不起的公司。看看他们免费提供给我们开发人员的所有东西(或者与我们收取的服务费相比，成本很低)。

当然，当他们使用的工具崩溃时，人们会抱怨它，但是它总是一个由数千人组成的团队开发的大型软件。然后他们用 Azure 把它带到了一个新的高度。我可能有点偏见，因为我最近参加了斯科特·格思里举办的蓝色红衫军发展之旅讲座。

但我要说这种天蓝色绝对令人惊叹。我最近的工作职责之一就是调查 Azure，看看我们能用它做些什么。到目前为止，答案是:几乎任何事情。塞特亚·纳德拉(第三任，现任微软首席执行官)是 Azure 的大力支持者之一，正是通过他的坚持，Windows Server、SQL Server 和其他一系列微软核心技术现在都可以在 Azure 上使用。

当萨提亚在 2014 年成为微软首席执行官时，很明显，微软将要前进的方向将与他们之前 40 年的运营略有不同。他们打算追求云，他们不仅想在这方面做到最好，还想为开发人员和企业提供最佳平台，帮助他们将应用程序提升并转移到云。而且，他们[决定开源。网](https://www.wired.com/2014/11/microsoft-open-sources-net-says-will-run-linux-mac/)。

这不是第一部分。NET 框架，它是开源的。除了有权访问。NET 框架源代码的第一部分。将被公开开源的. NET 框架是[动态语言运行时](https://en.wikipedia.org/wiki/Dynamic_Language_Runtime)(为我们提供了`dynamic`关键字的东西)。DLR 早在 2007 年就发布了，但直到 2010 年才发布(2008 年有一个 alpha 版本)。DLR 在 Mono 项目发布时就被采用了。开源的两个目标。NET 和推 Azure 齐头并进。

### 一切都是开放的

就在萨提亚告诉所有人。NET 将会是开源的，有一个微软工程师团队正在狂热地开发一个叫做 ASP NET vNext 的项目。“vNext”是他们给这个项目起的名字，它将是 ASP NET 的下一个版本。

它将是完全开源的，整个代码库都托管在 GitHub 上。它没有隐藏在 GitHub 中，也不是在微软内部托管，然后复制到 GitHub 中。微软工程师正在 GitHub 上开发 vNext...在户外。

人们认为公开记录“vNext”的发展是一个好主意，因此 Scott Hanselman、Damien Edwards 和 Jon Galloway 开始每周进行 ASP NET 社区脱口秀。这些长达一小时的 Google Hangout 电话向社区直播，并存储在 YouTube 上。这些公开发布的视频的奇妙之处在于，它们使您能够回顾并观看“vNext”向 ASP NET Core 的演变。事实上，这是他们做的第一个(日期为 2014 年 9 月 2 日):

[https://www.youtube.com/embed/1rLbT6pBtak](https://www.youtube.com/embed/1rLbT6pBtak)

##### 从通话记录中得知

我觉得看一看我第一次看那个电话时的笔记会很有趣。所以这里有:

*   文件>新项目是主要目标
    *   达米安·爱德华兹:改变文件>新项目体验处理方式的想法
*   已经支持 MacOS 和一些 Linux 发行版
    *   这是主要的。我现在可以在家里开发 ASP NET 的东西
    *   想法是尽可能多地模块化 ASP.NET

这是我们已经看到的东西，但是当你看着[ASP . NET 核心元包](https://www.nuget.org/packages/Microsoft.AspNetCore.All/)中的包时，它变得更加普遍。

*   使用 project.json
    *   这与 csproj 不同

最终，project.json 将被 csproj 文件重新替换。但是，回头看看这个早期的视频，你就能明白他们为什么选择试用 project.json 了(这在我引用 David Fowler 的话时会更明显)

*   谈论 Azure 作为部署目标

这很重要。Azure 在此之前几年才出现，而且它肯定没有像最近那样出色地重写 UI。它当然也没有像现在这样提供很多东西。

*   “Kestrel 基于 libuv，它是在 nodejs 上运行的跨平台网络堆栈。我们用它做了一个叫做 Kestrel 的网络服务器，这是 C#直接与 lubuv 对话。我们的设想是，你将把它作为主要的网络服务器，然后把 nginx 或 apache 放在它的前面。”
    *   大卫·福勒展示了节点的影响

由于 Kestrel 的设计使用了 nodejs 使用的 [libuv](https://en.wikipedia.org/wiki/Libuv) ，因此可以理解为什么“vNext”团队会选择使用稍微不同的项目文件类型。尽管不久之后 [project.json 将被](https://dotnetcore.gaprogman.com/2017/03/09/converting-from-project-json-to-csproj/)取代。网芯 1.0 发布。

*   “我的目标是希望您能够像在 Windows 服务器上一样轻松地在 Linux 服务器上托管您的 ASP.NET 应用程序...如果你想的话，可以放在码头集装箱里。”

    *   斯科特·亨特
    *   而且，这是巨大的！
*   “但是[Kestrel]不会具备 IIS 的所有功能。那是一个完全不同的故事。”

    *   斯科特·亨特

除了节假日和有活动的时候

*NDC，。网络会议等。*

每周都有 ASP NET 社区的起立鼓掌...三年了。截至发稿时，已有 127 个视频。内容太多了，尤其是大多数视频都在 45 到 80 分钟左右。

### 是所有的技术细节吗？

许多早期视频都是关于 vNext 的工作原理、kestrel 的工作原理以及一些设计决策。这些视频通常让 Damian 在白板前解释软件的不同部分是如何工作的，它们非常有趣——如果你是那种喜欢知道它们是如何组合在一起的人，那么我会推荐你观看早期的视频。最近，电话也有了类似的格式，大概是这样的:

*   所有三位主持人(如果在场的话)将谈论他们这一周的经历以及他们做过的酷事
*   Jon 将通过他发现的有趣的社区链接进行讲解
*   达米安将谈论一些即将出现或正在内部讨论的令人兴奋或有趣的事情
*   斯科特通常会带头打电话
*   达米安将戴两只手表
*   斯科特会取笑乔恩有太多的 Chrome 扩展
*   每个人都玩得很开心

这是一个专业的电话会议，就像开发者之间的专业会议一样，会有一些轻松、愚蠢的时刻穿插在整个谈话中，但是大部分电话会议都是关于严肃的商业问题。

* * *

第一个站起来的是我对 ASP NET Core 的介绍(当时它被称为“vNext”)，从那以后我一直关注它的发展。我对这个团队在成立后的几年里提出的一些想法非常感兴趣，最终我开了这个博客。他们每周都有一次(给或花时间去度假和大型活动，如。NET Conf)，所以它绝对值得收听，即使你以后再看。

它们是 ASP NET Core 世界未来发展的极好资源，也是向团队提出问题的机会(如果你能现场观看的话)。最新一集是关于从 ASP NET Core 2.1 的类库中调用 Razor UI 代码的。这是那一集 YouTube 视频的嵌入片段

[https://www.youtube.com/embed/6eVdRyXADaw](https://www.youtube.com/embed/6eVdRyXADaw)

* * *

你以前听说过 ASP.NET 社区联盟吗？你经常看吗(直播或点播)？你真的应该看着他们，你知道。它们真的很酷，包含了一些关于 ASP NET 核心的最新信息和一些惊人的社区链接。