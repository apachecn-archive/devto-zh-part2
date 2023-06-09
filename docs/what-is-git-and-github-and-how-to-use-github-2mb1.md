# 什么是 Git 和 GitHub？以及如何使用 GitHub？

> 原文：<https://dev.to/ravirajthedeveloper/what-is-git-and-github-and-how-to-use-github-2mb1>

2018 年 7 月

最初发布于[媒体](https://medium.com/@theraviraj/what-is-git-and-github-and-how-to-use-github-f49d6be08b25)

你有没有想过大项目是如何由大人物完成的，人们是如何快速交付的？这个问题的答案已经在这篇文章中描述过了。例如，他们使用“Git”或类似的东西作为版本控制系统来管理开发和项目发布。在这里，我们将看到为什么以及如何使用它。

## 目录:

*   饭桶
*   开源代码库
*   Git 的安装
*   设置 GitHub
*   项目设置
*   分支
*   Git 命令

这篇文章对谁有用？

*   任何种类的软件程序员
*   Git 和 GitHub 的新手
*   计算机科学或类似学位的学生

## [走吧:](#git)

Git 基本上是一个版本控制系统(又名 VCS)。有许多版本控制系统工具可供使用。我们将选择 GitHub，这是一家我们熟悉的云服务提供商，可以满足您所有的 VCS 需求。

## 关于 GitHub:

这是一个很棒的 git 工具，有很好的用户界面，而且非常容易使用。尽管它免费向开源/公共项目提供服务，但它对私人项目有一些定价。

你没看微软收购 GitHub 的最新消息吗？是的，GitHub 现在是其中一个大玩家的产品。[了解更多信息](https://blogs.microsoft.com/blog/2018/06/04/microsoft-github-empowering-developers/)。

[![Microsoft acquired GitHub](img/241f330ab1dcf1ddffa28827335f4c5e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IzOHVXul--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o899hg66zx6yhrpz0nfm.jpg)

为什么应该使用版本控制系统？

因为任何人都不应该为命名他们的项目文件夹而烦恼，

项目(1)、项目-副本、项目-新建、项目-最终、项目-最终(1)、项目-最终-最终等。,

如果你还喜欢那样。这篇文章可能会让你失望。😐

Git 如何对您和/或您的组织有用？

您可以以前所未有的方式组织您的项目文件，也可以同时与多人一起处理一个项目。

# 不信我？

Linux 项目使用 git，成千上万的人同时使用它，他们每月发布两次版本。

好了。我从哪里开始？

> 你不能循规蹈矩地学习走路。你通过实践和跌倒来学习。
> 
> *   理查德·布兰森

让我们边做边学。

## 安装 Git:

如果您的计算机上已经安装了 Git。很好，你可以继续下一部分。

如果没有，你可以从[这里](https://git-scm.com/downloads)下载

如果您需要安装方面的帮助，请在此处找到它[。](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)

## 调教 GitHub:

[点击这里](https://github.com/)用你的邮箱在 GitHub 上注册如果你已经有了，你可以使用它。登录您的帐户。

不管您的本地机器/计算机上是否已经有了一个项目，您都必须在 GitHub 中创建新的资源库。存储库只意味着一个项目。给它起个好听的名字。记住，这是你的第一个项目！按你喜欢的方式命名。😉

现在该使用黑客的工具了，*终端*或*命令提示符*。

[![Picture of Computer hacking](img/c99370ef0645918e6eba9d04eac7313e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7JJaL1uP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media1.tenor.cimg/093b967de4114d379d5860ff0c335c2a/tenor.gif%3Fitemid%3D7506285)

## 项目设置:

打开终端。

如果您已经有一个项目，请将目录更改为您的项目的目录，或者创建一个新项目并移入新的项目目录。

如果您不熟悉终端，请使用以下链接。

点击[此处](https://en.wikipedia.org/wiki/List_of_DOS_commands)和[此处](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/windows-commands)为 Windows

[点击此处](https://en.wikipedia.org/wiki/List_of_Unix_commands)用于 MacOS 或 Linux

一旦你在 GitHub 中创建了一个存储库，它的主页将会是这样的，

[![Linux repository in GitHub](img/4380c8c630fcb45f71c632e290041c38.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--thr0CGsN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f4n02rv0ozkgm3igmbs0.png)

点击**克隆或下载**按钮，可以看到链接。这是 Git 的 URL。复制它。你一会儿会用到它。

如果你还想建立一个仓库。[查看此处](https://help.github.com/articles/set-up-git/)了解更多说明。

回到您的终端，确保您在您的项目目录中。

输入“`git init`”然后回车，会添加一些文件，保存一些元数据，用于跟踪你的文件变化和其他一些必要的东西

当我把一些东西放在两个像这样的括号里时，你需要在里面填入你的输入。

现在输入"`git remote add origin <LINK_OF_GitHub_REPOSITORY_THAT_YOU_JUST_COPIED>`"，点击回车，它已经为你的资源库保存了链接名称为" origin "

如果它提示输入用户名和/或密码，请输入您用来创建帐户的 GitHub 凭据。

如果项目中没有任何文件，您可以添加一些文件并对项目进行相关更改。

> 注意:你的项目文件将被公开推送到 GitHub，所以请确保你没有在项目中的任何地方输入任何秘密信息，如密码、API 令牌、电话号码、信用卡信息等。,

在 Git 世界中，存储库类似于树的结构。如果你要对它做任何改变，你要在它的一个分支上做。因为你是第一次推动，你可以推动到它的主要分支，这被广泛称为“主人”。而其他人将在不同的分支机构进行他们的更改，并需要做一些手续将其与主分支机构合并。我们一会儿就会看到。

让我们创建一个分支，

键入“`git checkout -b master`”，它会以“主人”的名义创建一个分支

稍后，您将创建不同名称的分支，因为它在您的项目中应该是唯一的。

您刚刚创建了一个分支，但是您的文件并没有添加到这个分支中，也没有保存在那些“git 元数据文件夹”中，以便跟踪您的进度。

让我们添加您的文件，并说 git 您希望这些文件被跟踪。

键入“`git add .`”(不要错过末尾的点)，它会将所有文件添加到跟踪列表中。

现在，您需要将所有的更改保存到 git 中，这样 git 会将它放在本地 bank 中，并为它分配一个惟一的 hash。这被称为提交。

键入“`git commit -m “initial commit”`”，它将保存您的更改或提交到具有唯一哈希的元数据文件夹。

到目前为止，更改仅保存在您的本地计算机上。我们需要把这个推到云端，这样你就可以从任何一台机器或者你自己的机器上下载，即使你把它弄丢了。

键入“`git push origin master`”，它会将您的提交(您保存的更改/文件)推送到您的云中的存储库。

就这样，你的代码现在在云端了。

[![Picture of cloud](img/e989b17a572b694cdb4d3f0b8dd48e08.png)T2】](https://i.giphy.com/media/HgycnYQCMeJXO/giphy.gif)

转到 GitHub 的存储库页面，你可以在那里看到你所有的文件修改。

那么你将如何继续你的工作。

您可以随时在您最喜欢的文本编辑器中进行任何更改，并将代码推送到同一个主分支，这样您的代码就会进入云中的存储库。但是，聪明人不会这么做。

被太多要学的东西压垮了？别担心，你会习惯的。

> 今天很残酷。明天更残酷。
> 而后天是美好的。
> 
> *   马云

让我们再走一步，

现在假设你的项目中有一个小问题，你将推动一个小的改变或补丁。这可能会解决问题。你通常所做的只是做必要的修改，并把它保存在一个新的文件夹中。我们将用分支的方法来处理同样的概念。

## 分支:

分支无非是在多个文件中保存有更改的副本。这听起来就像古老的方法，不同的版本有不同的文件夹。但是，它有很多优点。

我们来做一个样本了解一下。

坚持你的假设，有一个小的补丁，你需要把它推到云，这样你的团队成员就可以得到你的工作副本。

回到您的终端，确保您在您的项目目录中。键入“`git status`”，它会显示一些关于您所在的分支或您最后一次提交的信息，以及您在云中更新后更改的所有文件。

它会说这样的话，

```
“On branch master

Your branch is up to date with ‘origin/master’.

nothing to commit, working tree clean” 
```

意思是在更新到云库之后，你从来没有做过任何改动。

是时候创建一个分支了，*终于*。

键入“`git checkout -b new-branch`”，它将创建一个名为“new-branch”的分支，并签出相同的分支。

什么是结帐？不要担心，git 在终端窗口中一次只能关注一个分支。所以你需要告诉哪一个分支要集中。就是这样。

> 注意:在上面的命令中缺少“-b”将会抛出一个错误，

```
pathspec ‘new-branch’ did not match any file(s) known to git. 
```

上面的错误表明它找不到任何名为“new-branch”的分支。因为它将搜索任何现有的分支，如果存在就签出，如果不存在就抛出错误。

我希望，您键入的前一个命令是正确的。

现在对项目中的文件进行更改。

即使你真的不知道要改变什么，只是学习这个过程。只需通过添加一些空行来更改任何文件，并进一步移动。

现在点击，“`git status`”

您可以看到如下所示的内容，以及一些用红色表示的已更改的文件名，

在分支 new-branch
上未准备提交的更改:

已修改:

这意味着，在使用云存储库更新项目之后，提到的文件内容已经被更改。在第一行中，您可以看到您在刚刚创建的新分支中。

现在，我们需要将所有更改提交到这个分支，并将其推送到云中。

键入“`git commit -m “my second commit”`”，它会将您的更改提交到“new-branch”，即您刚刚创建的分支。

点击“`git push origin new-branch`”，这将把您的新分支推到您的云存储库中。

在上面的命令中，“origin”是指我们为您的云存储库 URL 分配的别名。所以它知道你的分支应该上传到哪里。“new-branch”意味着您要求 git 将本地创建的分支“new-branch”推送到您提到的存储库。

现在转到浏览器并查看您的云存储库页面，在显示所有文件的主页中，左侧会有一个下拉选项来选择分支。它类似于你的终端。该页面一次只能显示来自一个分支的文件。所以你要选择展示哪一个。在那里，您可以找到您新创建的分支名称“新分支”显示。

单击它将显示来自该特定分支的文件。去看看你修改过的文件。这就像你机器里的一个新文件夹。

瞧吧！你已经学习了其中一个复杂的概念，分支。每当您需要做出新的更改并推进到云时，您都必须遵循相同的步骤。

> 现在把你分支机构的网址给你的朋友或队友，像老板一样说“你要的代码在这里”。👓

## Git 命令:

您可以看到，我们在终端中使用了许多 git 命令。我们将在这里再次集体见到他们，以便你们能够记住。

### 对项目进行设置:

"`git clone <CLOUD_REPOSITORY_CLONE_URL>` " —为已创建的云存储库创建项目目录

### 为已有项目:

"`git init` " —创建 git 的所有隐藏文件，以跟踪我们的文件更改等

"`git remote add origin <CLOUD_REPOSITORY_CLONE_URL>` " —将云储存库 Git URL 添加到 Git 的内存中，这样我们以后就可以用“origin”这个名字来调用它了

### 状态:

"`git status` " —检查当前分支和所有修改过的文件

### 分支:

"`git checkout -b <NEW_BRANCH_NAME>` " —用您的唯一名称创建一个新分支

### 切换到已有的分支:

"`git checkout <EXISTING_BRANCH_NAME>` " —让 git 专注于特定的分支(已经存在的分支)

### 推送:

"`git add .` " —将所有文件添加到 git 跟踪列表中

"`git commit -m “<YOUR_COMMIT_MESSAGE>”` " —将所有文件更改保存到 git 内存中(简称为提交)

"`git push origin <YOUR_BRANCH_NAME>` " —将分支中的所有提交推送到具有相同分支名称的云储存库中

我告诉过你分支比古老的方法有很多优势。我们将在下一部分看到所有这些，以及更多用于组织的内容。

你在这篇文章中学到了什么吗？或者你对这篇文章中的例子有任何疑问？评论框都是你的了。😉

*编码快乐！*😈

Raviraj Subramanian

在 [GitHub](https://github.com/ravirajthedeveloper) 和 [Twitter](https://twitter.com/thisisraviraj) 上找到我