# 我经常遇到的 Git 常见问题

> 原文：<https://dev.to/3sanket3/git-faqs-i-encounter-regularly-433l>

Git 不仅仅是代码的`push` / `pull`。在这篇文章中，我将讨论其他支持命令和技巧来处理我在团队中编码时遇到的一些场景。这些也是我接触的大多数 git 初学者问我的问题。

我假设你已经知道 git 的`push` / `pull`和分支概念。如果你不是，请在评论/DM 中让我知道，我会在上面写一篇文章。

## 1。分支切换迫使我在签出之前提交我对当前分支的更改。但是我还不想提交我的更改。我该怎么办？

如果是这样的话，您可以保存(安全地存放在一边)您当前的更改，并轻松地导航到另一个分支。

```
$  git stash 
```

它不会隐藏新添加的文件。一般来说，新添加的文件在切换到另一个分支时不会引起任何问题。但是，如果您不希望在分支切换后看到新添加的文件，您也可以通过向上面的命令添加`-U`参数来隐藏它们。

```
$ git stash -U 
```

然后，每当您想要回您的更改时，运行:

```
$ git stash pop 
```

## 2。如何删除/放弃所有未提交的更改？

```
$ git reset --hard 
```

删除未被跟踪的文件和目录【2018 年 10 月 26 日添加】

```
$ git clean -d -f 
```

## 3。我不小心在`master`或者一个我不应该在的分支犯了。怎么办？

首先，只需备份您在新分支中提交的内容，这样您就不会丢失您的更改，使用:

```
$ git checkout -b new-branch-name 
```

它将检查您在`master`分支到`new-branch-name`中的所有内容。接下来，我们需要从`master`中移除不必要的提交(因此需要变更)。

您将首先需要查看日志，并获得您希望您的`master`分支返回的 *git 提交散列*。就像*撤销*一样。

```
$ git log
commit be6bf5e001852e57bbc2c4e14054e3e539b6601c (HEAD -> master)
Author: Sanket Patel <3sanket3@gmail.com>
Date:   Wed Sep 12 18:19:24 2018 +0530

    another commit by mistake

commit f7169231dc4b729c97f69a877487cf2ddae54133
Author: Sanket Patel <3sanket3@gmail.com>
Date:   Wed Sep 12 18:18:56 2018 +0530

    commit by mistake

commit 9f6deced44efecb59f6d70283b8d0ba2d0a57550 <-- We want to restore at this point
Author: Sanket Patel <3sanket3@gmail.com>
Date:   Wed Sep 12 18:18:26 2018 +0530

    good commit 
```

> 使用`pretty=oneline`也就是(`git log --pretty=oneline`)你可以去掉额外的细节并在单行中显示每个提交。

假设我们对标题为*好的提交*即`9f6deced44efecb59f6d70283b8d0ba2d0a57550`的提交是好的，并且我们想要移除后来添加的另外两个提交。我们可以通过
做到这一点

```
$ git reset --hard 9f6deced44efecb59f6d70283b8d0ba2d0a57550 
```

它将删除在*本地*中由于错误所做的*的提交和更改。*

## 4。我不小心在远程上推了一些提交。我该如何丢弃它们？

正如上面第 3 条中所解释的，我们应该首先备份并丢弃本地的提交。我们的第一个想法是，不能只是简单地同步本地的放弃操作。不，本地现在比远程落后。如果您将运行`git push`，它将抛出一个错误，并要求首先运行`git pull`。因此，我们将不得不强制要求遥控器接受我们的推送，并放弃遥控器的所有额外更改。

⚠️警告:在强制推送代码之前，应该确保远程分支中没有我们想要保留(而不是丢弃)的提交。如果其他团队成员也在同一个分支机构工作，建议您仅在与他们交流后执行此操作。

```
$ git push origin +my-branch 
```

分支名称的前缀`+`将强制推动您的更改。这也意味着，它将放弃您在本地放弃的提交。

## 5。在将任何分支合并到我的分支之前，我想要检查更改。

```
$ git merge others-branch-name --no-commit --no-ff 
```

这试图将来自`others-branch-name`的更改合并到您的分支，但不会提交。这样您就有机会回顾一下如果合并发生了，您的分支会是什么样子。您可以手动检查并提交合并。

## 6。我想重命名目录

```
$ git mv old-name new-name 
```

如果你想改变现有目录名的大小写，你必须使用临时目录交换它们，如下:

```
$ git mv DirectoryName temp
$ git mv temp directory-name 
```

祝你快乐！:)

校对人: [@ron4ex](https://dev.to/ron4ex)