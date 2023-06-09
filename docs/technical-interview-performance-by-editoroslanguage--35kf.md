# 按编辑/操作系统/语言列出的技术面试表现

> 原文：<https://dev.to/triplebyte/technical-interview-performance-by-editoroslanguage--35kf>

*最初发布在[三字节博客](https://triplebyte.com/blog/technical-interview-performance-by-editor-os-language?ref=devto_mar18_sp)上。*

我的同事丹尼尔非常喜欢 Emacs。这是他的主要编辑，他的购物清单(通过 [org-mode](http://orgmode.org/) )，以及他在 OkCupid 档案中不能缺少的 6 样东西中的第 4 样。然而，办公室里的其他工程师更喜欢 Vim(或 Sublime Text，甚至 RubyMine)。自然，这就导致了战争。但是经过几个月的激烈争论([维姆戈尔夫](https://vimgolf.com/)胜人一筹，无端提到[这个](https://stackoverflow.com/questions/11828270/how-to-exit-the-vim-editor)烟囱溢出问题)，没有明显的胜利者出现。

因此，上周，我着手看看三字节数据在这个问题上有什么说法。对此要有所保留(相关性并不意味着因果关系)，但工程师使用的编辑器、他们的语言和操作系统以及他们通过编程面试的能力之间存在相关性。而且，根据我们的数据，Vim 用户通过面试的比率比 Emacs 用户略高！<sup id="ref1">[【1】](#fn1)</sup>

## 关于数据

在 Triplebyte，我们每周都会评估数百名工程师。每次面试都包括与我们的一位面试官交谈(比如我！)在谷歌视频聊天上。我们记录每个工程师使用的语言和编辑器。面试结束后，我们把工程师介绍给其他公司(他们跳过了电话屏幕)。

为了收集我想要的数据，我提取了去年每个三字节面试的编辑/语言数据，以及三字节面试和同一工程师通过我们的平台进行的公司面试的结果(通过/失败)。下面是编辑器使用的分类:

## 编辑和 IDEs

这不再仅仅是一个 Emacs 和 Vi(m)世界了(曾经是吗？):

[![Editor - Breakdown.png](img/dfa0ce6b3c56842d2a6bf37e170b171f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--r4_COnWd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d25hn4jiqx5f7l.cloudfront.net/file_attachments/files/original/6601f0866082ad8dac318cbf1b3a457cfa69898e.png%3F1505780709)

我们在采访中看到的最常用的编辑器是 Sublime Text，Vim 紧随其后。

在我们的采访中，Emacs 代表了相当小的市场份额，大约只有 Vim 用户群的四分之一。这很好地匹配了两位编辑的谷歌搜索趋势的 4:1 比例。

然而，我真的在寻找质量的信号，而不是数量。哪些工程师最有可能顺利通过我们的面试？

[![Editor - Tech interview pass rate.png](img/98860c9eb4d5a64b887f173f3c89dbf5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JyZVsbtj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d25hn4jiqx5f7l.cloudfront.net/file_attachments/files/original/c297bfc4d3b5d1bed45a77ecf16e7020284c8b10.png%3F1505780753)

Vim 在这里获奖，但是 PyCharm 和 Emacs 紧随其后。我们发现这些编辑器的用户倾向于以高于平均水平的比率通过我们的面试。

另一个极端是 Eclipse:使用 Vim 或 Emacs 的人通过我们技术面试的可能性似乎是 Eclipse 用户的两倍多。

我很想知道面试后会发生什么，所以我观察了每组通过最终现场面试并获得我们联系的公司的工作机会的可能性:

[![Editor - Onsite success rate.png](img/205ce717ddc21f4a7bf0014b94fee490.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--62G7OP0Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d25hn4jiqx5f7l.cloudfront.net/file_attachments/files/original/0c8429f60b923c0e625d0736c961110544122cdb.png%3F1505780770)

在很大程度上，这在编辑之间看起来相当均匀，这告诉我们，即使 Vim 用户作为一个群体比 Sublime 用户更强大，当谈到 Triplebyte 的采访时，我们仍然可以确定 Sublime 用户的正确子集，他们将在我们合作的公司中表现良好。

我惊讶地发现这里有一个巨大的异常值:Eclipse！奇怪的是，似乎即使我们预先筛选了 Eclipse 用户的编程技能，与其他人相比，他们仍然只有一半的现场面试机会。我对这种相关性没有一个很好的解释，这是我将要研究的东西。(无论如何，不要担心，我们仍然愉快地接受并为许多伟大的 Eclipse 用户找到工作！)

## 编程语言

在我们技术面试的第一部分，你可以使用任何你喜欢的语言。如今，许多工程师选择用 Python 进行面试:

[![Language - Breakdown.png](img/95cf549d9dde39f510993b300d08c74e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kmOXiatz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d25hn4jiqx5f7l.cloudfront.net/file_attachments/files/original/6f4483a664ac5c11e5c558a886dffc4816ad8595.png%3F1505780899)

嘿，PHP 怎么了？作为一名前 PHP 开发人员，我发现有趣的是，它现在只是“其他”桶中的一小滴。作为这里的面试官之一，我个人还没见过有人用 PHP 面试(虽然别人见过)。

Python 也以对初学者友好而闻名，那么类似 Eclipse 的趋势会在这里出现吗？嗯，不完全是:

[![Language - Tech interview pass rate.png](img/951b7a783a9d175c87e596585dc71be3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8sVsV5ls--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d25hn4jiqx5f7l.cloudfront.net/file_attachments/files/original/97aff5ad4d163cb11d063e2375fd7559842a8d67.png%3F1505780931)

在这种情况下，我们发现普通的 Ruby、Swift 和 C#用户往往更强，Python 和 Javascript 处于中间位置。

当我们深入研究这些数字时，值得注意的是某些编辑器和某些语言之间不可避免的紧密联系:90%的 Eclipse 或 IntelliJ 用户也使用 Java，99%的 PyCharm 用户使用 Python。我发现的一个有趣且具有统计学意义的结果是，与使用 Eclipse 的人相比，使用 Eclipse 以外的任何东西的 Java 开发人员通过我们面试的可能性要高 47%。

以下是我们挑选工程师一起工作并把他们派到现场后的情况:

[![Language - Onsite success rate.png](img/8c47b295f903461640bfa9284ba0381c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wkQdI_qe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d25hn4jiqx5f7l.cloudfront.net/file_attachments/files/original/6e48822b629946201c3a438e08ec547cbf318694.png%3F1505780946)

这里没有异常值，但是让我们看看 C++部分。虽然 C++程序员平均来说最难通过 Triplebyte 的技术面试，但我们选择与之合作的人往往更容易在每个现场获得报价。

我不完全确定如何解释这一点，但一个假设可能只是看看我们平台每一侧的总体供应和需求。我们看到与我们合作的公司对有才能的系统和低级程序员的强烈需求。另一方面，我们受限于使用 C++申请的相对较少的一部分人，乘以相对较少的一部分我们认为有足够才能与之共事的人。另一个想法是，也许更多的利基专业化可能会给我们的匹配过程更多的信号。无论如何，这告诉我，我们应该寻找更多有才华的 C++开发人员！

## 操作系统

如果你在过去几年里经常光顾旧金山的一家咖啡店，那么今天大多数工程师都在 macOS 上接受面试就不足为奇了:

[![OS - Breakdown.png](img/8e52e795d17295436704101bf95698a4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ms_hRhdX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d25hn4jiqx5f7l.cloudfront.net/file_attachments/files/original/2652c10428eefab788fe6fe5d8315f29155fb53f.png%3F1505780975)

在这种情况下，最受欢迎的选择也是最成功的:

[![OS - Tech interview pass rate.png](img/b7ca74bb2880780ffdbd97b96632330d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dQoMj6nN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d25hn4jiqx5f7l.cloudfront.net/file_attachments/files/original/016bc1192d0c93e279f8556d4a7a95ab0c10a24b.png%3F1505780989)

我们发现，Windows 用户平均来说更加努力。在这个类别中，我很惊讶没有看到 Linux 处于领先地位。作为一个以前的 Linux 桌面用户，我猜想 Linux 用户可能拥有与其日常计算相关的广泛的技术知识。

在现场采访中，这一趋势仍在继续:

[![OS - Onsite success rate.png](img/5edb6b73bbf746717a5844635e747f90.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--I3Z0fl5I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d25hn4jiqx5f7l.cloudfront.net/file_attachments/files/original/8f6f95ba4fa5b1a00b4c69c096614b226a539f71.png%3F1505781002)

奇怪的是，与我们一起工作的苹果粉丝似乎也比 Windows 或 Linux 用户多通过了约 20%的现场面试。(这让我想起了一篇旧的 [OkCupid 博客文章](https://theblog.okcupid.com/dont-be-ugly-by-accident-b378f261dea4)比较苹果和安卓用户的数据。)

很难知道到底为什么会发生这种情况，但这里可能有一个隐藏的变量。例如，虽然我们采访的人中有 60%使用 macOS，但如果你走进旧金山的任何一家科技公司，你不可避免地会看到远远超过 60%的 MacBooks。虽然我们的面试对背景一无所知，但拥有几年的专业编程经验通常会有所帮助！

## 结论

作为一名采访者和工程师，我认为看看我们掌握的工程师们今天使用的工具的数据会很酷。

在我们 2017 年的数据集中，我们发现在 macOS 上用 Vim 编写 Ruby 的工程师通过我们技术面试的机会高于平均水平，而在 Windows 上用 Eclipse 编写 C++的工程师则处于另一端。我们不会明确使用这些指标来决定我们与谁合作，但这是我们在采访过程中收集的数据的副产品。

在更高的层面上，我们认为[招聘过程应该专注于发现优势，而不是发现弱点](https://triplebyte.com/manifesto?ref=devto_mar18_sp)，我们已经就[如何面试工程师](https://triplebyte.com/blog/how-to-interview-engineers?ref=devto_mar18_sp)写了大量文章。我们对公司的最佳建议是，允许候选人使用能让他们最好地展示个人优势的工具。在我们的情况下，这意味着我们允许工程师选择他们选择的任何语言和环境，并在家里使用屏幕共享进行方便的采访。

如果你是一名工程师，想要尝试我们的过程，第一步是完成我们的多项选择编程测验[这里](https://triplebyte.com?ref=devto_mar18_sp)。

如果你是一家寻找工程师的公司，并且想学习更多关于使用 Triplebyte 的知识，你可以从这里开始。

* * *

<sup id="fn1">【1】</sup>说清楚一点，我不认为这对招聘有任何预测价值，我也不认为工程师应该根据我们的分析来更换编辑。Emacs(和 Eclipse)是伟大的编辑器！ [↩](#ref1)