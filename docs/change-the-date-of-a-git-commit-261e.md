# 更改 git 提交的日期

> 原文：<https://dev.to/hugo__df/change-the-date-of-a-git-commit-261e>

git 最大和最糟糕的事情之一就是你可以重写历史。这里有一个卑鄙的滥用方式，我想不出一个合法的理由这样做。

和其他事情一样，感谢 StackOverflow 为我提供的所有选择👍。

## 将上次提交的日期设置为当前日期

```
GIT_COMMITTER_DATE="$(date)" git commit --amend --no-edit --date "$(date)" 
```

Enter fullscreen mode Exit fullscreen mode

## 将最后一次提交的日期设置为任意日期

```
GIT_COMMITTER_DATE="Mon 20 Aug 2018 20:19:19 BST" git commit --amend --no-edit --date "Mon 20 Aug 2018 20:19:19 BST" 
```

Enter fullscreen mode Exit fullscreen mode

## 将任意提交的日期设置为任意或当前日期

在所述提交和停止修改之前重设基础:

1.  `git rebase <commit-hash>^ -i`
2.  在提交(第一个)的那一行用`e` (edit)替换`pick`
3.  退出编辑器(在 VIM 中，ESC 后跟`:wq`)
4.  要么:
    *   `GIT_COMMITTER_DATE="$(date)" git commit --amend --no-edit --date "$(date)"`
    *   `GIT_COMMITTER_DATE="Mon 20 Aug 2018 20:19:19 BST" git commit --amend --no-edit --date "Mon 20 Aug 2018 20:19:19 BST"`

关于在 git 中重建基础和编辑的更多信息，请参见这里:[分割一个现有的 git 提交](https://dev.to/hugo__df/split-an-existing-git-commit-26d6)。

肖恩·芒格