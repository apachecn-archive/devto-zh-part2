# 将 GitHub 作为一个团队使用

> 原文：<https://dev.to/dandevri/using-github-as-a-team-44m0>

> 第一次使用 GitHub 有一个陡峭的学习曲线。在团队中使用 GitHub 完全是另一回事。以下是我在团队项目中了解到的一些最佳实践。

在我参与的各种 OSS 项目中，有一件事总是比我预期的花费更多的时间。**设定一个 Git(Hub)标准**,用于处理一个存储库(或者酷孩子所说的 repo)。下面是一些最佳实践，它们在我的成长过程中确实帮了我。

[![O really](../Images/e4e9b049d161d2eeb91de81ab4b14b46.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DFGFZXmx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ws0bwu66es7n8kg0kefv.jpeg)

## 保护你的树枝

防止以后头疼的第一件事就是保护好`master`分支，马上做一个`develop`分支。禁用**强制推送**，防止树枝被**删除**。这是保护您项目的一个好方法，并确保您不必这样做🚑例如，如果您使用持续集成，可以修复任何东西。

## 一致的分支命名

有了`master`分支，受保护的贡献者不能直接进入`master`。他们不得不用树枝做`pull requests`。为分支提出一个命名约定非常有帮助。其中之一就是使用前缀:

*   `fix/...`
*   `feature/...`
*   `enhancement/...`

在快速概览中，您可以看到每个分支的用途，它们有一致的命名。

## 拉取请求

确保没有必要的审查，PR 不能被合并。如果您保护一个分支，您也可以启用`Require pull request reviews before merging`。每一份进来的公关都要经过另一个投稿人的审核。

因此，默认情况下，合并是被阻止的。贡献者必须手动导航到 PR 并点击`review changes`。然后，您可以:

*   提出**意见**以获得总体反馈。(没有明确批准)
*   **批准**变更和请购单可以合并
*   **请求变更**，在合并之前必须解决的事情。

这是在代码合并之前进行快速质量检查的好方法。

*   它保持了代码的整体质量。
*   更多的眼睛👀更好。别人可以发现你可能忽略的错误。
*   而且我个人觉得看别人的代码是学习的好方法。

## 提交消息

哦，上帝，提交信息。总是一个令人痛苦的话题。每个人都有个人/偏好的方式去做。Chris Beam 在 2014 年写了一篇关于这个的文章。这是一些通用规则，但大多数人认为它们是可以接受的。

> 你绝对要看[全帖](https://chris.beams.io/posts/git-commit/)。

理想情况下，您的提交标题应该是这样的:

```
Add styling for navigation bar 
```

Enter fullscreen mode Exit fullscreen mode

通常这些规则在`contributing.md`中概述，并给出例子。

## 计划您的提交

罪名是编写大量代码。但是，计划提交也是控制自己的一个好策略。在你直接开始构建特性之前，试着把特性分解成小步骤。每一步都代表一次提交。[这里](https://dev.to/rpalo/plan-your-commits)是 dev.to 上关于这个的一个简短帖子。

* * *

提出一个一致的 GitHub 标准和流程可能需要一些时间，但从长远来看是值得的。这使得回购更容易维护，也让我免于恐慌。

> 感谢阅读！你可以在 [@dandevri](https://dev.to/dandevri) 上关注我，如果你有什么想说的，随时联系我！