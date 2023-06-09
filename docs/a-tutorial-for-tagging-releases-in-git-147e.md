# Git 标签在 Git 中标记发布的教程

> 原文：<https://dev.to/neshaz/a-tutorial-for-tagging-releases-in-git-147e>

标签是 Git 的一个简单方面，它们允许您识别代码的特定发布版本。你可以把一个标签想象成一个不改变的**分支。一旦创建，它就失去了更改提交历史的能力。**

## 两种类型的 Git 标签

Git 中的[标签有两种:**带注释的**和**轻量级的**。它们都允许您引用存储库中的特定提交，但是它们在可以存储的元数据数量上有所不同。](https://kolosek.com/git-commands-tutorial-part2/)

### 标注标签

**带注释的标签**将额外的**元数据**作为完整对象存储在 Git 数据库中，比如作者姓名、发行说明、标签消息和日期。所有这些数据对你项目的**公开发布**都很重要。

标签还可以包括更具描述性的**标签消息**或**注释**，就像您要合并时的[提交消息一样。通常，这是通过使用`-a`进行注释来实现的:](https://kolosek.com/git-merge/) 

```
$ git tag -a v1.0.0 
```

执行该命令，您将创建一个新的带注释的标记，用 v1.0.0 版标识。然后，该命令将打开您的提交编辑器，以便您可以填充元数据。

如果你想添加一个**标签消息**，你可以通过`-m`选项，这是一个类似于`git commit -m`的方法。

```
$ git tag -a v1.0.0 -m "Releasing version v1.0.0" 
```

### 轻量级标签

轻量级标签是向 git 存储库添加标签的最简单方式，因为它们只存储它们所引用的提交的散列。它们是在没有-a、-s 或-m 选项的情况下创建的，并且*不包含任何额外的信息。*

> 轻量级标签本质上是提交的“书签”，它们只是提交的名称和指针，对于创建相关提交的快速链接很有用。 [By Bitbucket 教程](https://www.atlassian.com/git/tutorials/inspecting-a-repository/git-tag)

要创建一个新的轻量级标签，执行以下命令:

```
$ git tag v1.0.0 
```

## 附加命令

**列出标签** - `git tag`
每当您想要列出所有现有标签时使用该命令，或者您可以使用`git tag -l 'v1.1.*'`过滤列表，其中`*`充当**通配符**。它将返回一个标记有`v1.1`的标签列表。

您会注意到，当您调用 [`git tag`](https://kolosek.com/git-commands-tutorial-part1/) 时，您看不到注释的内容。要预览它们，你必须将`-n`添加到你的命令:`git tag -n3`。

```
$ git tag -l -n3
v1.0            Release version 1.0
v1.1            Release version 1.1
v1.2            Release version 1.2 
```

该命令列出所有现有的标签，最多 3 行标签消息。默认情况下`-n`只显示第一行。

**标记详细信息** - `git show <tag_identifier>`
该命令向您显示来自被标记的提交的**标记详细信息**和**信息**。

```
$ git show v1.0
tag v1.0
Tagger: Kolosek 
Date:   Fri May 11 10:45:33 2018 +0100

Release version 1.0
-----BEGIN PGP SIGNATURE-----
Version: GnuPG v1

iMTvhAA...
-----END PGP SIGNATURE-----

commit 7d44b6bb8abb96dee33f32610f56441496d77e8a
Author: Kolosek 
Date:   Fri May 11 9:50:13 2018 +0100

    Edited the Login form
... 
```

它打印作者的名字、创建日期、消息、GnuPG 签名(如果有的话)以及关于引用提交的信息。如果标签是轻量级的，输出将被限制为关于引用提交的信息。

**编辑标签** - `git tag -a -f <tag_identifier> <commit_id>`
如果你试图创建一个与已有标签标识符相同的标签，Git 会抛出一个错误:`fatal: tag 'v1.0' already exists`。

您可以简单地替换它，同时保留现有的描述，而不必删除它并重新添加标签。在你的提交历史中用`<commit_id>`选择你希望标签移动到的地方，并在你的命令中添加`-f`或`-force`。

> 当你“强迫”这样的改变时，记得提醒你的团队成员。如果他们仍然有一个“旧”版本的标签，当他们试图推送到服务器时，可能会导致冲突！

如果您已经**将标签**推送到服务器，并且想要修复它，那么在您运行下面的[命令](https://kolosek.com/git-commands-tutorial-part2/) : `git push origin -f --tags`之前，首先确保您的标签本地版本是正确的。

**删除标签** - `git tag -d <tag_identifier>`
一般来说，**没有理由删除标签**，因为它们不贵，不占用任何资源，除非你错误地创建了一个指向错误提交的标签。

如果标签已经被**推送到**，并且您需要从远程存储库中删除它，那么运行:`$ git push origin :v1.0`。

**发布标签** - `git push <location> <tag_identifier>`
标签只是对本地存储库的引用，它*而不是*自动[与其余代码一起推送到远程存储库](https://kolosek.com/git-merge/)。相反，你可以单独地`git push`标签，或者你可以运行`git push --tags`一次推送所有标签。可以类似于推动分支来完成:

```
$ git push origin v1.0 
```

**排序标签** - `git tag --sort=<type>`
当查看一个有很多标签的项目时，使用[排序选项](https://stackoverflow.com/questions/14273531/how-to-sort-git-tags-by-version-string-order-of-form-rc-x-y-z-w?#answer-22634649)可以派上用场。支持的类型有:

*   `refname`(按字典顺序排序)，
*   `version:refname`或`v:refname`(这里标签名被视为版本)。

```
git tag -l --sort=-version:refname "v*" 
```

在这里，我列出了所有名称以“v”开头的标签的版本。

* * *

社交媒体中的 *git 标签*和*#标签*不是一样的吗，只是方式不同而已？它帮助您返回并列出您以前的所有版本！

当你在这里的时候，请注意 Kolosek 总是很乐意帮助你，把你的想法变成现实。如果你需要帮助，你可以通过社交媒体联系科洛塞克。[推特](https://twitter.com/kolosekit)，也许？

本文最初发表于 [Kolosek 博客](https://kolosek.com/git-tags/?utm_source=dvt)。