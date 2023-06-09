# 一次处理一个 Perl 6 文档错误

> 原文：<https://dev.to/jj/squashing-perl-6-documentation-bugs-one-at-a-time-4ojn>

每月一次的社区 bug SQUASHathon 将于明天欧洲中部时间 11 点开始。而*你*可以帮忙。

[![Nasty bugs](img/7b1bd0f5c2d98d56a5bcda1903cdd370.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tD5A3YeN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tjte82wr6dynmq2c27hy.jpg)

## 虫子是讨厌的东西

但是谁能摆脱它们呢？不是 Perl 6 文档，这将是这次黑客马拉松的焦点。这个存储库包含了在 docs.modules.org 发布的文档和工具。

这是该语言的官方文档。您的贡献将成为 Perl 6 语言官方文档的一部分。你能想象吗？是的，你可以！因为你可以，也愿意，在这次黑客马拉松中提供帮助！

## 你能帮上什么忙？

黑客马拉松将持续 50 个小时，于西欧时间周日上午结束(不同时区会有所不同)。你可以随时加入，随时离开，想和多少虫子一起工作都可以。协调将通过[、#perl6 和#perl6-dev 通道](https://perl6.org/community/irc)完成，所以如果你感到迷失，或者需要一些帮助或指导，启动你的 [IRC 客户端](https://opensource.com/life/15/11/top-open-source-irc-clients)并四处询问；会有人来帮忙的。你也可以[直接切入其中一个议题，如果你愿意的话先从*好第一个议题*](https://github.com/perl6/doc/issues?q=is%3Aissue+is%3Aopen+label%3A%22good+first+issue%22) 开始。在你感兴趣的问题上发表评论，然后让别人把它分配给你，这样就不会有人干涉你的工作了。完成后，[提交一个拉取请求](https://github.com/perl6/doc/pulls)。PR 将被审查，并最终被接受！谢谢你的提问和贡献。

## 现在，真的，我能帮什么忙？

[Perl 6](https://github.com/perl6/marketing/raw/master/Brochures/Introducing-Perl6-Brochure--1516098660/Introducing-Perl6-Brochure--1516098660.pdf) 是一门相对较新的语言，很可能到目前为止你都没有听说过。这不一定是在文档中进行协作的障碍。你可以一起学习，你会发现通过阅读材料和帮助它或例子来学习更容易。文档的语言是英语，您可能已经懂英语了。但是这里有几件事你可以帮忙

*   如果你要写新的材料、例子，甚至是新的页面，通读存储库中的贡献文件会有所帮助。
*   对 Perl 6 的温和介绍不会有什么坏处。至少对这种语言的工作原理有一个大致的了解，至少这样您就可以理解文档中示例的一般机制。
*   Perl 6 文档是用 [Perl 6 POD](https://docs.perl6.org/language/pod) 编写的。它是一种标记语言，包括通常的东西:块、强调和代码示例。你会在以`=`开头的行中认出它。你不需要学习整个语言，但至少了解它是如何呈现为 HTML 的总是有帮助的。
*   安装了 Perl 6 很方便，以防万一您想要测试运行一个示例或者在您的系统中实际构建文档。推荐的方法是使用 [Rakudo star](https://rakudo.org/files) ，这是一个二进制形式的现成发行版，适用于任何操作系统。

## 我只有半个小时，但是我想帮忙！

不客气！黑客马拉松的目的是改进文档，而文档是为不熟悉该语言的开发人员准备的。因此，您可以尝试了解 Perl 6 中的一些事情是如何完成的，并查看文档是否充分描述了必须做的事情。想想任何语言结构或特定任务，在 Google 中搜索它，看看你是否进入了 [Perl 6 文档页面](https://docs.perl6.org)；如果你没有，你可以[提出问题，说文件没有很好地定位](https://github.com/perl6/doc/issues)；如果你这样做了，而文档页并没有真正地削减它，文件的问题，说明我们失去了你:太复杂的语言，例子不够清楚...不管是什么问题。问题也有助于改进网站，也许这很容易，其他参与黑客马拉松的人也可以提供帮助！

## 我有整个周末，我*真的*想帮忙！

不客气！为一种语言编写文档的问题是，这不是重复使用其他文档，实际上没有为它编写的文档。如果你想记录一个没有被记录的函数或者类型，下面的方法可以帮助你找到它实际上是做什么的。大部分包括在 GitHub 中搜索[，所以你需要在那里有一个账户。](https://github.com)

1.  进行一般的网络搜索，或者在 Perl 6 降临节日历中特别搜索[。从 2009 年 12 月](https://perl6advent.wordpress.com/)开始就有了降临节日历[；他们中的一些人谈到了实验性的特性，并不是所有的都被写进了通用文档，所以可能会有一些东西没有出现在](https://perl6advent.wordpress.com/2009/12/)[这里](https://modules.perl6.org)。

2.  如果你已经在 IRC 频道的[中，你可以使用有用的](https://perl6.org/community/irc)[机器人搜索代码](https://github.com/perl6/whateverable)。正如 AlexDaniel 所说,`greppable`将会搜索整个库并找到你正在寻找的源代码。一旦进入 IRC 通道，写:`greppable: CompUnit`，它将回答类似`greppable6 | jmerelo, 10251 lines, 698 modules: https://gist.github.com/35124b916a96e596a18786b8fe318d71`的问题(你可以检查一下要点，它是永久的，仍然存在)。这将帮助您搜索所有的存储库。如果你不想打扰通道的其他部分(你这么做没有问题，其他人也可以帮忙)，[使用 freenode](https://github.com/perl6/whateverable/wiki/Adding-New-Features) 上的#whateverable 通道。这个机器人，以及它的其他兄弟姐妹，也可以在那里找到。

3.  在通用 Github 搜索中搜索[。什么时候没有记录的东西会阻止任何人使用它？在某些情况下，您会发现实际代码中使用的类型，并且您将有一个现成的示例可以使用，或者使用一个问题来询问作者该代码的意图和效果是什么。当然，将搜索减少到`Code`并使用`language:"Perl 6"`来限制你的搜索。](https://github.com/search?l=&q=ACCEPTS+language%3A%22Perl+6%22&type=Code)

4.  在[烘焙](https://github.com/perl6/roast)、 [Perl 6 测试库](https://perl6advent.wordpress.com/2013/12/13/day-13-roasting-rakudo-star/)中搜索函数或类型。如果它在语言里，那它就在烤肉里。如果您已经完成了上面的搜索，您可能已经找到了，但是您也可以关注它来检查不同的实现。

5.  在 [Rakudo 库](https://github.com/rakudo/rakudo)中搜索源代码。Rakudo 是 Perl 6 编译器，它主要是用 Perl 6 和一个叫做[的东西编写的，不太像 perl](https://github.com/perl6/nqp) 。它，嗯，不完全是 Perl，但是如果你至少能够阅读源代码，你将能够理解它，足以解决未记录的特性问题。

如果你这样做，那就太好了。你将帮助 Camelia 和整个自由软件社区。

[![Beautiful camelia](img/18a7ea491f708bbe1482d71f63823a2f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FGRI3OgD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/curepuygdjb232b6br6e.jpg)

## 我*真的*没时间，但我还是想帮忙。我这周末有场复仇者联盟马拉松...

别担心。只要在你最喜欢的社交网络中传播这个消息。使用 [#perl6](https://twitter.com/search?q=%23perl6&src=typd) 和 [#squashathon](https://twitter.com/search?q=%23squashathon&src=typd) 标签，它会找到已经看过所有《复仇者联盟》电影的人，会很友好地不会破坏它，还会在这个特定的黑客马拉松中提供帮助。也非常欢迎你！