# Git:最常用的 git 命令

> 原文：<https://dev.to/flaviabastos/git-most-used-git-commands-4poo>

下面是我最常用的 git 命令列表:

**git status**

何时使用:了解您的分支中文件的状态。它将显示哪些文件被修改、添加、删除、提交等。您的分支机构当前状况的快照。它超级安全，因为它不会改变任何事情。它只是给你…地位。我每时每刻都得到状态。

如何使用:

```
git status 
```

**去添加**

何时使用:写完一些代码后，你必须准备好它(准备提交)

如何使用:

```
git add . 
```

或者

```
git add -A 
```

或者

```
git add path/to/my/file 
```

前两个选项将暂存您更改的所有内容(git-speak 中的“modified”或“M”)。使用点(。)将存放您的分支已经知道的所有修改过的文件(之前已经添加和提交的)，选项“-A”将存放所有修改，包括您刚刚添加的新文件或您删除的文件(在 git 中称为“未跟踪”或“U”和“已删除”或“D”)。我通常暂存我正在处理的所有东西，但是也可以通过将文件路径传递给命令来暂存单个文件: *git add path/to/my/file*

**去委员会**

何时使用:创建提交并添加提交消息

如何使用:

```
git commit -m "add my commit message here" 
```

或者

```
git commit --amend. 
```

“- -amend”选项将允许您将分阶段的更改提交到以前的提交，还允许您编辑提交消息。它将在操作系统中配置的文本编辑器中打开提交消息。

**去推**

何时使用:将暂存的和提交的变更发送到远程存储库

如何使用:

```
git push remote-name local-branch-name 
```

这些肯定是前 4 个，因为它们是开发周期的一部分:你写一些代码，你检查文件的状态，你登台它(git add)，你提交它，你把它推到你的远程 repo。

然后我也用:

**git checkout**

何时使用它:访问特定分支中的代码，或者恢复尚未被登台(“添加”)的变更。

如何使用:

```
git checkout branch-name 
```

访问特定分支或

```
git checkout file_name 
```

放弃对*文件名*的更改。

**去分支**

何时使用:列出所有分支

如何使用:

```
git branch 
```

或者

```
git branch -a 
```

第一个选项将列出所有本地分支，“-a”将列出所有远程分支

**git branch -d**

何时使用:删除本地分支

如何使用:

```
git branch -d branch-name 
```

或者

```
git branch -D branch-name 
```

如果该分支中的更改没有被合并，请使用“-D”选项(一个例子是当您创建了一个分支来尝试一些东西，但是您不想提交它)。小心这里，没有办法(据我所知)从这些恢复。

**git pull**

何时使用:当您想要用来自远程存储库的最新变更来更新本地存储库中本地分支的内容时。它是两个 git 命令(fetch 和 merge)的组合。

如何使用:

```
git pull 
```

**去重置**

何时使用:还原阶段化的变更(您已经“添加”但尚未提交的变更)

如何使用:

```
git reset -- file_name.txt 
```

以下内容不常用，但了解这些内容很有帮助:

**git stash**

何时使用:当您想要保存当前分支中的更改，但是还不想将它们存放起来时。

如何使用:

```
git stash 
```

或者

```
git stash save -u “stash message” 
```

和

```
git stash list 
```

和

```
git stash pop 
```

第二种选择是将更改隐藏在未被跟踪的文件中，并保存一条消息——以便以后更容易检索。“*存储列表*将列出所有保存的存储，而“*存储弹出*将从存储中移除更改，并将其应用到当前分支。

**git 远程添加**

何时使用:在本地添加远程引用。例如:您在 GitHub 中派生了一个 repo，然后您想将您的 fork 添加为本地 repo 的远程引用(这样您就可以将您的更改推送到 GitHub 中的远程 repo)。

如何使用:

```
git remote add remote_name remote_git_reference 
```

示例:

```
git remote add origin git@github.com:your-github-name/react.git 
```

有时当使用这个命令时，你可能会得到一个错误，比如:

```
message: fatal: remote origin already exists. 
```

您可以通过列出该项目的现有远程引用来解决这个问题:

```
git remote -v 
```

它将显示:

```
origin https://github.com/facebook/react (fetch) origin https://github.com/facebook/react (push) 
```

在这个特定的例子中，它表明“origin”已经是原始项目的远程名称(在 react 的例子中，它是 facebook 的)。要解决这个问题，请移除该遥控器，添加您的遥控器，然后将该遥控器推送到您的 GitHub:

```
git remote rm origin

git remote add origin git@github.com:your-github-name/react.git

git push -u origin master 
```

这只是我个人最常用的命令的概述。关于这些命令和 git 如何工作的更深入的解释，请查看 [Atlassian 的 git 文档](https://www.atlassian.com/git/tutorials/learn-git-with-bitbucket-cloud)(这是一个更容易的参考)或 [git 的官方文档](https://www.git-scm.com/docs)(有点枯燥)。

> 帖子 [Git:最常用的 Git 命令](https://wp.me/pa0b0y-w)最初发表在 [flaviabastos.ca](https://flaviabastos.ca) 上