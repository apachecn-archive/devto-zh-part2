# 如何组织你的 git 分支

> 原文：<https://dev.to/hardkoded/how-to-organize-your-git-branches-4dci>

我记得在我的团队中实现了 git。我觉得是 6 年多前的事了。在那个时候，如果你想学习如何有效地使用 git，必须阅读由 [Vincent Driessen](https://twitter.com/nvie) 撰写的成功的 git 分支模型。从那以后，我一直遵循“Git 流”的风格。但是，在过去的一年里，我受到了来自社区的许多开发人员的影响，并开始根据我所从事的项目以不同的方式使用 git。

在进入不同的分支风格之前，重要的是要记住，尽管许多 git 客户端将斜杠(“/”)作为目录分隔符，但是在 git 规范中没有文件夹或目录这样的东西。你会看到很多 UI 客户端都实现了这个，但是我从来没有在控制台客户端，或者 Github 或者 GitLab 这样的网站上发现过。

也就是说，让我们探索一些组织分支的方法，这样你就不会迷失在代码的海洋中。

# Gitflow

虽然 Gitflow 没有提到分支文件夹，但是很多 dev 都使用“特性分支”、“热修复分支”和“发布分支”，并相应地创建文件夹。
所以基本上，一个 GitFlow 组织有三个文件夹:

*   特征
*   修补程序/修复
*   释放

由于没有公开的文件讨论这个问题，我看到一些工作副本使用这些文件夹的复数形式，其他的使用单数形式。

[![](../Images/311e885075976b9d17bec648a7bfc220.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zSTlxO3C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/kblok/kblok.github.io/master/img/git-branches/gitflow.png)

# 类固醇 Gitflow

我发现自己又在我的 Gitflow repos 中添加了两个文件夹:

*   单据:针对与降价或发布单据相关的分支机构。
*   任务:对于既不是特性也不是修复的分支。例如，清理脚本或重构。

# BPMP(分支-推送-合并-修剪)

我在很多项目中都见过这种情况。分支-推送-合并-修剪是反文件夹方法。我不是说混乱是组织分支的一种方式。使用这种方法的人喜欢清理他们的工作副本。他们只是分支、推送、创建拉取请求，然后在 PR 合并后立即删除分支(手动或通过 git fetch prune)。

[![](../Images/a9b7d26a60b71a34db069a6b6de74504.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WsdIoi0e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/kblok/kblok.github.io/master/img/git-branches/bpmp.png)

# 基于模块

我发现自己在一个大项目中使用我称之为“基于模块”的分支模型，在这个项目中，我迷失在功能和修复的海洋中。所以我开始根据它的模块创建分支。

[![](../Images/42f501bbab763b34e9bfee1814a26d33.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GvozHRtc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/kblok/kblok.github.io/master/img/git-branches/module-based.png)

您可以将 Gitflow 与这种基于模块的方法混合使用，类似于“后勤/计费/修复/计费值”，但这可能太多了。

# 基于版本

我第一次看到 [Meir](https://twitter.com/MeirBlachman) 使用这种方法，我很喜欢。这对于像[木偶师-夏普](https://github.com/kblok/puppeteer-sharp)这样路线图清晰的项目来说很棒。
在基于版本的回购协议中，你在一个“vX”中创建每个分支。x”文件夹。它的酷之处在于它是基于时间的，所以更容易找到分支，而且用这个简单的 git 命令删除旧版本也非常容易:

`git branch | grep -e "vX.X/" | xargs git branch -D`

[![](../Images/ec4e32200d1fe0a7861a93b2f4c0f296.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1EvoAWh1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/kblok/kblok.github.io/master/img/git-branches/version-based.png)

同样，这可以与 Gitflow 文件夹混合使用，但是...

# 票制

如果门票号码(门票，问题或任何你称之为他们)是你的团队的语言的一部分，使用基于门票的系统可能是一个完美的适合。你可以使用一个文件夹，比如“tickets/242”或“issues/242”，或者就叫它“242”。

[![](../Images/efa0e4d053ddf14932599acb099d934d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iephL5do--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/kblok/kblok.github.io/master/img/git-branches/tickets-based.png)

# 基于表情符号

如果这些系统都不适合你，你可以按照尼克的想法实现一个基于表情符号的系统:)

[![](../Images/f803e7f57ff92fecd75f69ffd416bcb4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cZ58uzEJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/kblok/kblok.github.io/master/img/git-branches/emoji-based.jpg)

# 最后的话

结束这篇文章的最后两个想法。首先，如果你在一个团队中工作，你通常会检查彼此的分支，例如本地测试，我建议你与你的团队分享这种风格。最后，经常清理你的工作副本。这将帮助您快速找到您的分支机构，并加快您的本地回购。

不要停止编码！

最初发布于[harkoded.com](http://www.hardkoded.com/blog/how-to-organize-your-git-branches)