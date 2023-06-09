# 使用 Git 和 Github 进行代码管理

> 原文：<https://dev.to/joshichinmay/code-management-with-git-and-github-4072>

> Git 是人类历史上最强大的版本控制系统。Git 极其丰富的特性集使得代码管理变得简单明了。

所以今天，让我们讨论如何遵循一个简单的结构来管理我们的代码库。

### 发起-

当我们开始一个新项目时，我们会在 Github 上创建一个存储库。然后建立或创建一个`README.md`文件。现在，它可以是一个公共或私人项目。

[![create-new-repo](img/d0e3db31f311b009e179389a2f660576.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--quu0uP_N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w2zj2dam9toumqtxsv6g.png)

* * *

### 协作者和可及性-

之后，我们邀请我们的合作者进行合作。始终为协作者设置访问权限。例如，不是每个人都可以合并拉请求。只有代码维护人员或核心团队成员可以管理合并部分。

[![add-colaborators](img/5320c40e87b2ef3d766e091a0e2955d5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cSF2dzhm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ksv5781o22w207gm8hrm.png)

* * *

### 分叉——

每个协作者都有自己的克隆库，因此他们可以独立工作。当您派生一个存储库时，主存储库成为`upstream`，克隆的存储库成为`origin`。我们将代码推送到`origin/branch-name`，然后它被合并到`upstream/develop`。我喜欢保持我的主存储库干净；因此，我建议使用基于 fork 的开发方法。

[![fork-repo](img/e80446eb5319978e6d0d16bda2d06feb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--csF8HvXA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/34kpl5dvnzolens5ahwd.png)

> 最常见的是，forks 被用来提议对项目进行修改，或者使用其他人的计划作为你的想法的起点。

* * *

### 分支-

所以，当一个合作者想要做一些新的事情时，他们会根据他们将要做的工作类型创建一个新的分支。因此，他们可以独立地处理他们的功能或错误，而不会打扰其他合作者。
当你在 Github 上创建一个新的存储库时，默认情况下，你会得到一个`master`分支。通常，所有的开发人员或维护人员都会从 master 创建一个新的分支，命名为`develop`。

[![create-develop-branch](img/2c198b2fe5beb44e87f0eea09ce4aa3f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kArRYdp1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ksp4arytrncd72ffko4i.png)

应该是合并您的功能、缺陷和增强功能的主要分支。

1.  `master` -是生产部署的一个分支。
2.  是一个分支，在这里我们合并来自不同合作者的代码。
3.  是一个分支，在进入生产级别之前，我们在其中合并和测试我们的代码。一旦我们测试了所有的功能和部署周期，我们就将`staging`代码推入/合并到`master`中。
4.  未完成工作的分支——当任何合作者带着未完成的代码离开组织时，或者如果他/她想将他/她的所有权转移给其他人，在父库上用他的名字创建一个新的分支是一件好事。这样另一个合作者就可以从那里取出他/她的代码。分支名称可以是这样的，`collaborators_name/feature/feature_name`。

在主存储库上拥有(仅仅)这些分支是一个好的实践。所以它保持干净。在你的 fork 上，你可以根据你正在做的工作类型创建一个分支。我们可以遵循一个简单的技术以更好的方式识别分支，

1.  对于一个特性，`feature/new-editor`。
2.  对于一个 bug，`bug/time-zone-fix`。
3.  如果您对此有未解决的问题，请点击`issue/:issue_number/issue-description`。
4.  任何增强，`enhancement/background-color-update`。

很简单，不是吗？这些是全球开发人员遵循的行业标准或惯例。

* * *

### 拉取请求-

一旦工作完成，我们就创建一个从我们的分支到`upstream/develop`的拉请求。现在，在您的拉取请求上设置不同的内容是一件好事。

1.  **审阅者-** 审阅者是那些将要审阅您的拉式请求的人。您可以请求对拉取请求进行多次审核。
2.  **受托人-** 受托人是当前正在处理拉式请求的人。可以有多个受让人。
3.  **描述-** 解释拉取请求是关于什么的。您可以将问题参考编号添加到描述中。
4.  **标签-** 标签是向拉取请求添加标识的最佳方式。Github 已经给出了重要的标签。比如- bug，增强，特性，wontfix，求助。以便其他协作者甚至不用打开就可以识别拉取请求。
5.  **里程碑-** 通过添加`milestones`，您可以跟踪和过滤您的拉动请求。你可以在这里找到关于[的详细描述。](https://help.github.com/articles/tracking-the-progress-of-your-work-with-milestones/)

[![pr-labels](img/5d116e07b6ca84306d097ffc4ec58e28.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3R7fhJr---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c0n4zser01znyt4m1aua.png)

在合并拉请求之前，至少要和你的同事进行两轮代码评审。通常，我喜欢使用不同的标签，如`waiting for peer review`、`waiting for merge review`、`changes suggested`、`help wanted`、`ready for deployment`来跟踪拉取请求的状态。
除此之外，我喜欢添加不同的标签来了解拉取请求的类型。比如，`feature`，`bugfix`，`enhancement`。
您可以创建多个标签，以便根据您的流程更好地识别您的拉式请求。
Github 已经预定义了一些标签。

* * *

### 问题——

这是一个跟踪 bug 和改进的好地方。您可以集成 bug 追踪器来自动创建问题。问题在合作者之间共享，他们可以在这里讨论解决方案。有一个受托人选项，你可以标记不同的合作者。

* * *

### 发布-

无论何时您合并从`staging`到`master`的任何拉请求，它都是一个释放。本质上，如果你能跟踪你的发布，这是一件好事。每当我们将代码合并到`master`中，我们就将计数器加 1。现在，很容易跟踪您的版本。你可以在这里阅读更多关于语义版本[的内容。](https://semver.org/)

生成版本号的简单解释是，`MAJOR.MINOR.PATCH`

1.  **主要-** 重要版本和功能。
2.  **MINOR -** 当你改进现有功能时，提高代码质量。
3.  **补丁-** 小改动，bug 修复。

这就是你如何用 Github 来起草新闻稿的方法。简单又有用！

真实世界的例子是 Bootstrap

[![bootstrap-releases](img/6bffb51712ff1d0847b478332a17b8ea.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4o7tWC3D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sfohkkydewtraei9c852.png)

* * *

在合并拉请求之前，您可以使用不同的工具来使您的代码健壮。比如，

1.  在云上运行测试用例。
2.  运行 linter 来修复代码质量。

嗯，就这样吧！我有我提到的所有东西的文档。如果你想要，请在下面的评论区写下你的电子邮件。我很乐意与你分享它。