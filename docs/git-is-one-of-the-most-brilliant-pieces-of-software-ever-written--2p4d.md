# Git 是有史以来最出色的软件之一...以及对 git 的其他看法

> 原文：<https://dev.to/ben/git-is-one-of-the-most-brilliant-pieces-of-software-ever-written--2p4d>

不久前，我开始了关于 git 主题的讨论:

[![ben](../Images/fe64a787b888dfb20fc13ad1e466da3d.png)](/ben) [## git 是版本控制的全部吗？

### 本哈尔彭 10 月 9 日 171 分钟阅读

#discuss #git](/ben/is-git-the-be-all-and-end-all-of-version-control-4lp)

我很好奇 git 是否真的是所有用例的赢家。我的想法是 git 的一些特性对于不同的情况来说是不必要的复杂。我并没有过于固执己见，但我认为这将是一次有益的对话。

对这个帖子的一些回复正面回答了这个问题，但其中许多只是一个表达 git 的历史和辉煌的机会。这是一个伟大的阅读，我想分享一些。

[![leob profile image](../Images/38b82f6a1937a20ed9ac7293f170a871.png) ](/leob) [ leob ](/leob) • [<time datetime="2017-10-10T01:51:16Z" class="date-short-year"> Oct 10 '17 </time>](https://dev.to/leob/comment/12ch) 

在我看来，Git 是有史以来最出色的软件之一(我甚至可以说它是 Linus 最大的成就，而不是 Linux)。这是一个设计非常好的软件的例子，它的多功能性和性能让你(至少是我)感到惊讶。

到目前为止，没有任何真正的竞争对手，也许这就是促使你问“难道没有其他选择”的原因。在其他领域(后端框架、前端框架、操作系统等等)有几十种选择，没有一种是明显优越的，但是 Git 确实是占优势的，但是我说它是当之无愧的。

对我来说，至少有一个领域是新鲜空气，我不会被太多没有明显附加值的选择所困扰。

再加上巨大的安装基础(仅 Github 一家，但也包括企业)和 Git(和 Github)对 OSS 和“社交编码”(共享、贡献)的巨大推动，那么我的结论是，任何替代方案都必须明显更好，不是 50%或 100%“更好”，而是好一个数量级，否则它就没有机会。

[![jballanc profile image](../Images/6e963e3a49cebc0ccaf7704c2b21098b.png) ](/jballanc) [ Joshua Ballanco ](/jballanc) • [<time datetime="2017-10-09T20:28:04Z" class="date-short-year"> Oct 9 '17 </time> • Edited on <time datetime="2017-10-09T21:09:11Z" class="hidden m:inline-block date-no-year">Oct 9</time>](https://dev.to/jballanc/comment/12af) 

Git 是一个典型的例子，它是为解决一个问题而发明的，但最终却解决了另一个更重要的问题。出于这个原因，我*确实*认为 Git 最终会(不，*会*被更好的东西取代，直接解决 Git 偶然解决的更重要问题的东西。

更具体地说:当 Linus 创建 Git 时，他的目标是创建一个完全分布式的版本控制系统。你必须明白，他针对的是 Linux 内核，这是一个分布极其广泛的项目，其中有许多“事实的来源”。换句话说，我们的目标是 Linux 基金会可以托管一个 Git 服务器，它有“官方”Linux 内核的历史、分支和标签，但是 RedHat 或 Canonical 或 Debian 可能*也会*托管 Git 服务器，它们有自己的分支、历史和内核标签，就像“官方”版本一样完整。

*大多数*团队，然而*没有一个以上的“真相来源”。每次你看到人们抱怨“GitHub 宕机了”，他们真正想说的是“尽管 Git 是完全分布式的，并且能够支持多个‘权威’节点，但我们已经同意使用 GitHub 作为我们都依赖的*事实上的*中央存储库”。*

 *那么，为什么那么多人在对版本控制系统的存在理由不感兴趣的时候使用它呢？因为在这个过程中，Linus 解决了一个更小但更有价值的问题:本地版本控制。

年轻的程序员可能不记得了，但是在 Subversion 的糟糕日子里，如果你想保存你正在做的东西，以便你可以继续做别的东西，你的选择是:a .)本地保存文件，然后当到了`svn commit`的时候尝试回溯修改，或者 b .)确保你连接到 Subversion 服务器`svn commit`，并且祈祷不与其他任何人正在做的任何东西冲突。

实际上，*是*的第三种选择，被称为“SVK”。从表面上看，`svk`的工作方式很像今天的`git`:你可以在本地保存一份 Subversion 库的副本，你可以根据需要提交给它，并且定期对主 Subversion 库进行`svk sync`。

所以，老实说，我相信 Git“赢”不是因为它完全是分布式的，而仅仅是因为它允许开发人员随意在本地工作，只在需要时集中同步。实际上，这是从最早的版本控制系统到 Subversion 的自然发展，在最早的版本控制系统中，在编辑文件之前，您必须在中央服务器上实际*锁定*文件，Subversion 取消了锁定，但仍然需要访问中央服务器才能进行版本更改。一些新系统更加关注*本地*开发人员的体验，并且简化了“中央存储库”的情况(因为*绝大多数团队将只有一个中央存储库)，可以轻松地吃 Git 的午餐。**  *针对约书亚的评论:

[![etcwilde profile image](../Images/2539853d77618b6e92de9cd805bbd8c8.png) ](/etcwilde) [ Evan Wilde ](/etcwilde) • [<time datetime="2017-10-10T04:46:52Z" class="date-short-year"> Oct 10 '17 </time>](https://dev.to/etcwilde/comment/12d6) 

这是一个很好的回答，尽管我认为它在为什么创建 git 部分遗漏了一个相当重要的点。Bitkeeper，linux 最初的 VCS，最初有极其严格的许可，但对开源项目是免费的(钱)。然后在 2005 年，改变了许可，开始对开源项目收费。该项目的大多数开发人员将能够购买该软件，但由于 OSDL (Linux 基金会)正在进行的一个独立项目违反了限制性许可，因此任何为 OSDL (Linus Torvalds)工作的人都不能使用该软件。

Linus，以他典型的 Linus-y 方式，把 Bitmover 变成了一只鸟，并建立了他自己的 git，“愚蠢的内容跟踪器”。Bitkeeper 是分布式的，具有大部分必要的特性，但是限制性太强。

[![jillesvangurp profile image](../Images/f737729e860a8b0cf136f1cb1dd72d5e.png) ](/jillesvangurp) [ Jilles van Gurp ](/jillesvangurp) • [<time datetime="2017-10-10T06:54:53Z" class="date-short-year"> Oct 10 '17 </time>](https://dev.to/jillesvangurp/comment/12dn) 

不同分支和存储库之间的区别是 git 如此出色的原因。没有。仅仅因为你叫你当地的分行行长，我叫我当地的分行行长，并不意味着我们都在同一个分行。当对本地分支和远程分支执行时，重定基础和合并的工作是相同的。加密内容寻址对象存储的使用是实现这一点的关键。同步就像交换只读对象一样简单。

因此，即使团队不一定使用很多遥控器，他们也使用大量的分支，这就是为什么他们需要 Git。至少有 n+1 个分支，其中 n 是人数加上任何功能、生产、开发等。人们创造的分支。在我的团队中，这意味着在过去的六个月里超过了一千个。有些分支是长寿的，有些是短命的。

Github 只是同步变化的一种便捷方式。大多数团队不正确地使用它，允许多个个人写入同一个存储库，而不是克隆和创建拉请求。

仍有很大的改进空间。Git 对于非专家来说不是很有用。很多复杂性与处理合并和 rebases 可能无法按预期工作的许多方式有关。这里的一个限制是合并提交通常只有两个祖先。Git 实际上允许两个以上，但是瓷器不使用这个。已经有了一些替代方法，可以用来跟踪来自多个分支的变更。

Git 正在努力的另一个领域是更大的代码库。linux 内核并不小，但是有一些大公司的例子表明 git 不足以跟踪他们在一个存储库中的所有东西。例如，谷歌这样做(不使用 git)。所以规模是一个问题。那么即使 git 是分布式的，它也不是分片的。这对单个 git 存储库中的内容设置了上限。模型一直是，您需要本地的整个存储库才能使用它。如果你只需要其中的一部分就能工作，那不是很好吗？IPFS 复制了 git 的一些设计，并将内容寻址对象存储在分布式文件系统中。如果有人在此基础上重建 git 不是很好吗？

因此，Git 世界还有很大的改进空间。

这条评论更直接地回答了这个问题:

[![sergiodxa profile image](../Images/4a39bd5981c8dd168e06f1ec075711c1.png) ](/sergiodxa) [ Sergio Daniel Xalambrí ](/sergiodxa) • [<time datetime="2017-10-09T16:22:05Z" class="date-short-year"> Oct 9 '17 </time> • Edited on <time datetime="2017-10-09T22:17:42Z" class="hidden m:inline-block date-no-year">Oct 9</time>](https://dev.to/sergiodxa/comment/128k) 

我认为 Git 足够简单，可以在其上创建工具(比如 Github)来扩展它。

例子:拉请求不是 Git 的特性，但是没有人会使用版本控制平台(Github，Gitlab 等等)。)没有构建在 Git 分支上的特性。

因此，与其从头再做 Git，我认为更好的办法是改进基于 Git 的工具，也许简化流程以避免弄乱它。

[![ben profile image](../Images/aafbcbcc2891483db855a7b6ec39b85c.png) ](/ben) [ Ben Halpern ](/ben) • [<time datetime="2017-10-09T16:35:14Z" class="date-short-year"> Oct 9 '17 </time>](https://dev.to/ben/comment/128p) 

那是合理的。Git 可能有点像版本控制的汇编语言。无论您构建什么抽象，它都会编译成 git 以实现兼容性，并利用投入其中的大量工作。

未找到注释

[![zackphilipps profile image](../Images/976e73a76773806bf90beac172318227.png) ](/zackphilipps) [ Zack Philipps ](/zackphilipps) • [<time datetime="2017-10-10T00:37:35Z" class="date-short-year"> Oct 10 '17 </time>](https://dev.to/zackphilipps/comment/12ca) 

[Git 流](https://jeffkreeftmeijer.com/git-flow/)，有人吗？

我们是否能改进任何一点软件都是值得质疑的，但惊叹于我们使用的一些工具的功能和稳定性也是令人兴奋的。这次讨论让我对 git 有了更高的评价，我希望你喜欢阅读它。*