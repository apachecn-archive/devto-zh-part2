# 克隆 Git 存储库

> 原文：<https://dev.to/zellwk/cloning-a-git-repository-25ek>

[https://www.youtube.com/embed/GYZBxXmrfWU](https://www.youtube.com/embed/GYZBxXmrfWU)

注意:这是 Git 初学者系列的第五个视频。[在这里看第一个视频](https://dev.to/blog/setting-up-git)。

假设你想和一个朋友一起做一个项目。你们两个将在同一个项目上创建提交。

假设你的朋友创建了这个项目。他们在 Github 上初始化了一个存储库。

接下来您需要做的是将项目从遥控器复制到您的计算机上。

在 Git 中，可以通过 Git 克隆来实现这一点。

## 什么是 Git 克隆

git 克隆使得复制项目更加容易。它做三件事:

1.  初始化本地存储库
2.  将远程 URL 添加到您的本地存储库中
3.  将存储库拖到您的计算机上。

注意:如果您愿意，可以手动完成这三个步骤。

## 克隆一个项目

首先，您需要转到您想要克隆的项目。

假设我们想要克隆类型。Typi 是我做的一个库，当你创建网站的时候，它可以让排版变得简单。

要克隆这个项目，你可以点击绿色的大按钮克隆或下载。

[![The clone button](img/34cb3c06342b3484352a89cce9a8b307.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ypYZQq8e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/git-clone/clone.png)

当你点击克隆或下载，你会看到一个网址。

复制此 URL。

再次强调，确保使用 SSH 进行克隆。当您使用 SSH 时，您不必在每次推送到远程或从远程拉取时都输入您的用户名和密码。

[![The git URL](img/f2c21186a78cbffae06e4e3ade32e059.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gq1D-otT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/git-clone/git-url.png)

然后，打开叉子。

您可以通过进入`File`来克隆存储库。选择`clone`。

[![The clone menu in Fork](img/3b20f1ae3b9d3bbe6b49689a916272e5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Dz_zsc3---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/git-clone/fork-clone.png) 

<figure>

<figcaption>克隆菜单中的</figcaption>

</figure>

Fork 将自动填充您复制到存储库 URL 字段中的 URL。

父目录是您希望存储库所在的文件夹。在我们的例子中，这是 Git。如果你想改变目录，你可以改变它。

Name 将是项目的名称。这个名称默认为远程存储库上的项目名称。如果你也想换，你也可以换，但是我们通常不换。

当您单击 Clone 时，Fork 将开始下载项目。

[![Project cloned](img/1f97e2fc4f9766b6f64d49a49283fa39.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_TI11xxD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/git-clone/cloned.png) 

<figure>

<figcaption>项目克隆</figcaption>

</figure>

## 项目间切换

当您克隆项目时，您将能够在您选择的 Git 客户机中看到该项目。

在我们的例子中，你可以点击左上角的搜索图标，你会看到“Typi”和“Project”。

[![Switching between projects](img/e7a96ff27cb51f3c860be2cca43b9ceb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XhZIcabF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/git-clone/switch.png) 

<figure>

<figcaption>项目间切换</figcaption>

</figure>

## 合作者权利

您可以提交到您克隆的本地存储库，但是您不能将您的更改推送到远程存储库。

这是因为您还没有权限写入存储库。它防止未经授权的人更改属于他人的代码。

如果您想推送至存储库，您需要协作者权限。您的朋友可以通过将您添加为合作者来授予您访问存储库的权限。

他们可以通过去 Github 项目添加你为合作者。然后，点击设置，然后点击 collaborator。接下来，他们需要在文本字段中输入您的用户名，然后单击“添加协作者”

[![Adding a collaborator](img/e5ec3914d81343442203214996cce5ad.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aQdW84It--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/git-clone/collaborator.png)

## 包装完毕

Git clone 同时做三件事:

1.  初始化本地存储库
2.  将远程 URL 添加到您的本地存储库中
3.  将存储库拖到您的计算机上。

如果您想要推送至远程存储库，您需要协作者权限。让你的朋友给你回购的权限。

* * *

感谢阅读。这篇文章最初发表在我的博客上。如果你想要更多的文章来帮助你成为一个更好的前端开发者，请注册[我的时事通讯](https://zellwk.com)。