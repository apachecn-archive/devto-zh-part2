# Git Merge vs Git Rebase

> 原文：<https://dev.to/neshaz/git-merge-vs-git-rebase-5134>

Git merge 和 **rebase** 服务于相同的目的——它们将多个分支合并成一个。虽然最终目标是相同的，但这两种方法实现的方式不同。*使用哪种方法？*

## Merge 或 Rebase 是什么意思？

这里你有一个样本库，它有两个 **[分叉分支](https://kolosek.com/git-branches/)** :主库和特性库。你想把它们融合在一起。让我们看看这些方法是如何解决问题的。

[![git-flow](img/66914fcc0518af5af9196a5e40f3c55e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YZi2YwdW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storage.kraken.io/kk8yWPxzXVfBD3654oMN/fc73a41ce658a6a566e2a54d60534ade/git-flow.png)

### 合并

当您运行`git merge`时，您的 HEAD 分支将生成一个**新提交**，保存每个提交历史的祖先。

[![git-merge-2](img/b07fdfe3e7d6cb7ebb760534c501e5fc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Q2Loe7Tq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storage.kraken.io/kk8yWPxzXVfBD3654oMN/673b91456bdc6fd454c5ad203f825568/git-merge-2.png)

> [快速前进合并](https://kolosek.com/git-merge/#fastforwardmerge)是一种不创建提交的合并，相反，它将分支指针更新到最后一次提交。

### 重置基础

**rebase** 将一个分支的更改重写到另一个*上，而*不会创建新的提交。

对于在[特征分支](https://kolosek.com/git-branches/)上而不是在主分支上的每个**提交**，将在主分支之上创建一个新的提交。看起来好像这些提交一直都是在主分支之上编写的。

[![git-rebase](img/175bda67fe65374f64cb19a7986172f3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0tOmcThs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storage.kraken.io/kk8yWPxzXVfBD3654oMN/5ade4f7276bc6ad18dad4b6078950ac9/git-rebase.png)

## 融合利弊

### 优点:

*   易于使用和理解。
*   维护源分支的原始上下文。
*   源分支上的提交与其他分支提交是分开的。如果你想获得这个特征，然后[将](https://kolosek.com/git-merge/)合并到另一个分支，这会很有用。
*   保留您的提交历史并保持历史图语义正确。

### 缺点:

*   历史可能会被大量的合并提交严重污染，因为许多人正在并行地处理同一个分支。你的库的可视化图表可能会变得一塌糊涂，不要添加太多的信息。*它可以看起来像伦敦地铁地图提交！*

[![london-tube-map-commit](img/16afb651f76b4e66e6e9c7b417284a19.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wrAFvKMC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storage.kraken.io/kk8yWPxzXVfBD3654oMN/c8b97f4dbb5f7d49fc3eb3624eafff79/london-tube-map-commit.png)

*   由于合并提交，使用 git 二分法进行调试会变得更加困难。

## 权衡利弊

### 优点:

*   代码历史是简化的、线性的和可读的。
*   操纵单个提交历史比操纵许多单独的特性分支及其附加提交的历史更容易。
*   干净、清晰的**提交消息**可以更好地跟踪一个 bug 或者一个特性何时被引入。*避免 20+单行提交污染历史！*

### 缺点:

*   将该特性降低到少量的提交可以隐藏上下文。
*   重置基础对**拉请求**不起作用，因为你看不到有人做了什么微小的改变。重写历史不利于团队合作！

    > 与合并时相比，您需要更加小心地重定基础。

*   在处理冲突时需要更多的工作。使用 rebase 来保持要素分支的更新需要您一次又一次地解决类似的冲突。而合并，一旦你解决了冲突，你就准备好了。*你必须按照冲突产生的顺序解决冲突，才能继续重建基础。*

## 选择哪种方法？

当您的团队使用基于功能的工作流或不熟悉 rebase 时，那么`git merge`是您的正确选择:

*   它允许您保留任何给定特性的提交历史，而不必担心覆盖提交和更改历史。它帮助你避免不必要的 [git 回复或重置](https://kolosek.com/git-reset-revert-and-checkout/)！
*   不同的特性保持隔离，不会干扰现有的提交历史。
*   可以帮助您重新整合已完成的功能分支。

另一方面，如果你更看重一个干净的、线性的历史，那么`git rebase`可能是最合适的。您将避免不必要的提交，并保持变更更加集中和线性！

如果你不正确地重新设定基数，无意中重写历史，会导致严重的问题，所以请确保你知道你在做什么！

* * *

Rebase 或 merge，你更喜欢哪一个？Kolosek 团队更倾向于合并我们基于特征的工作流。

本文原载于 [Kolosek 博客](https://kolosek.com/git-merge-vs-rebase/?utm_source=dvt)。