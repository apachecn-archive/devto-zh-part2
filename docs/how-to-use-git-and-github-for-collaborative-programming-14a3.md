# 如何使用 Git 和 GitHub 进行协同编程？

> 原文：<https://dev.to/ravirajsubramanian/how-to-use-git-and-github-for-collaborative-programming-14a3>

2018 年 10 月

*原贴于[中](https://medium.com/@ravirajsubramanian/how-to-use-git-and-github-for-collaborative-programming-955da6876aa9)T3】*

这是我在 Git 和 GitHub 上的基础教程的续篇。如果你是 Git 或 GitHub 的新手，我建议你从那里开始阅读。

## 目录:

*   组织
*   叉
*   拉取请求
*   代码审查过程
*   部署
*   标签
*   放
*   高级功能

在我之前的帖子中，我已经给了你 Git 和 GitHub 的基本概念。在这篇文章中，我们将看到如何提高对 [VCS(版本控制系统)](https://git-scm.com/book/en/v2/Getting-Started-About-Version-Control)的认识，并使我们的开发过程更加结构化，以便与社区或同事一起更好地协作编程。

好吧，我说的是“**一个社区**或者“**的同事**”。如果他们也想做贡献，他们如何能看到和使用你自己的云存储库？我们需要一个公共的存储库来放置我们的项目，这样其他每个想要参与这个项目的人都可以访问它。

我们如何做到这一点？开始了。

### 组织

该组织就像在 GitHub 中创建一个小组，就像在脸书、WhatsApp、Skype 等其他社交媒体或消息平台中的小组一样。

有什么比一群程序员在一个地方牛逼！💙

[![Photo of people at work fist bumping](img/877500866a03508509cea1dbd3896b4c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--X322DpS7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cdkqkboprlm29adqcs4w.jpg)

让我们从创建一个组织开始。

进入 [GitHub](https://github.com/) 或者[主页点击这里](https://github.com/)可以看到以下画面。

[![GitHub homescreen](img/1f5c59c5e798ff245e158f5ee66a90e1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QZCvg08O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qn81ijxls98grv0eenov.png)

如果你不是黑客，你必须先登录才能看到上面的屏幕。😬

在右上方，你有这个`+`图标，点击它将打开这个下拉窗口。

[![Picture of the new organization option in the drop-down](img/33c2bd72117102fcca3f4fb77808aa86.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--H07op1V5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8v2h0dcu3w3epkr8yzkz.png)

你已经有想法了，不是吗？如果您还没有，您必须点击“**创建新组织**来创建一个新组织。😑

填写一些基本信息，如组织名称、电子邮件 ID 等。到目前为止，您可以在自由计划中创建它，因为我们不打算创建任何私有项目。当您为您的公司或某个团队创建一个组织，并希望在其中保留您的项目隐私时，您必须在此步骤中设置付款设置。

我们现在不需要添加额外的成员，你可以在以后的时间点随时添加。只需填写其他必需的详细信息，并完成新组织的创建。

干杯！现在你有了自己的组织。😎

组织可以持有项目(也称为存储库)和 GitHub 的现有成员作为它的一部分。因此，我们必须在组织中创建一个项目，让其他人作为一个团队与我们一起工作。

等什么？让我们创建一个。

在您组织的主页中，您可以看到一个名为“ **New** ”的按钮。单击它将打开一个窗口，您可以在其中填写一些细节并创建一个存储库，就像用我们自己的帐户创建存储库一样。唯一的区别是，存储库归组织所有，而不是归您所有，尽管它是由您自己创建的。这是什么意思？这意味着只有对您的组织拥有管理员访问权限的人才能对这些存储库做任何事情，而其他成员则不能。

别担心。目前，您是贵组织的唯一管理员。

您不能将代码推送到您刚刚在自己的组织中创建的存储库中。

是的，你没看错。如果你真的想和你的团队、朋友或同事一起工作，你就不应该这么做。

我之前提到过，回购是机构所有。这意味着每个参与项目的成员都必须知道发生了什么。你不应该改变代码中的某些东西，然后直接推它。你需要提出修改建议并作为提案提交。这样，其他成员将审查代码，我们可以在每个人都批准时推送代码。当组织像许多开源组织一样发展时，只有授权的管理员会批准不破坏应用程序的更改。

好吧，他们是怎么做到的？这就是分叉发挥作用的地方。

### 分叉

fork 只不过是存储库的副本。

我们叉吧！

在贵单位项目的首页，可以看到一个名为“**叉**的按钮，在右上方，如下图所示。

[![Picture of Fork option](img/64fd0d4fe7c4bda4ff3f871f2d49c8f4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--V29vKp93--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lxxikwcy3ol8srommnky.png)

当您在组织中分叉刚刚创建的回购时，它会询问您要在哪个帐户下创建此分叉。点击你的帐户，你会看到你的用户名和你的个人资料图片。现在，您的帐户下有了贵组织回购的副本。被称为机构回购的分叉。

你知道吗，你是这个复制库的主人。(尽管它继承了组织回购的一些属性，如安全配置等。)

fork 和你自己的仓库有什么区别？

该分叉始终链接至其他组织的原始回购协议以及作为父组织的其他组织。因此，如果你想提议对他们的回购协议进行代码更改，你可以很容易地从 forks 中给出，因为它显然知道自己是母公司。

您不能从您在帐户上创建的其他存储库中向任何其他存储库提议代码更改。事情不是这样的。

您可以创建任意数量的分支，并通过提交来更改项目中的任何内容。

但是，我们如何建议对组织的回购进行这些更改呢？继续寻找答案。

### 拉取请求

Pull request(也称为 PR)是对父回购协议提出代码更改的方法，甚至是同一回购协议中一个分支到另一个分支的方法。

每当你工作于一个特性或者修复一个 bug 或者甚至一些随机的代码变更时，通常你会提交代码并推送到你个人存储库的云上。在组织的回购中，你不能简单地推动你的改变。您必须提出变更，组织的其他成员必须审查您提出的变更并将其与组织的回购合并。

因此，当代码库出现问题时，您不会是唯一一个承担责任的人。😝

但是,“拉”请求不仅仅是一个变更提议。这是一个专门讨论提议的代码更改的论坛。如果变更有任何问题，或者需要讨论一些代码变更，组织成员可以在 pull 请求中发布反馈，甚至通过推送后续提交来调整代码。

让我们看看这是如何运作的。

将您的 fork 存储库克隆到本地机器上。创建一个新的分支，命名为`feature/first-proposal`，在分支中修改一些代码，然后将提交推送到云中的分支。

如果你不知道怎么做，参考我的[上一篇文章](https://dev.to/ravirajthedeveloper/what-is-git-and-github-and-how-to-use-github-2mb1)。

现在在浏览器中打开您的 fork 存储库。您可以看到一个下拉菜单来更改项目，如下所示。

[![Picture of list of branches drop-down](img/cd417065f40f6a2206858e64d7796326.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FO5nxkIU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g079upf40l2a4uy3b9ng.png)

现在通过在下拉菜单中选择`feature/first-proposal`转到您的新分支。

让我们通过点击分支选择下拉菜单旁边的“**新拉取请求**”来提出一个拉取请求。现在，它将只是预览您将要提出的拉请求。您可以更改任何细节，修改拉请求名称，并更改您建议代码更改的父分支。一旦你确定了所有的细节，点击“**创建拉动式请求**以确认该提议。

您已成功向组织回购提交拉取请求。现在，我们需要做的一切就是让组织的任何其他成员通过共享此 PR 的链接来审核此拉动式请求。从浏览器地址栏复制就可以了。

### 代码评审流程

当您提交 PR 时，组织的其他成员会检查您的代码，以确保您没有通过添加一些额外的代码或删除一些代码来破坏项目的任何现有功能。😝

反之亦然，你将不得不审查你的队友的代码，这样你就会知道项目正在进行中。

那么我们该怎么做呢？

点击如下所示的“ **Files changed** ”选项卡，将打开一个页面，显示您或您的队友更改的所有文件中的所有代码更改，以一些颜色代码显示。

[![Picture of button of Files changed](img/a2924d8afc62a144bce9357f4a5c716b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KrhIMHTC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ao9ggrdegawxk7jzp77c.png)

[![Picture of the differences of code changes with red and green color](img/35d21795a0511f0d039067f0c34f53d1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ECRX-V6O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cbvs6z7ywgnxe82zmz7s.png)

在上图中，显示为绿色的代码是已经添加到项目中的代码，显示为红色的代码是从项目中删除的。此外，你可以在每一行前看到`+`或`-`符号，这是不言自明的，与颜色代码的目的相同。

审查者必须检查所有的代码变更，看看代码中是否有明显的错误。如果有任何错误或某些事情需要不同地完成或遗漏，评审者可以对请求留下评论。你不需要去任何地方发表评论，GitHub 已经为你做了一个很棒的功能，你可以在任何一行留下评论，它会为以后查看 PR 的任何人指出确切的行。

当您将鼠标悬停在代码上时，您可以看到一个带有`+`(加号)图标的蓝色按钮出现在该行开始之前，单击它将在同一行打开一个评论框。你可以输入任何东西，标记组织中的任何人，你还可以像任何其他评论框一样做更多的事情。甚至表情符号也支持。😉

然后，请求者将通过电子邮件和 GitHub 通知获得关于他们 PR 中评论的通知。甚至评论者也可以在任何聊天中通知请求者，以引起他们的立即注意。PR 中的对话可以继续，或者可以进行相关的更改，并通过额外的提交推送到同一个分支。

在请求者处理完评审者留下的所有评论后，现在“ **Files changed** ”页面将被更新，评审者必须再次检查这些更改，以确保在下一次提交中不会发生更糟糕的事情。😝

请阅读[这篇文章](https://hackernoon.com/the-art-of-humanizing-pull-requests-prs-b520588eb345)，这是一篇关于如何以礼貌友好的方式完成拉取请求和代码审查过程的有趣文章。

一旦一切正常，审核者必须通过点击“**审核变更**”按钮批准拉动请求，并在选中“**批准**”单选按钮的情况下提交，如下所示。

[![Picture of pull request approval option](img/482c915b9219a929b436d00973979228.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6TuCpCia--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ax0dplfn7psx9kzuvgo5.png)

通过点击“**合并拉取请求**按钮并确认，将拉取请求与组织库的主分支合并，如下所示。

[![Picture of merge pull request button](img/5a3305253fb076de6580be2474ace8c3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Gw2PSNFJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f9q2n7n5pcv4vbqzszoc.png)

除非进行了配置，否则拉式请求无需获得批准即可与组织回购合并。因此，您可以自己检查代码更改，并将其与主分支合并，而无需批准。但理想情况下，不建议这样做。

### 部署

在组织的项目中，我们将通过 FTP 或一些容器化工具(Docker 或 Kubernetes)将代码部署在云上的某个地方，通过任何服务提供商，如亚马逊 Web 服务、Azure Cloud、谷歌云平台等等。

每当我们进行更改时，我们可能希望将代码部署到在任何云服务中创建的任何服务器实例。

如果你以前做过或者听说过，那是完全没问题的。我们现在不打算这样做，因为这是一个需要专门学习的广泛话题。

如果 DevOps 团队或其他人在您的组织中做这些工作，现在正是您学习它并与他们一起在这个过程中工作的时候，因为它也越来越成为开发人员的必备技能。

我们不打算介绍部署步骤，因为每个服务提供商在其官方网站和其他在线资源上都有大量可用资源。我们只看如何使用 git 存储不同版本的代码库，以及如何区分最新发布的版本以简化部署流程。

### 标签

标签不过是命名项目的一个发布版本，并在任何阶段以某种压缩格式保存整个项目文件作为备份。

过程类似于分支。

让我们通过在项目目录中打开一个终端窗口，运行下面的命令来创建一个标记。
`git tag v1.0`
*v1.0* 是这里标签的名字。它就像一个分支名称，你可以输入任何东西来代替它。

让我们将这个标签推到云中的 fork 存储库中。
`git push origin v1.0`
上面的命令会将标签推送到你的 fork，这意味着你已经在你的云中备份了项目文件。它可以随时被取回和恢复。当部署的服务器中的某些东西崩溃时，您可以利用这些标记回滚到项目的前一个版本。通常，标签被推送到组织的存储库中，而不是任何人的 fork repo 中，以保持标签在组织范围内可用。

那么，我们如何恢复一个推到云端的标签呢？

如您所知，命令`git fetch origin`将获取所有分支。它还会将所有标签下载到您的本地机器上。

通过执行`git tag`，可以看到所有的标签。它将列出本地机器中所有标签的名称。

比方说，你做了很多提交，然后转移到其他分支，你的产品负责人/经理要求你回滚到云服务器中开发环境的 *v1.0* 版本。

您可以使用上面列出的命令从云中获取所有标签。您可以使用`git checkout origin/v1.0`恢复一个特定的标签，就像签出到一个分支一样。它将从标签 1.0 版中恢复您备份的代码。现在，您可以继续从本地机器将代码部署到服务器。

### 发布

多次部署之后，您或您的团队成员将无法记住何时以及为什么部署了哪个版本。此外，我们无法跟踪服务器中的当前版本。因此，我们需要在某个地方手动跟踪它，或者我们简单地做以下事情来跟踪项目的已发布的实时版本。

您可以找到一个名为“**发布**的选项卡，如下所示。

[![Picture of releases tab](img/51a57028fb10646e651d6f5b317adb91.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lHgpgPHz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dth59za3j6y2t0r9ydsv.png)

点击如下所示的“**标签**标签，查看到目前为止已经被推送到您的云存储库的标签列表。

[![Picture of Tags tab](img/bc074460afd90c502b8254bb3768d11e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LbMYbf_6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/26zpy59j6gh88cbp6vzk.png)

选择要命名为最新版本的标签。然后点击右上角的**编辑标签**按钮，如下图所示。

[![Picture of option to edit tag](img/cc9d108c2ca00cd7e960ca314f514b51.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7y4O0bX3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9rni3yaqqgsjb5v7d91q.png)

现在它会要求你填写一些细节。别担心，这不是求职申请，它只有几个字段需要输入。😉

在“**发布标题**”字段中输入任意名称，与标签名称相同即可。然后输入关于这个版本的摘要，如果你愿意的话，让你的队友知道这个版本，否则留空。

继续并提交发布。

现在，您可以在“**发布**”页面中看到顶部标记为“**最新发布**的标签的详细信息，如下所示。

[![Picture of details of latest release](img/a9e36ac0942b7ba7699e6719c8fb29ef.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hGm-IK1d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lvynknm81z45pxpr9pzb.png)

每次有新版本需要部署到云服务器时，您都可以遵循相同的步骤来推送新标签。

厉害！这些天来，我们已经完成了组织中协作编程所遵循的基本工作流程。

### 高级功能

以下是 Git 中的一些高级特性，您可以使用它们来使您的开发过程更加智能。

*   使用`git stash`功能将代码更改保存在内存中，而不会提交或丢失

*   使用命令`git rebase`编辑提交消息，将多个提交压缩为一个，删除分支中的一个提交，以及更多“Rebase”功能。

*   查看提交的全部日志，甚至使用`git log`命令更改代码。

*   通过使用`git cherry-pick`命令，在当前分支中使用任何特定的提交

*   使用智能过滤选项查看代码变化，如提交作者，或代码中的特定单词，以及使用`git blame`命令查找过去的任何提交

上述所有命令都必须与更多的选项一起使用，才能真正发挥作用。在使用它们之前了解它们，并确保你不会破坏代码库和丢失任何东西。

如果你喜欢这个帖子，就喜欢吧。如果你真的觉得有用，甚至可以把它加入书签。😉

让我们在社交平台上联系，

[GitHub](https://github.com/ravirajthedeveloper) ， [LinkedIn](https://www.linkedin.com/in/ravirajsubramanian) ， [Twitter](https://twitter.com/ThisIsRaviraj)

快乐编码。👩‍💻🙂

- [Raviraj Subramanian](https://about.me/ravirajsubramanian)