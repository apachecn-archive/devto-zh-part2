# Git 标签

> 原文：<https://dev.to/zellwk/git-tags--37a>

[https://www.youtube.com/embed/59OgWy4BO90](https://www.youtube.com/embed/59OgWy4BO90)

我们使用 Git 标签来创建发布。在这个视频中，你将学习如何在没有 Git 流的情况下手工标记。

## 创建标签

创建标签有两种方法。

### 第一种方法:

1.  转到 Git 客户端中的 Git 历史记录
2.  右键单击要创建标记的提交
3.  选择创建标签
4.  命名您的标签

[![Right clicking on the commit in Git history](img/f6a44efca551a354615b7b5df4b79df6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3_wnpEqS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/git-tag/method1.png)

### 第二种方法

1.  前往侧边栏
2.  右键单击要创建标记的分支。
3.  选择创建标签
4.  命名您的标签

注意:这将在分支所在的同一个提交上创建一个标记。

[![Right clicking on the branch in the sidebar](img/d059efee5d1faa907af206b842cf9c89.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MUxfMQU7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/git-tag/method2.png)

## 删除标签

右击标签并选择“删除标签”。

[![Contextual menu when right clicking on a tag in the sidebar](img/d0e5fe68cd30afd0c8cdee22cfec0e24.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BkesT4yV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/git-tag/delete.png)

选择“从远程存储库中删除标记”,也从您的远程存储库中删除标记。

[![Choice to delete tag from remote](img/58bb557bf892d4b86f7193f712d6cafe.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dNHRif-U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/git-tag/delete2.png)

## 推送标签

当您将更改推送到遥控器时，您可以选择`push all tags`将标签推送到遥控器。默认情况下，此选项处于选中状态。

[![Choice to push tag into remote](img/5af1b55834959cdc473b784b308123d8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--STmUgeO7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/git-tag/push.png)

* * *

感谢阅读。这篇文章最初发表在我的博客上。如果你想要更多的文章来帮助你成为一个更好的前端开发者，请注册[我的时事通讯](https://zellwk.com)。