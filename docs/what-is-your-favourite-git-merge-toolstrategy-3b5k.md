# 你最喜欢的 git 合并工具/策略是什么？

> 原文：<https://dev.to/flexdinesh/what-is-your-favourite-git-merge-toolstrategy-3b5k>

VSCode 是我最喜欢的编辑器。但是在重新构建和解决合并冲突时，我切换到 Atom，因为他们的 git 集成对于解决 IMO 冲突来说太好了。只是想知道开发人员通常使用什么其他工具/策略来解决合并冲突。

这是我通常的流程

1.  转到终端和`git rebase master`
2.  当冲突发生时，我打开 Atom，解决冲突并存放文件
3.  我回到终点站`git rebase --continue`