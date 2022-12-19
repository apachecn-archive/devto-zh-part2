# Git:如何删除除 master 之外的所有本地分支

> 原文：<https://dev.to/koscheyscrag/git-how-to-delete-all-branches-except-master-2pi0>

结帐到`master`分店。

删除除主分支以外的所有分支的命令是:
`git branch | grep -v "master" | xargs git branch -D`

要在 Windows 中使用相同的命令，我们必须安装[一些实用程序](https://github.com/bmatzelle/gow/releases)

或者，我们可以使用 PowerShell 命令来做上面命令做的事情:
`git branch | %{ $_.Trim() } | ?{ $_ -ne 'master' } | %{ git branch -D $_ }`
或
`git branch -D @(git branch | select-string -NotMatch "master" | Foreach {$_.Line.Trim()})`
[![Alt text of image](../Images/1121c1f55dee8bb29a6e68e8abaf92fa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8epcCWTQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a4ml56n1o8gyycxt50u7.png)