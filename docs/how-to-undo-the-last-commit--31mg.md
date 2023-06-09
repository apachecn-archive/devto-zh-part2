# Git 还原推送提交如何撤销上一次提交

> 原文：<https://dev.to/isabelcmdcosta/how-to-undo-the-last-commit--31mg>

在这篇文章中，我将展示如何在命令行中使用 git 来恢复编码项目中的错误变更(提交)。

### 我为什么要这么做？

在我的论文中，我正在做一个项目，我在一个环境中开发，然后在另一个由多个虚拟机组成的环境中测试。所以我做的每一个重要的改变都可能对项目的功能产生重大影响。有时候，我做的改变可能不会有我期望的结果。然后我必须看到变更，并分析项目在最后一次提交前后的行为。

### 怎么看最后一次提交？

要测试特定的提交，您需要散列。要获得散列，您可以运行`git log`，然后您会得到这个输出:

```
root@debian:/home/debian/test-project# git log
commit <last commit hash>
Author: Isabel Costa <example@email.com>
Date:   Sun Feb 4 21:57:40 2018 +0000

<commit message>

commit <before last commit hash>
Author: Isabel Costa <example@email.com>
Date:   Sun Feb 4 21:42:26 2018 +0000

<commit message>

(...) 
```

您也可以运行`git log --oneline`来简化输出:

```
root@debian:/home/debian/test-project# git log --oneline
<last commit hash> <commit message>
cdb76bf Added another feature
d425161 Added one feature

(...) 
```

要测试您认为具有最新工作版本的特定提交(例如:`<before last commit hash>`)，您可以键入以下内容:

```
git checkout <commit hash> 
```

这将使工作存储库与这个确切提交的状态相匹配。

这样做之后，您将得到以下输出:

```
root@debian:/home/debian/test-project# git checkout <commit hash>
Note: checking out '<commit hash>'.

You are in 'detached HEAD' state. You can look around, make experimental changes
and commit them, and you can discard any commits you make in this state without
impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may do so
(now or later) by using -b with the checkout command again. Example:

git checkout -b new_branch_name

HEAD is now at <commit hash>... <commit message> 
```

在分析了特定的提交之后，如果您决定停留在该提交状态，您可以撤销上一次提交。

### 如何撤销这个提交？

如果您希望[撤销/恢复最后一次提交](https://git-scm.com/docs/git-revert)，您可以使用从`git log`命令获得的提交散列来执行以下操作:

```
git revert <commit hash> 
```

该命令将创建一个新的 commit，在消息的开头带有“Revert”一词。在这之后，如果您检查您的存储库状态，您会注意到在您之前测试的提交中，您已经分离了头部。

```
root@debian:/home/debian/test-project# git status
HEAD detached at 69d885e

(...) 
```

[你不希望看到这个消息](https://www.git-tower.com/learn/git/faq/detached-head-when-checkout-commit)，所以要修复这个问题并把这个头附加回你的工作库，你应该签出你正在工作的分支:

```
git checkout <current branch> 
```

在这个帖子的制作过程中，我发现了这个教程 Atlassian 的[撤销提交和更改](https://www.atlassian.com/git/tutorials/undoing-changes)——很好地描述了这个问题。

### 总结

*   如果你想测试之前的提交，只需做`git checkout <test commit hash>`；然后，您可以测试项目的最后一个工作版本。

*   如果你想恢复最后一次提交，只需做`git revert <unwanted commit hash>`；然后，您可以推送这个新的提交，这会撤销您之前的提交。

*   要固定分离的头部，请做`git checkout <current branch>`。

* * *

你可以在 [Twitter](https://twitter.com/isabelcmdcosta) 、 [LinkedIn](https://www.linkedin.com/in/isabelcmdcosta) 、 [Github](https://github.com/isabelcosta) 、 [Medium](https://medium.com/@isabelcmdcosta) 和[我的个人网站](https://isabelcosta.github.io)上找到我。