# 如何使用 Git stashes 作为临时存储

> 原文：<https://dev.to/zellwk/how-to-use-git-stashes-as-a-temporary-storage-lg5>

[https://www.youtube.com/embed/pmpGsPDPLrI](https://www.youtube.com/embed/pmpGsPDPLrI)

假设您正在开发分支上编码。您会收到一个通知，说生产分支有一个 bug。

您想要检查 bug，但是您不想丢失您在开发分支上创建的工作。你也不想提交你写的东西，因为它们还没有完成。

你是做什么的？你不能提交也不能切换分支。如果您切换分支，未提交的内容将会流到您切换到的分支。

您要做的是在切换到另一个分支时，将更改临时保存在某个地方。**Git stash 是临时存储。**

## 用饭桶叉起一个藏匿处

要使用 stash，您需要从一些未提交的代码开始。在这一课中，我们将使用下面这段代码作为未提交的修改:

```
<!-- Some uncommitted code in index.html -->
<main>
  <p> A new paragraph</p>
</main> 
```

要保存这段代码，你可以点击保存按钮。

[![The stash button](img/0f7aff5e11a2d4f246e237da93cfc830.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hFMhX7fP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/git-stash/stash-button.png)

一旦你点击了 stash 按钮，Fork 就会要求你留言。这条信息表明了隐藏的是什么。

因为 stashes 是临时的，你可以使用任何你想要的名字。我们称之为“临时存储”。

[![Naming the stash](img/cb610db4debd524e80cf6e1982c02259.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--c0DAVD6K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/git-stash/stash-name.png)

一旦你创建了一个新的贮藏，你会在侧边栏的贮藏部分找到它。

[![The stash can be found in the sidebar](img/f64af3634bb38c59108b3d31f19040b1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GaKvXIl6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/git-stash/stash-sidebar.png)

注意:您将无法看到这个存储中的变化，但这不是问题，因为您不必看到。你要做的是切换你的分支，完成你需要做的，然后切换回来。

在这种情况下，我们将检查到`master`分支。当你这样做的时候，注意你在`master`和`develop`分支中看不到我们上面写的未提交代码。

## 应用隐藏的更改

您可以返回到您所在的分支，然后右键单击您的存储并选择 Apply stash。

[![Applying a stash](img/74aab2b8f1e5b2cc7248e52c0e542678.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--P90aWdwO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/git-stash/apply-stash.png)

当你这样做的时候，Fork 会问你是否要删除隐藏的东西。我通常会删除存储，因为我不想一次保存多个存储。

[![Deleting the applied stash](img/8cd856e50d8add376c95926da8e9e372.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MhNnBHMY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/git-stash/apply-stash-2.png)

一旦你应用了存储，你将能够看到你所做的改变。

```
<!-- You'll see your uncommitted code again! -->
<main>
  <p> A new paragraph</p>
</main> 
```

## 包装完毕

Stashes 是你可以存放代码的临时仓库。当你存储了你的代码，你可以转移到其他分支去做别的事情。

当你完成后，你可以把你的代码放回原处。

有了 Git Stash，你就不用担心丢失任何未提交的更改了！

* * *

感谢阅读。这篇文章最初发表在我的博客上。如果你想要更多的文章来帮助你成为一个更好的前端开发者，请注册[我的时事通讯](https://zellwk.com)。