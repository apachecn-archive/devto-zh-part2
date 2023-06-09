# 从 Git 遥控器中提取

> 原文：<https://dev.to/zellwk/pulling-from-a-git-remote-4l63>

[https://www.youtube.com/embed/6x7CGpEKUXo](https://www.youtube.com/embed/6x7CGpEKUXo)

注意:这是 Git 初学者系列的第四个视频。[在这里看第一个视频](https://zellwk.com/blog/setting-up-git)。

当您对本地存储库进行更改时，您可以将更改推送到 Git remote。同样，当远程发生变化时，您可以将变化拉回您的本地存储库。

今天，您将学习如何将数据从远程设备拉回到存储库。

## 对遥控器进行更改

通常，对遥控器的更改是由处理同一项目的另一个人进行的。他们在自己的计算机上修改代码，然后将代码推送到远程存储库。

一旦远程存储库发生变化，您可以将其拉回本地存储库以获得更新的版本。

这是标准的工作流程。

但是，由于我是一个人在做这个项目，我将向您展示如何直接在 Github 上更改远程存储库。一旦完成，我们就从那里撤出。

## 直接更改 Github 库

假设我们想要更改`README.md`文本。

为此，您可以单击自述文件旁边的铅笔图标。这将把您带到一个编辑器，您可以在其中更改文本。

[![A text editor shows up after you click the pencil icon](img/bc313763103fdbe8fcc3afcb80c4955e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---cNAgTd2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/git-pull/edit-readme.png) 

<figure>

<figcaption>Github 的编辑</figcaption>

</figure>

一旦你完成了。向下滚动到底部，并编写一条提交消息。你可以点击绿色按钮直接在 Github 上提交修改。

[![Committing the changes](img/6d7202cce1d51ec8b85fa2149449e8a2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MxKfL5fv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/git-pull/edit-readme-commit.png) 

<figure>

<figcaption>提交修改</figcaption>

</figure>

项目将被更新。

## 抓取变化

Fork 和其他 Git 客户端可以向您展示对远程存储库的更改。他们通过一个名为 Git Fetch 的命令来实现。

你可以通过点击向下的空箭头来自己取数据。它是左上角最左边的箭头按钮

[![the fetch button](img/e0c03c65460c52be089be0ce076dfef1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8-W9HUhc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/git-pull/fetch.png) 

<figure>

<figcaption>取按钮</figcaption>

</figure>

Fetch 检查远程存储库是否有任何更改。就像一个邮件客户端说你有三封邮件要看。

一旦获取完成，您可以在 Git 历史中看到,`origin/master`在`update README.md`提交上，并且`update README.md`提交比我们的本地主分支早一个提交。

[![`origin/master` tag is one commit ahead of the `master` tag. ](img/a8aa94cd72a5b83dc222ab6d1ee20c3b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zStQgWTC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/git-pull/history-1.png) 

<figure>

<figcaption>`origin/master`标签比`master`标签提前一个提交。</figcaption>

T9】</figure>

在侧边栏上，你可以看到我们的主分支旁边的数字 1，以及一个向下的箭头。这告诉我们，我们的分支比远程操作晚一次提交。

[![Sidebar shows a number 1 and a downwards arrow](img/81ce5f77a49160262a402fbe759492ff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--g8Xi7fX0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/git-pull/branch) 

<figure>

<figcaption>边上的主分支告诉我们同样的信息——我们的主分支是远程后面一个 commit。</figcaption>

</figure>

## 拉动变化

要更新您的本地分支机构，您可以单击“拉”按钮。下拉按钮是左上角的实心向下箭头。是取和推之间的那个。

[![The pull button](img/c7f93535a3374e8c08c25034f2cefec7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sXuzfuas--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/git-pull/pull.png) 

<figure>

<figcaption>拉动按钮</figcaption>

</figure>

当您单击“拉”时，您将能够选择您想要拉的分支。因为我们之前已经跟踪过它，所以您可以通过再次单击 pull 来直接拉动主分支。

[![Menu that opens up after clicking pull.](img/c4e3ab000065addb8245a4673ffcbba9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mTEoJEUB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/git-pull/pull-menu.png) 

<figure>

<figcaption>再次点击拉动即可拉动变化</figcaption>

</figure>

当您将分支从远程拉到本地存储库时，您会看到`master`与`origin/master`移动到相同的提交。

[![`master` and `origin/master` are on the same commit again](img/5a1ea1f2502ab8b59011e01165bf2c84.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--A1o-Yp1z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/git-pull/history-2.png) 

<figure>

<figcaption>`master`和`origin/master`再次进行相同的提交。</figcaption>

T9】</figure>

## 包装完毕

Fetch 检查远程存储库中是否有任何更改。

“拉”将变更从远程存储库带到您的本地存储库。

* * *

感谢阅读。这篇文章最初发表在我的博客上。如果你想要更多的文章来帮助你成为一个更好的前端开发者，请注册[我的时事通讯](https://zellwk.com)。