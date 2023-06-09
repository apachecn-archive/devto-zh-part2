# git 学习模式=快速

> 原文：<https://dev.to/pranavkumar389/git-learn---modefast-2bpl>

## 版本控制

版本控制系统或 VCS，也称为修订控制或源代码控制系统，是一个软件实用程序，它由**跟踪**，由**管理**对文件系统的更改。VCS 还提供协作工具，让其他 VCS 用户分享和集成这些文件系统的变化。

> 流行的软件行业 VCS 选项包括 Git、Mercurial、SVN 和 preforce。这里我们将重点介绍`Git`。

## Git vs Github

Git 是**不是** Github。还没信任我吗？

```
if("Git" == "Github"){
console.log("Code lies.");
} else {
coneole.log("Code never lies.");
{
>Code never lies. 
```

Git 允许一群人在同一时间处理相同的文档(通常是代码),并且不会影响到其他人。这是一个[分布式](https://en.wikipedia.org/wiki/Distributed_version_control)版本控制系统。
**Github** 只是一个存储你的相同工作目录的地方——又名存储库，简称 repo。它实际上是 Git 仓库的枢纽。

## 安装 Git

要安装 Git，去[官方网站](https://git-scm.com/download)下载你机器的可执行文件。我不打算讨论安装细节，因为它们实际上只是一系列的步骤。

为了确保您的安装成功，在您的终端/命令提示符下运行以下命令:
`git --version`
您应该得到类似于:
`git version 2.15.1`的内容

## 我们开始吧

首先，做一个空目录。打开终端/命令提示符(Git Bash 将是一个很好的选择),并确保您当前的工作目录是新创建的。
要在这里初始化 Git 存储库，请键入以下命令:

```
git init 
```

> Repository 是一个目录，Git 已经在这个目录中初始化，开始对文件进行版本控制。

注意一个新的隐藏目录`.git`。它里面有各种各样的目录和文件。在这里你很少需要做任何事情，但是它是 Git 的核心，所有的魔法都在这里发生。

接下来，让我们键入`git status`命令来查看我们项目的当前状态:

```
git status 
```

经常跑步有益健康。有时候事情发生了变化，而你却没有注意到。

> 暂存:
> 文件已准备好提交。
> 未转移:
> 包含尚未准备好提交的更改的文件。
> 未跟踪:
> Git 还没有跟踪文件。这通常表示新创建的文件。
> deleted:
> 一个文件已经被删除，正在等待从 Git 中删除。

假设你新建了一个文件，假设它是 demo.txt，Git 会输出“demo.txt 是`untracked`”。
要告诉 Git 开始跟踪对 demo.txt 的更改，我们首先需要使用:
将它添加到**暂存区**

```
git add demo.txt 
```

> Staging Area:
> 在将文件“提交”到 Git 之前，我们可以将它们分组在一起的地方。

让我们再跑一次`git status`看看我们站在哪里。
注意 Git 是如何说变化为`committed`？这里列出的文件在暂存区，它们还不在我们的存储库中。在将文件存储到存储库中之前，我们可以从 stage 中添加或删除文件。

> add all:
> 您也可以键入`git add -A .`，其中点代表当前目录，这样它里面和下面的所有内容都会被添加。-A 确保甚至包括文件删除。
> git reset:
> 您可以使用 git reset 从暂存区删除一个或多个文件。

为了存储我们的阶段性更改，我们运行 commit 命令，并显示一条描述我们所做更改的消息。现在让我们通过键入:
来完成这项工作

```
git commit -m "initial commit" 
```

> 提交“提交”是我们的存储库的快照。这样，如果我们需要回顾我们所做的改变(或者其他人做的)，我们会看到所有改变的一个很好的时间表。

所以我们做出了承诺。现在让我们浏览它们，看看我们做了哪些更改。我们可以通过键入:
来实现这一点

```
git log 
```

> Git 的日志是一个日志，它按照我们提交的顺序，记录了我们到目前为止提交的所有更改。使用`git log --summary`查看每次提交的更多信息。

为自己鼓掌。你做得很好。现在，让我们进入下一个阶段。

## 克隆一个知识库

在你的组织中，你会得到一个链接，看起来像这样:
`https://github.com/9kWorld/demoMain.git`
我们要把这个库放到我们的计算机上。这可以通过键入:
来完成

```
git clone https://github.com/9kWorld/demoMain.git 
```

现在，您有了一个新文件夹`demoMain`。

记住，你不应该向主人承诺，**永远不要**？

## 分支

**分支**是当你想同时处理多个特性时自然发生的事情。你不会想以一个特性 A 已经完成一半而特性 B 已经完成一半的`master`分支结束。
相反，您应该将代码库分成两个“快照”(分支)，并分别处理和提交它们。一旦一个分支准备好了，您就可以将这个分支合并回主分支，并将其推送到远程服务器。

> 主分支是所有其他分支的主要分支(如果存在)。

让我们创建一个名为`demoBranch`的分支，在这里我们将完成所有的工作:

```
git branch demoBranch 
```

太好了！现在，如果您键入`git branch`，您将看到两个本地分支:名为`master`的主分支和名为`demoBranch`的新分支。
您可以使用`git checkout <branch>`命令切换分支。现在试试切换到`demoBranch`分支:

```
git checkout demoBranch 
```

> 尝试`git checkout -b new_branch`
> 同时结帐并创建一个分支。这和做
> `git branch new_branch
> git checkout new_branch`是一回事

改变你必须做的事情，修复错误或者增加一个新功能。
随时运行`git status`来检查您将要提交的更改。

现在，您已经准备好提交您的更改。
`git commit -m "your message here"`

很好，现在您只需要切换回`master`分支，这样您就可以将您的更改从`demoBranch`分支复制(或合并)回`master`分支。
继续检查总分行:

```
git checkout master 
```

好了，现在是时候将 demoBranch 分支中的变更合并到 master 分支中了。深呼吸，没那么可怕。

我们已经在`master`分支中，所以我们只需要告诉 Git 将`merge`分支到`demoBranch`分支:

```
git merge demoBranch 
```

**合并冲突**可能发生在同时对一个文件进行更改的时候。当冲突发生时，很多人会非常害怕，但不要害怕！它们没那么可怕，你只需要决定保留哪个代码。看看[冲突是如何呈现的](https://git-scm.com/docs/git-merge#_how_conflicts_are_presented)。

如果您不再需要某个分支，可以使用`git branch -d <branch name>`删除它。使用`--force(-f)`删除选项删除尚未合并的分支。这也可以通过
`git branch -D wrong_feature`来实现

## 最后一推

我们到了，最后一步。您现在要做的就是将您一直在做的所有事情都推到您的远程存储库中，这样就完成了！

```
git push 
```

最后，我们做到了。

希望这有所帮助！祝你好运。