# Git:一个伟大的盟友

> 原文：<https://dev.to/alexribeirodev/git-um-grande-aliado-41if>

<figure>[![](img/b592e19615538b9aced31d0d5f904d6f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6MeNy89_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AaugkC9jV1gsn_kFQ) 

<figcaption>【笔记本电脑 USB 端口中的 pendrive】作者[Brina Blum](https://unsplash.com/@brina_blum?utm_source=medium&utm_medium=referral)on[Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

</figure>

如果您是开发人员或曾参与过某个项目的版本控制，您可能听说过 Git。

如果您还不熟悉此工具，请继续阅读此帖子，此帖子可帮助您完成当前和未来的项目。

在开始了解 Git 的概念之前，了解什么是版本控制是很重要的，因为 Git(和其他类似的系统一样)使用这个系统来做最好的事情。

根据文件本身

> 版本控制是一种系统，用于记录一段时间内对一个文件或一组文件所做的更改，以便检索特定版本。

在这些版本控制的前提下，我们可以执行的其他控制包括确定谁对任何修改负责，映射错误源，甚至比较一段时间内所做更改的进度。

太棒了！你能想象这对我们的项目带来了多大的安全性和实用性吗？！重要注意事项:

*   完整性:控制对项目文件或结构的任何更改；
*   真实性:控制文件的属性和来源，以确定谁负责或谁更改了任何内容；
*   可用性:根据文件的存储位置，可以通过 internet 或本地网络访问任何计算机上的项目。
*   机密性:根据文件的存储方式和位置，您可以控制对文件的访问权限。

[![](img/e97382c5ee0ef1c2ad1b4d8a0f578de5.png)T2】](https://i.giphy.com/media/oYtVHSxngR3lC/giphy.gif)

> 真不敢相信这个工具能做这么多，是吧？！

### 毕竟 Git 是什么？

Git 是一种分布式文件版本控制系统，允许多个用户处理同一个项目。

有多种版本控制系统，但 Git 的区别在于它的速度，它将大多数控制放在本地，而无需立即连接到主存储库。

Git 的另一个不同之处在于它是如何版本控制和确保文件完整性的，因为它使用分支的概念(类似于快照)，可以随时创建分支，也就是说，它允许我们在同一文件夹中拥有同一项目的副本。

使用 git 的这些优势的一个简单而实用的例子是:我在主分支(master)中有自己的网站，我想测试一些新的东西，而不影响它的就绪性和功能。我将创建一个新的分支，从该分支中，我可以更改项目中的任何内容。一切准备就绪并经过测试后，我将进行这些更改，并与主分支进行合并(这种合并称为合并)。如果主分支出现问题，我可以轻松地将任何更改还原到以前的状态。

### Lista de comandos

Git 拥有各种工具，可以通过终端命令使用。您可以在下面查看这些命令。

#### 创造

克隆现有存储库:

```
$ git clone ssh://user@domain.com/repo.git 
```

Enter fullscreen mode Exit fullscreen mode

创建新的本地存储库:

```
$ git init 
```

Enter fullscreen mode Exit fullscreen mode

#### 局部修改

活动目录中修改过的文件:

```
$ git status 
```

Enter fullscreen mode Exit fullscreen mode

对版本化文件的修改:

```
$ git diff 
```

Enter fullscreen mode Exit fullscreen mode

将所有当前更改添加到下一次提交:

```
$ git add . 
```

Enter fullscreen mode Exit fullscreen mode

在下一次提交时添加对<file>的更改:</file> 

```
$ git add -p \<file\> 
```

Enter fullscreen mode Exit fullscreen mode

提交所有修改

```
$ git commit -a 
```

Enter fullscreen mode Exit fullscreen mode

提交更改

```
$ git commit 
```

Enter fullscreen mode Exit fullscreen mode

修改 o último 提交:

```
$ git commit --amend 
```

Enter fullscreen mode Exit fullscreen mode

#### 时间轴

显示所有承诺，从最新承诺开始:

```
$ git log 
```

Enter fullscreen mode Exit fullscreen mode

显示特定文件的修改:

```
$ git log -p \<file\> 
```

Enter fullscreen mode Exit fullscreen mode

谁在文件中更改了什么和什么时间:

```
$ git blame \<file\> 
```

Enter fullscreen mode Exit fullscreen mode

#### 分支 e 标签

列出所有现有分支:

```
$ git branch -av 
```

Enter fullscreen mode Exit fullscreen mode

更改当前分支:

```
$ git checkout \<branch\> 
```

Enter fullscreen mode Exit fullscreen mode

从当前头创建分支:

```
$ git branch \<new-branch\> 
```

Enter fullscreen mode Exit fullscreen mode

Deleta uma 分支本地:

```
$ git checkout --track \<remote/branch\> 
```

Enter fullscreen mode Exit fullscreen mode

删除本地分支:

```
$ git branch -d \<branch\> 
```

Enter fullscreen mode Exit fullscreen mode

用标签标记当前提交:

```
$ git tag \<tag-name\> 
```

Enter fullscreen mode Exit fullscreen mode

#### 更新发布

列出当前配置的所有远程

```
$ git remote -v 
```

Enter fullscreen mode Exit fullscreen mode

显示远程信息:

```
$ git remote show \<remote\> 
```

Enter fullscreen mode Exit fullscreen mode

添加一个名为<remote>的新远程存储库:</remote> 

```
$ git remote add \<shortname\> \<url\> 
```

Enter fullscreen mode Exit fullscreen mode

下载对<remote>的所有修改，但不集成到 HEAD:</remote> 

```
$ git fetch \<remote\> 
```

Enter fullscreen mode Exit fullscreen mode

下载修改并自动合并:

```
$ git pull \<remote\> \<branch\> 
```

Enter fullscreen mode Exit fullscreen mode

将本地修改发布到远程:

```
$ git push \<remote\> \<branch\> 
```

Enter fullscreen mode Exit fullscreen mode

Deleta uma 分支 no remote:

```
$ git branch -dr \<remote/branch\> 
```

Enter fullscreen mode Exit fullscreen mode

Publica suas 标签:

```
$ git push — tags 
```

Enter fullscreen mode Exit fullscreen mode

#### Merge e rebase

将<branch>合并到目前的头上:</branch> 

```
$ git merge \<branch\> 
```

Enter fullscreen mode Exit fullscreen mode

在<branch>上做头部折缝:</branch> 

```
$ git rebase \<branch\> 
```

Enter fullscreen mode Exit fullscreen mode

中止降级:

```
$ git rebase --abort 
```

Enter fullscreen mode Exit fullscreen mode

解决冲突后继续降级:

```
$ git rebase --continue 
```

Enter fullscreen mode Exit fullscreen mode

使用您配置的合并工具解决冲突:

```
$ git mergetool 
```

Enter fullscreen mode Exit fullscreen mode

使用您的编辑器手动解决冲突，并将文件标记为已解决:

```
$ git add \<resolved-file\>

$ git rm \<resolved-file\> 
```

Enter fullscreen mode Exit fullscreen mode

#### 撤消

舍弃目前目录中的所有本机变更:

```
$ git reset --hard HEAD 
```

Enter fullscreen mode Exit fullscreen mode

舍弃特定档案中的本机变更:

```
$ git checkout HEAD \<file\> 
```

Enter fullscreen mode Exit fullscreen mode

撤消提交(创建一个具有相反修改的新提交):

```
$ git revert \<commit\> 
```

Enter fullscreen mode Exit fullscreen mode

将 HEAD 指针重置为以前的提交

…并放弃此后的修改:

```
$ git reset --hard \<commit\> 
```

Enter fullscreen mode Exit fullscreen mode

…并将所有修改保留为未准备好的修改:

```
$ git reset \<commit\> 
```

Enter fullscreen mode Exit fullscreen mode

…并保留未提交的本地修改:

```
$ git reset --keep \<commit 
```

Enter fullscreen mode Exit fullscreen mode

> 我知道，太多了。

[![](img/b68e1378851845d325607148de206dda.png)T2】](https://i.giphy.com/media/ijGS9TME6iN7W/source.gif)

### 工作流程

Git 的工作流程通常是非常简单的顺序:

1.  修改工作目录中的文件或文件组：
2.  您可以选取档案，将它们的快照新增至暂存区：
3.  提交时，会将文件保留在登台区中，并将其永久存储在 Git 目录中。

### 迪卡

如果您想或需要与 Git 一起练习主要工作流程，我推荐这个网站:[【http://learn . vidyek . net/learn/git】](http://aprenda.vidageek.net/aprenda/git)。

它可以简化和客观地执行 Git 的各种命令。

### 参考文献

*   [https://git-scm.com/book/pt-br/v1](https://git-scm.com/book/pt-br/v1/Primeiros-passos-No%C3%A7%C3%B5es-B%C3%A1sicas-de-Git)
*   [https://www . oficinatanet . com . br/post/16111-o-what-e-how-o-git-e-github](https://www.oficinadanet.com.br/post/16111-o-que-e-e-como-funciona-o-git-e-github)如何运作
*   [https://pt.wikipedia.org/wiki/Git](https://pt.wikipedia.org/wiki/Git)
*   [https://www.git-tower.com/blog/git-cheat-sheet](https://www.git-tower.com/blog/git-cheat-sheet)

#### 谢谢你，再见！

如果你喜欢我的文章，跟我来，我很快就会带来消息。

你想知道更多还是有疑问？留下评论。哦，我也接受批评。

抱抱！

* * *