# 你们是如何在本地开发环境中修补其他人的 PRs 的？

> 原文：<https://dev.to/rick_viscomi/how-do-yall-patch-other-peoples-prs-in-your-local-dev-env-4fk3>

场景:您被分配审查一个拉取请求。它可能有副作用，所以您想在本地构建它，看看它是否能自己运行。你怎么做到的？

我是这样做的:

*   从我的本地 git 客户端，我运行`git remote add <user> <git-url>`。这就创建了一个新的远程(如“原点”或“上游”)对应的 PR 作者的分叉。
*   我创建了一个分支，通常是“主”分支。
*   我从远程分支`git pull <user> <branch>`引入变更，其中`branch`对应于 PR 中显示的任何内容。

现在，我的客户应该同步到公关的内容，我们就出发了。

我不喜欢那种工作流程。我能借你的吗？在这种情况下，其他人会怎么做？

我听说过将`.patch`附加到 PR URLs 来获得补丁文件，但不太确定如何简化它。救命啊！