# 循序渐进地学习 Git 和 Github

> 原文：<https://dev.to/ows_ali/learn-git-and-github-step-by-step-18gm>

嘿！在这篇文章中，我将向你展示如何开始使用当今最好的版本控制软件，不是别人，正是 Git。

如果您是从编码开始，或者您已经知道编码，但是以前还没有使用过 Git，那么是时候向您介绍这种奇妙的技术了，本文是您开始 Git 之旅的良好开端。

**为什么这篇文章值得一读:**

1.  这篇文章不会只告诉你语法，我会把重点放在这个命令背后的概念/工作原理上。

**为什么还要做 Git/Github:**

1.  维护/管理项目的最佳方式。您可以随时随地访问您的项目
2.  当你和一个团队一起工作时，Git 会小心处理处理相同文件和其他冲突的问题
3.  几乎所有的开源世界都知道 Git，因为代码对每个人都是可见的，人们也会对你的代码感兴趣
4.  它是免费的

这是我在 Medium 上的第一篇帖子，所以可能会有一些错误或不符合 Medium 的标准，所以请原谅我。

够了。让我们从这里开始真正的东西。

**创建 Github 存储库**

让我们从一个简单的 PHP 项目开始，它位于你所说的' E:\ '驱动器和目录' E:\xampp\htdocs\testing '中。(见下图)

目标是在 Github 上上传这个项目。

你需要在 Github 上有一个帐户，当然这个帐户是免费创建的，所以如果你还没有的话，你必须注册。登录后，点击“新建存储库”，您将看到如下屏幕:

[![](img/b0f4230c1556e921aa8fac991d8bcc7f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--J6fDMkBC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/694/1%2Afsa8yWFmIo4kVNGCzzF5RA.png)

添加您选择的存储库名称(如果需要，添加描述)，然后单击“创建存储库”。

现在，打开命令提示符，在“开始”菜单中键入“cmd ”,将目录更改为“测试”目录，如下所示:

[![](img/413005e177d745bc8d758309bcd7f15c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YIZJrcX2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/668/1%2AQD5Rz3nusJwuTb1wLx8PjQ.png)

键入“dir ”,你可以在下图中看到我们只有一个文件“index.php ”,我们将把它推送到 Github

[![](img/c3aa565c70894dc7c58fc75017fa7287.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iNQQr6Nu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/675/1%2AjX30hbUV0QIc8bpIvDgIpA.png)

**本地 Git**

现在，你需要告诉“testing”文件夹这将是一个 Github 项目。因此，您需要启动“testing”文件夹作为本地 git 存储库。这个简单的命令就可以做到

> 去吧，林特克

它将在测试文件夹中创建一个. git 文件夹，这意味着您现在可以开始使用远程 Github 项目了。你可能会想，我应该如何告诉 git 我在 Github 上的存储库在哪里，所以下面这个命令告诉你 Github 存储库的 url 是什么:

> git 远程添加原点[https://github.com/ows-ali/testing](https://github.com/ows-ali/testing)

在您的情况下，ows-ali/testing 将替换为[您的用户名]/[您的存储库名称]

现在，你知道了

> 去把它给我。

这将分阶段存放您的文件( *add* 命令意味着您准备好提交 git，然后推送它，文件可以在添加后删除，因此这个额外的层在很多时候会很方便)。

现在，了解这个命令没有什么用处:

*   git 添加。将所有编辑过的或新的文件添加到 stage
*   *git 添加 index.php*只会将 index.php 添加到舞台上，最终只会被推送到 Github

现在，您已经准备好提交了

> git commit -m“第一次提交”

提交是将本地 repo 中的更改带到 Github 上的远程存储库中(一个提交可以包含一个文件或多个文件中的更改，或者文件的创建或删除等)

所以，最终最期待的将你的代码放到 Github 上的命令是:

> git 推送原始主机

现在继续，检查您的存储库，index.php 将在您的存储库中可见，它看起来像这样:

[![](img/3ffa962408c8b225df7296a35707c638.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_lHx6wWf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/723/1%2AZQNPVEwOYRpbmGDg7rzrxw.png)

**自述文件简介**

现在，在上面的图片中，你可以看到它要求你创建一个自述文件。等等！这不在计划中，我没听说过，可能我的项目也没有。别担心，这是另一个关于 Git 项目的漂亮而重要的文件，理解它是完全值得的。README.md 是一种 markdown 语言，你可以用它来描述你的项目，你也需要遵循特定的 markdown 风格来编写。哦，但是就这样吗？不，README 文件最棒的地方在于，每当你在 Github 上打开你的库时，Github 会自动显示 README.md 文件，如果有的话。很好，对吧。

因此，您可以按照上述步骤返回并创建一个文件，然后推送它，但我们也可以直接从存储库中创建文件。因此，点击“添加自述文件”,添加您的内容，如以下内容或您喜欢的任何内容:

[![](img/c86ad6e662f9b90bd97e964e73ac3f03.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--a5-C0s3Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/683/1%2AUG2CB4MIPTc5rJE1lYNrIQ.png)

单击预览，您可以在最终确定之前看到更改:

[![](img/3b0ff849d0eb5366e83adab8d768143b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rjOXD-Ot--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/736/1%2A_SiG_ayYz89q8XHBm2awoA.png)

你可以在这里学到一些造型:

*   # is equivalent to

    # tags in html (it doesn't need to end, although it's the same as in HTML)

*   ##相当于

    ## 等等

*   [文字](link)行为类似于

看起来很好，现在继续输入您的提交消息(记住提交用于将所有类型的更改带入存储库)并单击 commit。

[![](img/27173cf93e8b3f3d74b00764e8034db2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--b2b-nCtA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/717/1%2AW-nELDyvKWz8BjJIma9Xww.png)

现在，您的存储库将如下所示

[![](img/661650d4a42c18b058ea7910b6c7b2f5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zMeBy_d3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/888/1%2Au0w2Is9xK9e9dMjcZ9xyTA.png)

可爱！看起来好多了。

好吧，等等，有个问题。对！这个 README.md 文件不在您的本地计算机上。嗯（表示踌躇等）..

**从远程 Github 库拉取**

拉的命令来了。打开项目目录中的命令提示符，键入

> git 拉取来源主机

[![](img/826c5594f1d9a29262cace0b702e0aea.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mH_HTtjl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/667/1%2AcpgKvBuYeROJuOYXi9CdoQ.png)

似乎很棒。现在，远程和本地存储库是同步的。我们的文章到此结束。如果你喜欢，你可以喝杯水。

**超越**

如果你知道这些，你可以继续学习:

*   [分支](https://learngitbranching.js.org/)
*   [Github 机器人](https://github-bot-tutorial.readthedocs.io/en/latest/)
*   github pages
*   [哲基尔主题](http://jekyllthemes.org/)等等

我希望能写更多关于如何为开源做贡献的文章，敬请关注。

这是我的第一篇文章，但你已经知道媒体有一个精彩的鼓掌功能，对不对？:)

编码快乐！