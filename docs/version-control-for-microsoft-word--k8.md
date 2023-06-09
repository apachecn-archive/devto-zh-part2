# Microsoft Word 的版本控制

> 原文：<https://dev.to/simuldocs/version-control-for-microsoft-word--k8>

### 将 Git 和 GitHub 的特性引入 Microsoft Word

## 短版

[Simul Documents](https://www.simuldocs.com) 是一个协作工具，将 Git 和 GitHub 的所有令人惊叹的特性带到了 Microsoft Word 的世界。它是为那些和其他人一起从事长期文档工作的人设计的:律师、记者、学者、作家等等..

## 加长版

我来自一个软件开发背景，由于编写代码的性质，我们不得不拿出一些伟大的工具来合作。这背后的主要驱动力是能够在不影响其他人的情况下对文档(或者在我们的例子中是一段代码)进行更改。一旦我们对我们的更改感到满意，我们就必须将我们的工作与可能同时更改了同一个文件的其他开发人员的工作合并，以最终创建一个内聚的文档。

Git 和 GitHub 在我们的行业中取得了巨大的成功，但是它们的平台仅限于纯文本(不支持 Microsoft Word 提供的丰富格式)，这使得它们不适合许多其他行业。

在 Simul，我们想把这些非常有用的特性带到不起眼的 Word 文档中。

## Git 和 GitHub 如何帮助？

Git 有一个“主”分支的概念。这是文件的真实来源。

无论作者何时对文档进行更改，他们都是在自己的分支中进行的。这可以防止他们的更改影响到其他人。一旦完成，他们将尝试将他们的更改推送到主版本，此时他们将需要执行合并并小心地解决任何冲突。

随着作者的合作，这个过程一遍又一遍地继续。在此过程中，代码中的标签或检查点会被标记出来。这些用于表示已经测试并可以部署的代码版本。

这与 Word 文档的创作过程有很多重叠。你不一定想要微软 SharePoint 或谷歌文档提供的实时合著体验。，这可能会限制您确定谁对内容的特定更改负责的能力。分支提供了更清晰的变更审计线索。与代码一样，可以添加标记来表示文档的次要或主要版本已准备好发布。

GitHub 是一个位于 Git 之上的接口，提供额外的社交功能，如评论和提出问题，极大地改善了协作。Simul 旨在提供两种产品的优势。

## 太好了，为什么我不能直接用 Git？

Git 的主要限制是它只能处理纯文本(想想 Windows 记事本)。我见过作者为了利用 Git 而求助于纯文本或 markdown (LaTeX ),但这意味着丢失所有丰富的格式，比如图像和字体。对于公司来说，这通常是一个很大的牺牲，因为格式需要在每个出版物中重新应用，这非常耗时并且容易出错。

第二个障碍是 Git 是一个由非常专业的用户设计的工具。大多数操作是通过命令行执行的，这使得它不适合一般用户。

## 那么，Simul 是如何弥合差距的呢？

一旦在 Simul 中创建了一个文档(这可能是一个新的 Word 文档或上传的文件),所有的更改都会被跟踪。

在这个例子中，我上传了一个名为“示例文档”的非常原始的文档，其中包含单词“快速的棕色狐狸跳过懒惰的狗”。点击“在 Word 中打开”按钮，Microsoft Word 启动，我将单词“dog”替换为“cat”。保存更改后，Simul 将显示以下内容:

[![Simul shows any changes similar to Word’s Track Changes feature](img/cdc11506002ce5c107ec65cb9fea9a87.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PmOc0Fwc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.simuldocs.cimg/git-blog/001.png)

这里有几件事值得注意:

*   最初的版本 0.0.1 显然已经被更新的版本 0.0.2 所取代

*   可以清楚地看到作者是谁，修改的时间以及他们插入或删除了多少文本块

*   最重要的是，我可以确切地看到做了哪些更改。删除了“狗”一词，插入了“猫”一词。

无论文档中的“修订”是否打开，都会发生这种情况。这不仅显示了文档的更改，还保证了没有其他的更改被偷偷执行。

相当直截了当；但是，如果我们的朋友“James”也和我们同时进行了更改(或者只是决定编辑以前的版本)，我们将会看到一个新的分支被创建:

[![Simul introduces the concept of “Branches” to Word](img/9f4a3077df764092e684b9d6e34e266d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lh7pnPYg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.simuldocs.cimg/git-blog/002.png)

修订树现在显示 James 对 0.0.1 进行了更改，但不包括我们对 0.0.2 所做的更改。

如果我们看看詹姆斯的变化，我们可以看到他把“狐狸”一词改为“狼”。注意，这个句子仍然是指懒狗而不是猫。

[![Simul allows you to comment on changes](img/d19008c2aa22c6ac2475573da5c160fa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cdz-_0td--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.simuldocs.cimg/git-blog/003.png)

我们也可以评论詹姆斯的改变；确保所有影响文件的沟通都被记录并可访问。

然而，我们现在的情况是，同一份文件有两个不同的版本。我们可以通过使用上面的蓝绿色面板合并我们的工作来缓解这个问题。

[![Simul can merge branches resulting in a new version with both revisions as Tracked Changes](img/f9885960613d6e596910acdf470bb518.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JmxjMZYy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.simuldocs.cimg/git-blog/004.png)

通过合并，创建了一个新版本(0.0.4 ),它包括 Ben 和 James 的修改。每位作者的修改将作为常规修订包含在 Microsoft Word 中:

[![You can resolve and conflicts of a merge using Word’s Tracked Changes](img/76203f7ce7dff47db7ee8d3f44548198.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8RVJl5mo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.simuldocs.cimg/git-blog/005.png)

现在可以像往常一样接受或拒绝这些更改，我们有一个单一的“最新版本”供作者贡献。

Simul 提供了比上面演示的更多的特性。我不会详细讨论它们，但是如果你想了解更多，你可以阅读我们的指南[什么是模拟？](https://www.simuldocs.com/support/getting-started/what-is-simul)。

## 高级功能

一旦文档的基本草稿完成，通常需要做两件事情中的一件。标记文档的主要版本，并授予一组查看者只读访问权限。或者需要创建一个副本来接收一组作者的反馈，这些作者不应该访问主版本。

在 GitHub 中，这是通过标签、发布、分叉和拉取请求的组合来实现的。

Simul 允许将特定版本标记为具有只读权限的次要或主要版本，并且还支持[副本](https://www.simuldocs.com/blog/ben-morris/document-copies)，这允许一部分用户协作并提出更新主版本的请求。你可以在这里阅读更多关于这个功能[的信息。](https://www.simuldocs.com/blog/ben-morris/document-copies)

## 我们真的需要这一切吗？这只是一个 Word 文档

我们绝对需要它。一个很好的例子是一群来自不同公司的律师试图起草一份协议。这通常是一个非常低效的协作过程，涉及通过电子邮件发送多个副本，并且必须手动合并更改。

这不仅耗费更多的时间，而且容易出现人为错误——很难确定什么是最新版本，一旦更改被接受，所有的历史都将丢失。更不用说作者故意试图在不被注意的情况下插入条款，或者欺骗一方同意错误的合同版本。

有很多人在这个问题上挣扎的例子，但是到目前为止，所有的解决方案都要求用户放弃 Microsoft Word，使用 LaTeX 之类的纯文本。在 Simul，我们决心开发一个支持您和您的首选创作工具的平台。

## 哦，还有我有没有提到 Simul 是免费的？

[Simul](https://www.simuldocs.com)是一项免费的入门服务，我们也有[付费计划](https://www.simuldocs.com/pricing)可用，其中包括更复杂的功能和对您的文档的更大控制。我们将很快推出企业版，允许您在现有的 IT 基础设施上运行 Simul。

你可以在这里浏览一下这篇文章[中描述的所有功能。](https://www.simuldocs.com/tutorial)

*最初发表于[www.simuldocs.com](https://www.simuldocs.com/blog/ben-morris/version-control-for-microsoft-word)。*