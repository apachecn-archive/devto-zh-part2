# Git 中的分支是什么？

> 原文：<https://dev.to/zellwk/what-is-a-branch-in-git-3ink>

[https://www.youtube.com/embed/yARRMa9zssw](https://www.youtube.com/embed/yARRMa9zssw)

注意:这是 Git 初学者系列的第七个视频。[在这里看第一个视频](https://zellwk.com//blog/setting-up-git)。

想象有平行世界。我们有:

1.  在这个世界里，我制作了这个视频，你们正在观看。
2.  在这个世界里，我制作了这个视频，但是你没有在看。
3.  一个我没有创作这个视频的世界。

在这个平行世界概念中，Git 分支就是一个平行世界。

你可以有一个分支在一个世界里保持不变。然后，你进入一个不同的世界。一旦你完成了你的代码，你就可以通过合并改变来完成最初的世界。

## 如何创建分支

打开你的 Git 客户端。寻找您想要从中分支的分支。右键单击它并选择 create new branch。

![Cursor on the menu that says create new branch](img/898948855f6d899692dd53f33559c14d.png)

你可以给你的分公司起任何你想要的名字。

通常我们作为开发人员的第一个分支是开发分支。

命名您的分支后，单击 create and checkout。在这种情况下，Checkout 意味着转移到开发分支。

![Setting the branch name to development](img/8437ce87fbef1bf40e359e474c8bf3e6.png)

一旦您创建了开发分支，您可以在您的分支部分看到两个分支——主分支和开发分支。

![Sidebar contains all branches. In this case, it shows master and development](img/e4509155474aab99ecb0d85328854263.png)

在 Git 历史中，您还可以看到一个名为`development`的新标签。这个`development`标签与`master`和`origin/master`在同一个提交上。

![Git history shows the `development` tag](img/cadc7717a5375c4430ca149f8579b3cc.png)

## 为什么要创建开发分支？

比方说，你有一个网站，人们可以看到。这个网站在 master 分支上。

如果你把代码提交给 master 分支，就意味着你直接改网站。如果您引入了任何错误，其他人可以立即看到您的错误。

我们是人类。我们会犯错。我们不想让人们看到我们的错误。

所以我们创建了一个新的分支，然后处理它。当我们完成时，并且当我们确定不再有 bug 时(至少我们尝试这样做！)，我们将更改推回到主分支机构以更新网站。

这就是我们使用开发分支的原因。

在这种情况下，主分支也可以称为生产分支。

## 如何对新的分支进行编码

当您创建一个新的分支时，您可以直接在分支本身上编码。您更改的任何代码都只会反映在该分支上。

假设我们想要创建一个名为`development.md`的新文件。在这个文件中，我们说“你好！这是发科犯的！”。

```
# development.md
Hello! This is committed from the development branch! 
```

Enter fullscreen mode Exit fullscreen mode

如果您回到您的 Git 客户端，您可以将这个变更提交到开发分支。

当您创建提交时，确保您在开发分支上。在 Fork，你可以通过观察加粗的树枝来判断你的位置。

[![Development is bold on the sidebar. This shows that the development branch is selected](img/bae477aaf77d66166857eff4dfed1948.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2lRS0y1s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/git-branch/sidebar-2.png)

将您的代码提交到开发分支。

现在，如果您查看 Git 历史，您可以看到开发分支比`origin/master`分支和本地`master`分支早一次提交。

这表明我们可以在`development`分支上尽可能多地编码，而不会影响其他分支。

[![`development` tag is ahead of `origin/master` and `master`](img/28086cb5ad24938fe64e5d7d986900de.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--11DZ0vVq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/git-branch/history-2.png)

## 将开发分支推入 Git 远程

您可以通过点击 push 按钮将开发分支推入 Git 远程。该步骤将[类似于您第一次推动主分支](https://zellwk.com//blog/pushing-to-a-git-remote)时的步骤。

一旦推送完成，您可以在同一个提交中看到与`development`标记相同的`origin/development`标记。

[![`origin/development` tag on the same commit as the `development` tag.](img/58b00554f84200ef8b0d818818e00c09.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LCEq53Tg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/git-branch/history-3.png)

## 分支间切换

要在分支之间切换，你可以双击你想去的分支(在侧边栏上)。如果您双击主文件，您将签出主文件。

结帐意味着切换到您选择的分支。

[![Double-click the master branch to checkout the master branch](img/6bc4aea5a30fe80c304a5de044937c68.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--swTwbNPq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/git-branch/sidebar-3.png)

如果你现在看你的项目，你会发现`development.md`文件不见了。这再次证明了您可以在您的`development`分支上进行尽可能多的提交，而不会影响其他分支。

## 合并分支

如果您完成了开发过程，并且准备将分支合并回`master`。

要合并，首先签出要合并到的分支。这个应该是`master`。

然后，为了将`development`分支合并到`master`分支中，在 Git 客户端中右键单击`development`分支，并选择 merge into‘master’

[![Cursor on menu item that says merge into 'master'](img/3047564ce4dba296c01519331e89b56c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1tcgBk6r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/git-branch/merge-1.png)

<figure>

<figcaption>T6】</figcaption>

</figure>

Fork 将询问您是否想要创建一个合并提交。有些客户端会自动为您完成这项工作。

[![Menu that asks whether you want to create a merge commit](img/2111babbfbf4086e460a7dd300782d7c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BnWL-Pjw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/git-branch/merge-2.png)

<figure>

<figcaption>T6】</figcaption>

</figure>

选择合并，合并将完成。

如果您现在查看 Git 历史，您会看到`master`分支在`development`和`origin/development`分支之前。

这是因为我们已经完成了合并提交。

[![`master` branch is ahead of the `development` and `origin/development` branches](img/ad64fbcebb173b1935f6ccc9770e9aa9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--F7ybrd9z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/git-branch/history-4.png)

同时，`master`比`origin/master`分支提前两次提交。这就是为什么我们在侧边栏看到 2 了。

[![`master` is two commits ahead of the `origin/master` branch](img/06744dda913ff05f82bd7a34895d0032.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BvOGD8uD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/git-branch/sidebar-4.png)

当您完成合并时，您可以通过点击按钮来更新 Git remote。

[![`origin/master` and `master` are now on the same commit](img/f4e5fd6aa660119afb154068b577bb5b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CRMPYdkS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/git-branch/history-5.png)

## 删除分支

要删除分支，请右键单击要删除的分支，然后选择删除“分支名称”。

[![Cursor on menu item that says delete development](img/31bb541711315fbbd1d0dd330f7247f7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rP888jmD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/git-branch/delete-1.png)

在 Fork 中，您还可以选择从远程中删除分支。

[![Menu that asks if you want to remove the branch from the origin](img/aa216406c7fd7267ecb4627734178967.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JqJj_O6z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/git-branch/delete-2.png)

点击删除，Fork 将删除两个分支。

删除完成后，您可以查看一下 Git 历史。您会注意到`origin/development`和`development`标签都已经从历史中消失了。

[![`origin/development` and `development` tags have both disappeared from the history](img/25b4fd35572f51b5643ca85a9237534c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6whsSHZB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/git-branch/history-6.png)

## 包装完毕

分支就像一个平行的世界，在这里你可以创建提交，而不会在产品代码中引入错误。在将您的分支合并到产品代码之前，您总是可以修复 bug。

* * *

感谢阅读。这篇文章最初发表在我的博客上。如果你想要更多的文章来帮助你成为一个更好的前端开发者，请注册[我的时事通讯](https://zellwk.com)。