# 用 Git 摘樱桃

> 原文：<https://dev.to/neshaz/cherry-picking-with-git-4pmj>

管理还没有准备好完全合并的功能分支更改可能是一项困难的任务。有时，您不想将整个分支推到另一个分支，而只需要选择几个特定的提交。这个过程叫做**摘樱桃**。

## 为什么挑樱桃？

让我们看看下面的场景，这将更容易理解**挑选**如何工作。

您正在为下一周的 sprint 实现新特性。一旦代码准备好，你将[把它推到开发分支](https://kolosek.com/git-branches/)并在下一次公开发布时开放，那时一切都已经测试完毕。

但是，客户对所有的变化都不满意，只要求你介绍特定的变化。因为并不是所有的特性都被下一个版本接受，一个`git merge`或者`git rebase`不会产生想要的结果，因为它将包含上一个 sprint 中的所有变更。

> **樱桃挑**才是正确答案！它只关注提交中包含的实现所需特性的更改，而不会带来其他的提交。

此工具还可以提供许多其他用途:

*   当谈到 **[bug 修复](https://kolosek.com/debugging-with-git/)** 时，这是必不可少的，因为 bug 是在开发分支中用它们各自的提交来修复和测试的。
*   您可以通过使用`git cherry-pick`而不是其他选项来避免不必要的**冲突**，这些选项也可以应用来自给定提交的更改，例如 [`git diff`](https://kolosek.com/git-commands-tutorial-part1/) 。
*   每当由于各个分支中的不兼容版本而无法进行完全分支**合并**时，这是一个有用的工具。
*   `git cherry-pick`用于将变更引入到**子分支**，而不改变分支。

## 樱桃怎么挑？

让我们[从开发中挑选](https://git-scm.com/docs/git-cherry-pick)一个提交，并通过使用提交散列将其添加到特性分支。

[![git-cherry-pick](img/8760aaa3f26e4a56fece70d35fe9f865.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kLArrWHH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kolosek.com/conteimg/2018/05/git-cherry-pick.png)

假设您想从主服务器选择一个提交`C`。要完成这项工作，您应该遵循以下几个步骤:

1.  获取提交哈希。有两种方法可以做到这一点:
    *   通过键入`git log --oneline`，获得提交历史的日志。确保你在正确的分支上:`git checkout master`。
    *   从 GitHub 页面选择提交散列。
2.  [检查到您想要插入提交的分支](https://kolosek.com/git-branches/)，在您的情况下，这是特性分支:`git checkout feature`。
3.  精选提交:`git cherry-pick C`。

[![git-cherry-pick-1](img/ace38e7ae083a41880fb778d8f8c7bdf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5lCKUNxi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kolosek.com/conteimg/2018/05/git-cherry-pick-1.png)

如果您运行 [`git log`](https://kolosek.com/git-commands-tutorial-part2/) ，您将会看到您精心挑选的提交位于特性分支的顶部。*它将有一个新的不同的提交散列。*

## 提示和窍门

*   如果您想一次选择多个提交，您可以添加它们的提交散列，用空格分隔:`git cherry-pick C D`。
*   你可以挑选一个[合并](https://kolosek.com/git-merge-vs-rebase/)来代替提交:`git cherry-pick -m 1 <merge_hash>`。*小心！*最好用`git merge`代替`git cherry-pick`。当您挑选一个合并提交时，它会折叠对该提交所做的所有更改。您会丢失提交历史记录。

> 警惕不按顺序挑选大量提交。新的序列将会反映出您挑选的方式，而不是最初提交的时间顺序。

## 结论

开发者社区通常不鼓励摘樱桃。主要原因是它创建了重复的提交，并且您失去了跟踪提交历史的能力。你应该始终以使用`git merge`为目标。

* * *

附近的合并总是压倒樱桃采摘。但是值得看一看这个独特的 Git 特性！分享一下你的经验！

如果你需要在众多开发公司中挑选一家，为什么不选择科洛塞克呢？我保证科洛塞克团队会实现的！

本文最初发表于 [Kolosek 博客](https://kolosek.com/cherry-picking-with-git/?utm_source=dvt)。