# 个人项目的项目管理生命周期

> 原文：<https://dev.to/ignoreintuition/project-management-lifecycles-for-personal-projects-4f62>

在我的 web 开发生涯中，我已经启动了几个开源项目。在那段时间里，我在特性、维护和缺陷管理方面取得了不同程度的成功。我一直认为创建一个合适的项目是多余的，这意味着没有一个项目遵循一个一致的标准。在我当前的项目[中，我尝试遵循敏捷管理方法。当我完成下面的工作流程时，我很好奇其他人的成功程度如何。请在下面的评论中分享。](https://github.com/ignoreintuition/v-chart-plugin/)

# 项目概述

只是为了给你一个项目的大小/规模的感觉:这是一个可定制的 Vue 组件插件，用于添加绑定到 Vue 的反应数据的图表和图形。计划是有大约十几个图表，具有多元支持、目标跟踪、可定制的小部件(标签、标题等)以及通过 CSS 和 API 的额外定制。

我大约在一个月前开始这个项目，目前我正处于 0.1 预 alpha 阶段，计划在 2018 年 11 月推出 0.2 alpha。目前我是唯一的贡献者，但我一直在积极寻找其他人。从长远来看，期望是一个稳定的插件将允许贡献者使用 API 集成他们自己的图形。这些图表可以包含在将来的版本中，扩展可用的总图表库。

# 项目

开始这个项目时，我决定遵循一些基本的 Git 最佳实践。这意味着不需要直接推送到 Master，所有特性都有自己独特的分支，所有新代码都将通过 PRs 合并到 Github 中。为了组织这些，我在 GitHub 中为每个 bug、特性、增强等创建了一个问题，并相应地标记了它们。我还为那些类型的任务创建了一些 backlog / tech debt 项目(比如一般优化、错误处理、文档)。那些也相应地被标记。

[![issues](../Images/8d6dc702773bbf08258a9a55a6995ff5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MSnxs75D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4jf3qs2mbz1irx9l0f1h.png)

每当我开始开发一个新特性时，我都会在 Git 中创建一个名为 issue-{x}-{short-description}的分支。从这里开始，我将结束一系列分支，如下所示:

[![branches](../Images/70b3bba0f9def7dd247021bae9ac0d01.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FeosDJsb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sp6phzkggc5i158uv8xh.png)

然后，我可以单独处理这些分支中的每一个，当其他功能完成时，偶尔会从 Master 中合并更改。当特性完成时，它们可以被推回到原点，并通过 Github 中的 PR 合并到 Master 中

为了管理这个项目，我把它分成了几个冲刺阶段，更大的项目由三个为期两周的冲刺组成。

[![Projects](../Images/45ecf348436e3d150081771c08fd856d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ieGisx7L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hczxxhvsyddfgmm98scy.png)

最后，我给我的每一次冲刺分配问题。

[![sprints](../Images/bd30510b1f08cac008fbe00ba594405a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6qyFHYAO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/auo349ozygvophbf6o48.png)

这让我可以为每个功能设置截止日期，并让我专注于交付功能和解决问题。到目前为止，它一直工作得很好，允许我专注于单个功能或错误，而不会被所有打开的项目淹没。这也让我能够相当有规律地推动变革。我担心的是，随着项目变得越来越大，可能会有更多的人开始从事这项工作，这是否会扩大规模。它还会因为围绕变更的附加过程而阻止人们做出贡献吗？或者这种类型的工作流是一种受欢迎的附加吗？