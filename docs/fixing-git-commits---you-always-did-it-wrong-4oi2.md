# 修复 git 提交——你总是做错

> 原文：<https://dev.to/smichaelsen/fixing-git-commits---you-always-did-it-wrong-4oi2>

> ...除非你用- fixup 和- autosquash 来做

首先最重要的事情:当然你永远不要在你和其他人共享的分支中改变提交，比如`develop`、`master`或者任何你称之为“主”git 分支的分支。如果在那里遇到错误，只需用一个新的提交来修复它。

当你自己处理一个分支时，比如一个特性分支或者本地分支，有一个更好的方法来修复提交。

看看下面的 git 日志:

```
8680a59d This is the 3rd commit
7e866455 This is the 2nd commit
a069e42a This is the 1st commit of my feature branch
0e2977b5 Previous commit that is already integrated in master 
```

Enter fullscreen mode Exit fullscreen mode

假设您想在第二次提交时修复一个错误。

更改文件以解决问题，并将更改添加到阶段，例如使用`git add --all`。

```
git commit --fixup 7e866455 
```

Enter fullscreen mode Exit fullscreen mode

注意，提交散列`7e866455`是来自包含错误的提交的散列。现在您的 git 日志看起来像这样:

```
335ca372 fixup! This is the 2nd commit
8680a59d This is the 3rd commit
7e866455 This is the 2nd commit
a069e42a This is the 1st commit of my feature branch
0e2977b5 Previous commit that is already integrated in master 
```

Enter fullscreen mode Exit fullscreen mode

新提交具有来自第二次提交的提交消息，但是带有前缀`fixup!`。

对于分支中相同或不同的提交，您可以多次这样做。当您的分支准备好被合并(或审查)时，您会想要清理一下。刚刚运行:

```
git rebase -i --autosquash 0e2977b5 
```

Enter fullscreen mode Exit fullscreen mode

提交散列`0e2977b5`是您的特征分支所基于的散列(“先前提交”)。

这将把您的修正提交直接应用到它们所属的提交，并且您将获得一个包含所有已修复错误的干净的 git 日志。

```
26fe362d This is the 3rd commit
5db42a3d This is the 2nd commit
a069e42a This is the 1st commit of my feature branch
0e2977b5 Previous commit that is already integrated in master 
```

Enter fullscreen mode Exit fullscreen mode

✨✨✨