# Git 命令终极教程[第 2 部分]

> 原文：<https://dev.to/neshaz/git-commands-ultimate-tutorial-part-2-7ko>

在上一篇 Git 命令文章中，您已经学习了如何使用 [Git 选项](https://kolosek.com/git-commands-tutorial-part1/)以及它们能做什么。今天，我们将拓展我们的视野，发现关于**常用 Git 命令**的一切。

## Git 命令

Git 命令用于访问 Git 工作目录，并通过修改、查看不同的文件和许多其他可能的方式将它连接到我们的远程存储库！一旦你的[项目](https://kolosek.com/install-ruby-on-rails-on-ubuntu/)设置好了，一定要用 Git 连接它。

通过下面的命令和简短描述，我们将确保您在需要时随时可以使用 Git。关于*所有*的详细描述，GIT 命令请查看 [git 的官方描述](https://git-scm.com/docs/git)。

**`$git config`**
你可以用它来**配置**作者的姓名、电子邮件地址、文件格式以及更多你提交的内容。

```
git config --global user.name "Kolosek
git config --global user.email "kolosek@example.com" 
```

Enter fullscreen mode Exit fullscreen mode

**`$git init`**
使用这个命令，你要确保你的 **git 库**被初始化，并在一个新的或现有的项目中创建初始的`.git`目录。输出如下:

```
Initialized empty Git repository in /path/.git/ 
```

Enter fullscreen mode Exit fullscreen mode

*你可以用`rm -rf .git`撤销一个`$git init`。*

**`$git clone <path>`**
这将创建一个 Git 存储库的**工作副本**从远程源到您的本地存储库。这是您在克隆 Git 存储库时想要使用的第一个命令。

```
git clone /path/repository 
```

Enter fullscreen mode Exit fullscreen mode

此外，您可以将**原始位置**添加为远程位置，这样您就可以轻松地再次从它那里获取数据，如果您有权限的话，还可以推送数据。一旦项目被克隆，你就可以开始工作了。写你的 [RSpec 测试](https://kolosek.com/rails-rspec-setup/)！

```
git clone git@github:user/repository.git 
```

Enter fullscreen mode Exit fullscreen mode

您可以一次克隆一个特定的分支:`git clone -b <branch_name><repository_url>` :

```
git clone -b branch_name git@github:user/repository.git 
```

Enter fullscreen mode Exit fullscreen mode

**`$git add <file_name>`**
**将工作目录中的一个或多个文件添加到索引中。**

**`$git commit`**
把你所有的**变化**写在索引里带着`-m`消息给头分支。

```
git commit -m "Commit these changes." 
```

Enter fullscreen mode Exit fullscreen mode

您还可以**提交**您添加到`git add`的任何文件，并且还可以提交从那以后您更改过的任何文件:

```
git commit -a 
```

Enter fullscreen mode Exit fullscreen mode

**永远承诺你所有的改变，即使只是一堆[水豚考验](https://kolosek.com/rails-capybara-setup/)！**

**`$git status`**
它向您显示了一个索引和工作目录文件之间的**状态**差异。**列出**您已经更改的文件，因为它们只在您的工作目录中而未被跟踪，并且因为它们准备好提交而被暂存。

```
On branch master
Initial commit

Untracked files:
  (use "git add <file>..." to include in what will be committed)

    File_name

nothing added to commit but untracked files present (use "git add" to track) 
```

Enter fullscreen mode Exit fullscreen mode

**`$git remote`**
显示您的资源库的所有**远程版本**。

**`$git checkout <branch_name>`** :您可以**将**从现有分支切换到另一个分支，或者创建一个新分支并切换到它`git checkout -b <branch_name>`。

**`$git branch`**
有了这个，你可以简单地**列出**所有现有的**分支**，包括使用`-a`的远程分支，或者创建一个新的分支，如果提供了分支名称的话。

**`$git push`**
**将**所有更改推送到远程存储库。

```
git push origin <branch_name> 
```

Enter fullscreen mode Exit fullscreen mode

您还可以从远程存储库中删除一个分支:

```
git push origin :<branch_name> 
```

Enter fullscreen mode Exit fullscreen mode

**`$git pull`**
**获取并合并**您在远程存储库中的更改到您的工作目录中。

**`$git merge <branch_name>`**
**将一个或多个分支合并到您的活动分支中，如果没有冲突，它将自动创建一个新的提交。**

> 在 Kolosek 中，我们将所有的更改提交给 Git，并确保在它们与应用程序的其余部分合并时通知我们的团队！尝试使用 [Rails 关联](https://kolosek.com/rkolosek.com/rails-associations/)创建自己的应用程序。

**`$git diff`**
显示**在你的工作树和索引之间的变化**，在两个分支之间，或者在磁盘上的两个文件之间的变化。*分支间示例:*

```
git diff <source_branch> <target_branch> 
```

Enter fullscreen mode Exit fullscreen mode

**`$git reset`**
**重置**你的索引和工作目录到你上次提交的状态。

```
git reset --hard origin/master 
```

Enter fullscreen mode Exit fullscreen mode

*`git reset --hard`也将撤销您到目前为止所做的更改！如果您想保留您的更改，请使用`git reset --soft`。*

**`$git revert`**
Revert 的工作方式与`$git reset`非常相似，但它不是重置，而是创建一个新的提交来撤销意外提交引入的所有内容。

**`$git tag`**
您可以使用**标记**来标记您所做的重大更改，例如发布。

```
git tag 1.0.0 <commit_id> 
```

Enter fullscreen mode Exit fullscreen mode

总是给你的新产品发布贴上标签。通过实现 [CarrierWave](https://kolosek.com/carrierwave-upload-multiple-images/) 并添加一个 release 标签来试试吧！

**`$git log`**
它显示了分支上提交的的**列表以及相应的细节。** 

```
commit 134808af7c596be8d92c619f9efb94542874e1e3
Author: Kolosek <kolosek@example.com>
Date:   Fri Mar 23 14:24:54 2018 +0100

    [#1] First Commit 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

至此，我们已经了解了 Git 命令的所有知识。别忘了，这些只是最基本和最常用的命令。希望这有助于您开始使用 Git！

这篇文章最初发表在 [Kolosek 博客](https://kolosek.com/git-commands-tutorial-part2/)上。