# 在不停止生产的情况下，使用 kubernetes 从 Heroku 迁移到 AWS

> 原文：<https://dev.to/msedzielewski/migrating-from-heroku-to-aws-with-kubernates-and-without-stopping-production--49le>

几个月前，我们成功地将大部分基础设施从 Heroku 迁移到 AWS。现在，当尘埃落定(或者我应该说是云)时，我们想分享我们决定背后的主要驱动力是什么，以及我们如何在不停止 Voucherify API 的情况下进行转移，哪怕是一分钟。

## 架构概要

为了更好地理解我们这里的推理，让我们快速地看一下什么是 Voucherify 以及这个架构是什么样子的。

Voucherify 提供可编程的构建模块来构建优惠券、推荐和忠诚度活动。它基本上是一个 API 优先的平台，开发者可以用它来建立复杂和个性化的促销活动，比如当客户加入“高级”细分市场时，向他或她发送一封带有特定优惠券代码的电子邮件。它还允许公司跟踪优惠券的兑现，以找出什么样的促销效果最好。最后，它为营销人员提供了一个仪表板，以减轻开发人员维护和监控促销活动的负担。

该平台基本上由 3 个组件组成:

*   公开 API 的核心应用程序
*   服务于仪表板的网站
*   支持非 API 相关作业的微服务
*   说到数据存储，我们使用 Postgres、Mongo 和 redis trio。

这是它迁移后的样子:
[![](../Images/18f10f67302916e61550005e35c2ed2e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ispv-cgU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wvxl8q1o22kr3jh9z10h.png)

**负载**:我们为 100 多个客户服务，他们每月发送几百万个 API 调用，包括常规请求和一些更耗电的请求，如批量导入/导出或与第三方集成的同步。‍

为什么首先是 Heroku，为什么我们要迁移？在我们的[博客](https://www.voucherify.io/blog/migrating-from-heroku-to-aws-with-kubernates-and-without-stopping-production?utm_campaign=tofu&utm_medium=post&utm_source=devto)上阅读更多信息。