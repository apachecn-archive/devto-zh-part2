# 我的 Git 命令行工具箱

> 原文：<https://dev.to/andrewcahill/my-git-command-line-toolbelt-889>

**概述**

最近我对 Git 命令行越来越熟悉了，我不得不说我很喜欢它。我之所以一直远离它，是因为我发现 GUI(在我的例子中是 SourceTree)对于我不太熟悉的东西来说是一种安全/简单的交互媒介。然而，我意识到 CLI 发出命令更快，所以我有意识地努力开始熟悉它。

这篇文章的目的是演示和解释我通常使用的最常用的命令，希望能帮助其他对理解 Git CLI 基础感兴趣但可能有点担心的人。

我将从存储库所有者和存储库贡献者的角度演示操作。

免责声明:我知道我排除了更多的命令，但是我只是想把重点放在我认为初学者最常用的日常命令上。

我不仅想列出命令和它们的解释，还想提供一个真实的例子来说明更好的上下文。

要了解这些示例，您需要安装一些先决条件。

**先决条件**

*   Git 安装在你的机器上——你可以从这里下载:[https://git-scm.com/downloads](https://git-scm.com/downloads)
*   你有。Net Core SDK(因为我将在一个示例中演示这些命令。Net 核心应用程序——如果你没有，你可以浏览:[https://www.microsoft.com/net/download](https://www.microsoft.com/net/download)

**我用的是什么(如果你对图片感到疑惑的话)**

*   posh Git——漂亮的 PowerShell 模块，允许我从提示符([https://github.com/dahlbyk/posh-git](https://github.com/dahlbyk/posh-git))看到 git 状态
*   通过命令行的 PowerShell–在黑色背景中更容易看到命令颜色

**我将讲述的内容**

我将列出这些命令及其解释，然后将它们应用到一个示例应用程序中，我将使用。网芯。

除了使用编辑器更新文件之外，从创建应用程序到与 Git 交互，我将 100%在命令行中。

的。我将创建的. Net 核心应用程序将是一个简单的控制台应用程序，尽可能保持简洁，并专注于 Git 而不是应用程序本身。

**我将使用的命令:**

*   Git Config 这个命令用于设置您的初始一次性配置，我将使用它来设置我的身份(姓名和电子邮件)。注意:您可以设置全局或项目特定的配置，即配置生效的范围。我会把它应用到新项目中。

如果你想设置全局配置，只需添加'—global '开关。

*   Git Init

    这将我们的新项目目录初始化为一个 Git 存储库。

*   Git Add

    这个命令将你的文件从你的工作目录添加到 staging 或者 Git 索引中。

*   Git Status

    这允许我们查看已经更新、添加或删除的文件。

*   Git Commit

您添加了-m 开关，以便在您的提交中附加一条消息。

*提示:您可以绕过上面的 add 命令，通过添加'-am'* 开关->将 add 命令与 commit 命令合并为一个命令，我将在下面演示。

*   Git Push

    将提交的更改推送到您的远程存储库(在我们的例子中是 GitHub)

*   Git Checkout

*   Git Diff

    查找文件在更改前后的差异。

*   Git Merge

    将变更从一个分支合并到另一个分支。

*   Git Stash

    这个命令允许你保存你可能不想马上应用的更改。

*   Git Clone

    从远程存储库克隆到本地存储库

*   Git Pull

**好了，我们开始吧**

首先，我们需要创建我们的。Net 核心控制台应用程序一起工作。

打开 PowerShell，我将在命令提示符下打开它——只需在命令提示符下键入“PowerShell”——您将看到 PowerShell 列在顶部栏的下方。

[![image](img/1fc6177f5ae7bcdfe90397a2d06fb705.png "image")T2】](https://aviddeveloper.com/wp-content/uploads/2018/10/image-7.png)

接下来只需浏览到您想要创建新项目的目录，在我的例子中，我将在“c:\dev\github”中创建。

现在创建应用程序，并通过键入->，将其命名为“GitCommandLine”

' dotnet 新控制台-n GitCommandLine '。

您应该看到下面的内容..

[![image](img/81c6dc3c26e646aad2ddde68125e1bf2.png "image")T2】](https://aviddeveloper.com/wp-content/uploads/2018/10/image-8.png)

既然我们已经创建了 dotnet 核心控制台应用程序，现在让我们从 Git 开始。

首先，让我们导航到我们刚刚创建的项目目录。

**去启动**

现在我们想把我们的新项目初始化成一个 Git 库，所以我们想使用如下的‘Git Init’命令。

[![image](img/ea2ab4d999d338f1655608e2c036796a.png "image")T2】](https://aviddeveloper.com/wp-content/uploads/2018/10/image-9.png)

**Git 配置**

现在我们要设置配置，特别是与我们的提交相关联的身份(用户名和电子邮件),我将使用

用户名:无名氏

邮件地址:【john@johndoe.com】T2

注意:配置范围只是这个项目，所以我排除了–global 开关。

[![image](img/6028b93c636441f1a80d65f64ca75560.png "image")T2】](https://aviddeveloper.com/wp-content/uploads/2018/10/image-10.png)

**git config–l**

*   让我们查看当前配置，通过发出“git config–l 命令”来确认正确应用的设置

[![image](img/304407053b0afeb319e2fdee33445fbb.png "image")T2】](https://aviddeveloper.com/wp-content/uploads/2018/10/image-11.png)

就像你在底部看到的那样。

**Git Status**

让我们调用一个 Git status 来查看我们的存储库中当前的变化。

[![image](img/76a442bf276ee606f9d3002fdac8fdc0.png "image")T2】](https://aviddeveloper.com/wp-content/uploads/2018/10/image-12.png)

这告诉我们，我们没有指定任何文件要包含在下一次提交中。

**去添加**

如上所述，我们既可以将 Git add 作为一个单独的命令调用，也可以在 commit 命令中调用，现在让我们单独调用它。

在这里，我们指定要添加到 staging 中的内容，我们使用“git add”添加所有内容如果我们愿意，我们可以指定单独的文件。

现在让我们在应用 git add 命令后重新运行我们的 git 状态。

[![image](img/250dc0a5a3c2bb699e0d05c8390c7902.png "image")T2】](https://aviddeveloper.com/wp-content/uploads/2018/10/image-13.png)

您现在会看到输出是不同的——表明我们已经准备好提交新文件。

**去委员会**

让我们继续提交它们，我们将在提交中添加一条消息，我们称之为“初始提交”

[![image](img/96f702e3099b22a73c399cbb10398887.png "image")T2】](https://aviddeveloper.com/wp-content/uploads/2018/10/image-14.png)

**去推**

现在，我们希望将本地存储库的更改推送到远程存储库(GitHub)。

*仅供参考:我已经在我的 GitHub 账户([https://github.com/andrewcahill/GitCommandLine](https://github.com/andrewcahill/GitCommandLine))中创建了一个新的资源库。*

为了推送，我需要指定我的远程存储库的详细信息，特别是它的位置，所以我使用下面的命令。

git 远程添加原点[https://github.com/andrewcahill/GitCommandLine.git'](https://github.com/andrewcahill/GitCommandLine.git%E2%80%99)。

[![image](img/f60bdefa5584198ca12e9f1cd65f2a3a.png "image")T2】](https://aviddeveloper.com/wp-content/uploads/2018/10/image-15.png)

现在我们将使用' git push -u origin master '发出 push 命令。

[![image](img/d4b92f144dec584c7771d6abb3109415.png "image")T2】](https://aviddeveloper.com/wp-content/uploads/2018/10/image-16.png)

很好，现在我们在 GitHub 中有了我们的知识库。

现在，让我们创建一个新的开发分支，在这个新分支中进行一些更改，然后将它们推送到 GitHub，最后将这些更改合并到 master。

**Git Checkout**

我们将使用'-b '开关来指定一个新分支，并将这个新分支称为“开发”

[![image](img/3050931c816c3034a52cfe20180e9567.png "image")T2】](https://aviddeveloper.com/wp-content/uploads/2018/10/image-17.png)

我将对我们的 Program.cs 文件做一个小改动，将“Hello world！”输出到“你好 GitHub！”

*快速提示:要查看文件内容，PowerShell 命令“Get-Content”将允许您这样做。*

我们可以用这个来查看前后的 a，如下图..

[![image](img/ad54a5b77bb9d2de7ec26dd770c8afc2.png "image")T2】](https://aviddeveloper.com/wp-content/uploads/2018/10/image-18.png)

在改变之后..

[![image](img/e20ee26389c1ec77369b3810bb845f93.png "image")T2】](https://aviddeveloper.com/wp-content/uploads/2018/10/image-19.png)

git diff

我们可以使用“Git diff”来查看将在下一次提交时应用的更改

[![image](img/ce155b55044f529cf8c1ead79e886260.png "image")T2】](https://aviddeveloper.com/wp-content/uploads/2018/10/image-20.png)

现在让我们提交它们，为什么这次不在提交命令中添加“add”命令，并添加消息“Updated output”。

[![image](img/a70d050e13eef5a6935d7c4338ad2d21.png "image")T2】](https://aviddeveloper.com/wp-content/uploads/2018/10/image-21.png)

现在让我们将我们的更改推进到一个新的远程“开发”分支。为了做到这一点，我们希望通过发出命令‘git push–set-upstream origin development’来创建新的远程分支

*注意这只是一次性操作*

**Git Merge**

现在让我们将这些开发变更合并到 master 中，这样两者就同步了。

这是一个两步的过程，首先让我们结帐或切换到我们的主分支，然后我们将调用合并-如下所示。

[![image](img/65aa4917b9197175745d1e29c1eeeb67.png "image")T2】](https://aviddeveloper.com/wp-content/uploads/2018/10/image-22.png)

现在我们可以看到主分支有一个更新挂起，所以让我们将这个更改推送到我们的远程存储库。

[![image](img/f343a3f17163d5fe7fd2b1db96bdefef.png "image")T2】](https://aviddeveloper.com/wp-content/uploads/2018/10/image-23.png)

就是这样，至少从库的创建者的角度来看，从贡献者的角度来看呢，他们可能想为你的库做贡献。在这种情况下，过程非常简单。

我们真正需要做的是将这个存储库克隆到我们选择的目录中，进行更改并将其推回。

让我们这样做..

我将在我的 github 目录下创建一个名为“GitCommandLineContributor”的新文件夹。

**Git 克隆**

现在让我们导航到我们的新目录，并使用“git clone[https://github.com/andrewcahill/GitCommandLine.git'](https://github.com/andrewcahill/GitCommandLine.git%E2%80%99)进行克隆。

[![image](img/dccdb5fbd1ec7c99b5cab6a04887bda7.png "image")T2】](https://aviddeveloper.com/wp-content/uploads/2018/10/image-24.png)

通常变更是在开发或特性分支中完成的，所以让我们切换到这里的开发分支，发出‘git check out development’。

现在，作为贡献者，让我们对 Program.cs 文件进行另一项更改–>我们将从“Hello GitHub”更改为“Hello from contributor”。

好的，现在让我们继续，像以前一样承诺和推动。

[![image](img/a8a4e240f0e5156a8fd2810952703a95.png "image")T2】](https://aviddeveloper.com/wp-content/uploads/2018/10/image-25.png)

**Git Pull**

现在回到我们的所有者视点(在我的例子中是 c:\dev\github\gitcommandline ),切换到开发，通过发出“git pull”来删除贡献者可能已经发生的任何更改。

[![image](img/edb68ea042eedeff6a9e0a55ab5bb50f.png "image")T2】](https://aviddeveloper.com/wp-content/uploads/2018/10/image-26.png)

我们可以看到，在执行拉取后，我们可以看到应用了新的更改。

现在，让我们将这些贡献者更改合并到 master 中。

所以我们发布‘git check out master’&‘git merge development’，最后像以前一样发布‘git push’。

[![image](img/43527ff38034a738e8fd767df519fa78.png "image")T2】](https://aviddeveloper.com/wp-content/uploads/2018/10/image-27.png)

**转到帮助**

您可以随时运行“git–help”命令，该命令列出了所有可运行的 git 命令。你也可以运行一个。git[command]–帮助；以显示一个网页，详细介绍有关该特定命令的更多信息。例如‘git commit–help’–>非常酷。

**Git Stash**

最后，我想展示的最后一个命令是 Git Stash 命令，因为我不止一次忘记在进行更改之前切换到正确的分支，例如，意外地在 master 而不是 development 上进行更改——这个命令真的帮助了我。因为我通常试图使我的提交变得更小，而且我以前经常只是简单地将我的更改复制到新的分支——这是一个痛苦且容易出错的过程。

我将如何展示它:

举个例子，我们无意中对 master 做了一个我们想对 development 做的更改，所以我们隐藏这些更改，切换到正确的分支(development)并将这些更改应用到 development 分支。

首先，通过运行“git status”确保我们在主分支中

[![image](img/e295e850ca54e9d29bf4d6f37be0547b.png "image")T2】](https://aviddeveloper.com/wp-content/uploads/2018/10/image-28.png)

让我们再次用“Hello owner！”来修改我们的 Program.cs 文件在我们的输出中。

让我们再次确认我们的更改..

[![image](img/377ebebba466f2f0112ff4553ad6304f.png "image")T2】](https://aviddeveloper.com/wp-content/uploads/2018/10/image-29.png)

现在我们意识到这个变化应该已经在开发中，所以，让我们隐藏这些变化。

[![image](img/1b6bbe1fec3f7699156bf5226f4eb3a3.png "image")T2】](https://aviddeveloper.com/wp-content/uploads/2018/10/image-30.png)

签出开发并运行“git stash apply”。

[![image](img/b12e7e9b5d019d10a0b3135847e2c7e7.png "image")T2】](https://aviddeveloper.com/wp-content/uploads/2018/10/image-31.png)

现在再次执行相同的步骤(git commit with add–git push–git check out master–git merge development &最后 git push ),如下所示..

[![image](img/83f3c5a3b68c8d166613bb98f213d2d9.png "image")T2】](https://aviddeveloper.com/wp-content/uploads/2018/10/image-32.png)

**结论**

这是我最常用的 Git 命令，我希望有人会觉得这个概述有用。

**资源:**

有关 Git 命令、解释等的更多信息。退房:[https://git-scm.com/](https://git-scm.com/)

帖子[我的 Git 命令行工具带](https://aviddeveloper.com/my-git-command-line-toolbelt/)首先出现在 [Avid 开发者](https://aviddeveloper.com)上。