# 拆分现有 git 提交

> 原文：<https://dev.to/hugo__df/split-an-existing-git-commit-26d6>

与其他版本控制系统相比，`git`的主要区别之一是它允许用户重写历史。这样做的主要方法是使用`git rebase`，通常后跟一个`git push` `--` `force`用本地历史覆盖历史遥控器。

下面看看如何使用`rebase`、`reset`和`commit`来分割现有的提交。

假设您在一次提交中编辑了两个文件(A 和 B ),您希望将其中一个文件(A)的更改放入当前分支，而不是另一个文件(B)。

使用`git cherry-pick <commit-hash>`不是一个选项，因为它会同时引入 A 和 b 的变更。

解决方案是将提交分成两部分，只挑选包含 a 的更改的新提交。

为此:

*   运行`git rebase -i <commit-hash>~`(注意`~`)或`git rebase -i <hash-of-previous-commit>`
*   在 rebase 编辑屏幕中找到您想要分割的提交，将`pick`更改为`e` ( `edit`)
*   保存并退出(`ESC`后接`:wq`关闭 VIM)
*   `git reset HEAD~`重置阶段性变更
*   `git add [files-to-add]`我们想要添加到第一次提交中的所有文件(这里是`git add A`
*   `git commit`通常，用留言等方式
*   根据需要运行任意多轮的提交:
    *   `git add [other-files-to-add]`
    *   `git commit`
*   `git rebase` `--` `continue`表示拆分已经完成，继续重置基础

最后，我们可以`git cherry-pick <new-commit-hash>`让变更进入我们的分支

关于使用 git 的任何问题，欢迎在下面评论或发推特给我 [@hugo__df](https://twitter.com/hugo__df) 。

[订阅](https://buttondown.email/hugo)在你的收件箱里获得最新的帖子(比任何人都要早)。

由 [Markus Spiske](https://unsplash.com/photos/qcCHZLdsS80?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/search/photos/axe-split-wood?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的封面照片