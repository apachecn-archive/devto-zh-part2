# 。NET Framework 对。网络标准 2.0

> 原文：<https://dev.to/stuartblang/net-framework-support-for-net-standard-20-5gkj>

什么时候。NET 标准 2.0 的引入，并不是一帆风顺的。。NET Standard 1.5 和 1.6 已经是一个东西了，它们包含了来自。高于. NET 框架。NET 框架 4.6.1。自从。NET 标准版本应该总是以前版本的超集，这是很自然的。NET Standard 2.0 仅在中受支持。NET 框架高于或等于 4.7.0，因为标准 1.5 包括来自框架 4.6.2 的 API，标准 1.6 包括来自框架 4.6.3 的 API。

然而，微软看到了扩大。NET 标准 2.0 来包含。NET Framework 4.6.1，这意味着更改现有的框架支持级别。NET Standard 1.5 和 1.6，因为它们已经发布了。这是由于 4.6.1 运行时被广泛使用的事实而采取的方向，它会阻碍的采用和成功。NET 标准，以及。NET 核心作为结果，如果它被排除在外。

拥有。使用. NET Framework 4.6.1。NET Standard 2.0 软件包是非常雄心勃勃的，因为它已经在使用中，需要回溯性地构建支持。

虽然这种方法很有效，但实际上我发现写作中会遇到很多问题。可以在. NET 标准库上运行。NET Framework 4.6.1，并使用它们。我遇到的一些问题与 CI 构建代理没有必要的 nuget 和 MSBuild 版本有关。NET 标准 2.0 库，但更大的问题是关于程序集绑定重定向和运行时问题，[这里只是一个味道](https://github.com/dotnet/standard/issues/481)。随着时间的推移，出现了许多已经成为已知问题的问题。

由于我面临的所有问题，以及来自其他开发者的故事，我去年给人们的指导是，如果他们真的热衷于只有一个目标，那么在出现麻烦的第一个迹象时就瞄准`netstandard2.0`和`net461`，或者从一开始就瞄准两个目标，以获得幸福和平的生活。

最近 Immo Landwerth [在一条推特](https://twitter.com/terrajobst/status/1031999730320986112)上说。NET 标准 2.0 工作。NET Framework 4.6.1 是一个错误，文档将被更新以反映新的指导。他[澄清](https://twitter.com/terrajobst/status/1032000874552250368)这与缺失的 API 无关(1.5 & 1.6 中一些大量未使用的 API 就是这种情况)，而是与工具等其他问题有关。你可以在[本期 GitHub](https://github.com/dotnet/standard/issues/481)中看到其中的一些问题。

> ![Immo Landwerth profile image](img/80d3cd2d47abba0b48d634cbe99ba4f5.png)Immo Landwerth@ terra jobst![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)不好意思我们搞砸了。我们试着。NET 框架 4.6.1 追溯实施。NET 标准 2.0。这是一个错误，因为我们没有时间机器，而且还有一大堆错误。
> 
> 如果要消费。网标 1.5+起。NET 框架，我推荐 4.7.2 上。[twitter.com/marcgravell/st…](https://t.co/E7H2Ps9cLk)2018 年 8 月 21 日晚 20 点 21 分Marc Gravell@ Marc Gravell叹息；系统问题的另一个报告。在 net461 上运行的. net standard 2.0 lib 中的 Numerics.Vectors 导致绑定问题。耶。[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1031999730320986112)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1031999730320986112)157[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1031999730320986112)313

由于[大卫福勒最近的这条推文](https://twitter.com/davidfowl/status/1008453902058917888)，最近有一股转向`netstandard2.0`的动力，以获得它给开发时间带来的好处，这是一个很好的建议，但许多人选择借此机会删除`net461`或更低版本，这意味着更新的消费者现在面临一些问题。

> ![David Fowler profile image](img/dd21ffe8038eaaeea3a586440422a4a9.png)大卫·福勒@大卫·福勒![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)我知道我们说过目标是最低的网络标准，但我们真的希望每个人都重新以网络标准 2.0 为基准。至少添加一个 netstandard 2.0 目标。 [#dotnet](https://twitter.com/hashtag/dotnet) 核心[# dot net](https://twitter.com/hashtag/dotnet)2018 年 6 月 17 日 20 点 58 分[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1008453902058917888)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1008453902058917888)69[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1008453902058917888)148

## 推荐

所以我今天对打算为多个平台(包括在`net461` - `net471`上运行)构建包的库作者的建议是，以`netstandard2.0`和您想要支持的`net4x`平台为目标。。NET Framework 4.7.2 是在考虑了`netstandard2.0`的情况下构建的，因此在这一点上，您可能会失去单独的目标。

如果我们想用[快速想象这个。NET 标准实现支持表](https://docs.microsoft.com/en-us/dotnet/standard/net-standard#net-implementation-support)，那么下面是它的样子:

| 。净标准 | Two | Two point one |
| --- | --- | --- |
| 。网络核心 | Two | Three |
| 。NET 框架 | 可行但有问题:4.6.1
作品:4.7.2 | 待定(非 4.8) |

如果“可行但有问题”的部分让你感到不舒服，请随意解释为“行不通”。实际上，这意味着为可能的痛苦做好准备，这是作为一个图书馆作者你不希望释放给你的消费者的东西。

如果你是因为跑步而遇到这些问题的消费者。上的. NET 标准包。NET Framework 4.6.1，您应该优先升级到 4.7.2，从长远来看，这将真正节省您的时间和痛苦。如果你不能直接做到这一点，对那些控制这一点的人施加压力。

微软将在官方指导下更新他们关于这个主题的文档，我将在这个帖子发布后更新它。

## TL；速度三角形定位法(dead reckoning)

[![](img/4833191d219e43219ccb7f812b194255.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--g2Q0siTx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://stu.dev/conteimg/2018/08/netstandard-tldr-1.jpg)