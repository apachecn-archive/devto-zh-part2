# 如何派生和贡献 Github 回购

> 原文：<https://dev.to/ceri_anne_dev/how-to-fork-and-contribute-to-a-github-repo-5bfp>

在 Github 上发现了一个很酷的项目，你想参与其中吗？以下是如何分叉回购，保持最新，并提出拉请求。

**叉回购**

在原始回购中单击“分叉”

[![Fork](img/76d2daa5d54b57b02e75cc0c66f015c9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Wd_p_poK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vlfy5pq38pnfle4q0xq2.png)

这将在你自己的 Github 帐户中创建一个分叉版本，这样你就有了自己的回购副本。

**克隆分叉回购**

1)在分叉式回购中，单击“复制”或“下载”按钮

[![Clone or Download button](img/49460b91954b8d55d0fcf0f03e3ba94f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UX9kna-2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i6pz3i5dehsgvfry4wpr.png)

2)复制 git 地址

3)在终端中，导航到您想要保存回购的文件夹，然后输入 git clone，后跟复制的 git 地址，并点击 enter

```
 $ git clone <your-repo-address> 
```

Enter fullscreen mode Exit fullscreen mode

现在，您有了分叉回购的本地版本。

**让您的叉子保持最新**

您可以完全独立于原始版本来处理项目的分叉版本，但是如果您想要保持您的版本是最新的，那么您需要通过将它添加为`upstream`来将您的版本链接到原始版本。

设置(只需要做一次):

1)在原始回购中，点击克隆或下载并复制原始回购地址

2)在终端中，将 cd 放入您的项目文件夹

3)输入`git remote add upstream`，粘贴原始回购地址，点击回车

```
$ git remote add upstream <original-repo-address> 
```

Enter fullscreen mode Exit fullscreen mode

更新:

1)更新上游分支:

```
$ git fetch upstream 
```

Enter fullscreen mode Exit fullscreen mode

这将获取原始回购中所做的所有更改。

2)更新您的本地主机:

```
$ git pull upstream master 
```

Enter fullscreen mode Exit fullscreen mode

这将上游的变更拉入您的本地主分支。

3)然后 git push 到您的远程 repo

```
$ git push 
```

Enter fullscreen mode Exit fullscreen mode

这将使用更改更新您的分叉远程回购。

**提交拉取请求**

一旦您对 fork 进行了更改，您就可以请求将这些更改合并到原始项目中。

1)点击新的拉式请求

[![New pull request](img/c00c9167bd599a5f0179d1b1abd0efc8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ddFZ9lCF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/whboatvqlzz0nnjizn9d.png)

2)在“比较变更”页面上，检查基本分叉是否为原始回购，主要分叉是否为您选择了正确分支的回购

3)添加标题和描述

4)允许来自维护者的编辑将被检查。如果你对此不满意，那么取消选择

5)点击创建拉式请求

然后就要看原始回购的维护人员提出问题，要求修改或者合并进去了！🤞🏼

*本文最初发表在我的博客这里:*
[https://ceri-anne.co.uk/working-with-a-forked-git-repo](https://ceri-anne.co.uk/working-with-a-forked-git-repo)