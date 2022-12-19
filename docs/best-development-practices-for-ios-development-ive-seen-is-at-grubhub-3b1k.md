# 我在 GRUBHUB 上看到了 iOS 开发的最佳实践

> 原文：<https://dev.to/tiptopgs/best-development-practices-for-ios-development-ive-seen-is-at-grubhub-3b1k>

### 我见过的 iOS 开发的最佳开发实践是在 GrubHub

[![](../Images/502ebadd23f98891f325d773df66b7dd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vmpDOfrY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AJrHDbEdqGsVfnBYtxOitcw.jpeg) 

<figcaption>照片由 [rawpixel](https://unsplash.com/photos/a2VqhP3d4Vg?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上 [Unsplash](https://unsplash.com/search/photos/development?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

鉴于 GRUBHUB 是我工作过的第一家科技公司，我分享的一些实践可能是有限的。在加入 GRUBHUB 之前，我在一些公司工作，在那里商业和技术实践通常是分开的。他们不一定彼此一致。通常情况下，企业有更大的发言权，因为他们控制着公司的财务。所以我喜欢在技术驱动的公司工作。这也是我第一次体验真正的敏捷开发过程。以下部分来自我作为开发人员的经历。

### 团队结构

为了使这个过程有效，每个人都必须在同一页上。我们案例中的利益相关者包括开发人员、产品所有者、分析代表和 QA 工程师。有时候，设计师是根据需要参与进来的。在 GRUBHUB，我们非常重视产品的质量。因此，我们选择实现数量更少、质量更高的功能。

### 产品管理

在 GRUBHUB，我们与产品所有者密切合作。产品所有者管理他们的票证的优先级，我们作为一个团队一起培训票证。整理流程每周进行一次，每次 30 分钟，偶尔会召开临时会议来整理需要紧急完成的额外票证。在会议期间，开发人员确定需求中的任何差距，并将任何单元测试和功能测试添加到组合中，因为开发人员还负责实现自动化测试，包括单元测试和功能测试。我们的 QA 团队做更多的 exploration 手动测试来检查整个应用程序的健康状况。他们有更全面的观点。这是 QA 团队参与整理过程的原因之一。它们有助于确保票据包含需要由开发人员自动化的用例。产品所有者负责提供足够的细节，包括验收标准、设计模型、API 请求、流程和测试数据。

在计划期间，开发团队的领导将根据产品负责人的优先待办事项来获取 sprint 的入场券，并相应地分配它们。在这一点上，任何经过整理并可用于规划的票据都将包含开发人员需要处理的所有细节。由于我们的冲刺周期大约是两周，我们都在船上，以确保门票有足够的信息在一周的短开发周期内完成。

### 发展管理

除非包含单元测试/功能测试并通过代码更改，否则不会创建拉请求。为了让开发团队实现这一点，我们在工作开始前进行设计评审，编码风格指南，持续集成系统，以及代码评审过程。

开发人员从他们各自的分支工作，并负责从他们的分支创建临时构建，以在合并到主分支之前完成任何验证。验证包括设计师的任何视觉 QA 测试和负责产品所有者的功能测试。

开发人员需要完成的其他任务包括在试运行环境中进行实验设置。需要注意的一个重要的关键点是，我们有一大块时间可以不受任何干扰地专注于开发。任何会议都安排在中午 12 点之前和下午 4 点之后。因此，这两个时期之间的任何事情都是为了发展。

### 发布管理

我们的发布周期是每两周一次，所以发布会议每天都要举行，以筛选任何新的缺陷并检查发布的状态。参加会议的人包括 QA 团队和开发人员领导。在此期间，他们还会对任何需要解决的关键错误进行优先级排序。任何超出范围的问题，包括产品所有者期望的额外变更，都会被放入 backlog 中，以便在未来的 sprints 中进行优先级排序。

我们的目标是最终将发布周期缩短到一周，随着我们完善上述流程，我们将能够实现这一目标，并更频繁地发布到应用商店。我们的过程是可预测的，为开发人员提供了一些稳定性来完成他们的工作，并按时交付。

### 从这里去哪里？

你在你的团队中经历过类似的最佳实践吗？你还发现了其他在传递质量方面有价值的东西吗？

最初发表于[如何在 Swift](https://www.howtodoitinswift.com/blog/2018/10/13/best-development-practices-for-ios-development-ive-seen-is-atnbspgrubhub) 中完成。