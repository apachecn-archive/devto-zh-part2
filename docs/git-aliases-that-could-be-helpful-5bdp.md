# Git [Script]可能有用的别名

> 原文：<https://dev.to/sarathsantoshdamaraju/git-aliases-that-could-be-helpful-5bdp>

使用终端是程序员做的最好的事情之一。这让我们觉得自己就像一个四处玩耍的小黑客(至少对我来说是这样；d)用他舒适的工具。我们编写小工具(如果可能的话)来自动化小任务或减少键入很长命令的痛苦。

Git 有一个很棒的特性，可以为命令编写自己的 [**别名**](https://git-scm.com/book/en/v2/Git-Basics-Git-Aliases) 。我非常喜欢它，并写了一套我每天使用的别名(我称它们为**短代码**)，我想分享它们，希望对其他人有用。

## 从这里开始..

这些是我每天使用的化名列表。

```
alias g!='git init' 
alias g.='git add .'
alias g.-file='git add'
alias gb='git branch'
alias gb-new='git checkout -b'
alias gblame='git blame'
alias gcl='git clone'
alias gc='git commit -m'
alias gcout='git checkout'
alias gd='git diff'
alias gf='git fetch'
alias gl='git log'
alias gph='git push'
alias gph-f='git push -f'
alias gpl='git pull'
alias gr='git remote'
alias gr-list='git remote -v'
alias gr-add='git remote add'
alias greset='git reset --hard'
alias grevert-head='git revert HEAD'
alias grevert='git revert'
alias gs='git status'
alias gsh='git stash'
alias gsh-a='git stash apply'
alias gsh-c='git stash clear'
alias gsh-d='git stash drop'
alias gsh-l='git stash list'
alias gsh-p='git stash pop' 
```

如果你觉得它们有帮助，你可以用上面的列表创建你自己的别名，或者我已经创建了一个 [git repo](https://github.com/SarathSantoshDamaraju/lazyGit) ，里面有安装和使用它们的详细说明。

请确保重新打开您的终端以查看更改。

更新:我建议初学者在使用任何 git 工具/别名之前学习并理解实际的命令。

* * *