# Slack 应用目录开发者评论👨🏻‍🏭

> 原文：<https://dev.to/yvoschaap/the-slack-app-directory-developer-review-34go>

> 这是我根据自己从应用开发到发布的经验回顾应用市场的系列文章的第一篇。

在第一次应用市场回顾中，我将拆开 [Slack 应用目录](https://slack.com/apps)。在 Slack 应用目录中发布我的应用是迄今为止所有市场中最好的体验。Slack 为开发者提供了一个非常好的开发和应用提交流程。

Slack 是一种工具，它允许团队以一种介于即时消息和电子邮件之间的方式进行交流。Slack 声称在 5 万个团队中拥有超过 800 万的日活跃用户([2018 年 5 月](https://techcrunch.com/2018/05/08/slack-hits-8-million-daily-active-users-with-3-million-paid-users/))。

Slack 应用目录于 2015 年推出，目前已经发展到 700 多个应用([2017 年 10 月](https://techcrunch.com/2018/05/08/slack-hits-8-million-daily-active-users-with-3-million-paid-users/))。应用程序可以通过广泛的 API 深度集成到 Slack 客户端中。最常见的用法是实现(聊天)机器人。

<figure>![](../Images/fbb2385a86d55e7f3c84e74033ef157f.png)

<figcaption>Trello example: I can add tickets to my board from within Slack with their chatbot.</figcaption>

</figure>

Slack 表现出对其应用生态系统的高度承诺，这可以从他们广泛的应用营销、持续的开发(最近推出的[行动](https://slackhq.com/turn-your-conversations-into-action)和[工作区应用](https://api.slack.com/workspace-apps-preview))、投资(例如 8000 万美元 [Slack 基金](https://slack.com/developers/fund))和广泛的沟通(例如[文档](https://api.slack.com/)、[公共路线图](https://trello.com/c/LqzgzTJq/20-welcome-to-the-slack-platform-roadmap-read-more-here)、 [Twitter](https://twitter.com/SlackAPI) )中推断出来。

| 一目了然 |  |
| --- | --- |
| 公司 | 松弛的 |
| 数据调节器(Data Adapter Unit) | 8M |
| 应用程序 | 700+ |
| 要求 | 松弛 API 集成 |

> **请求**:你的 app 是否被列入 Slack App 目录，并且欣欣向荣？跟我分享你的[经历](mailto:yvo@yvoschaap.com)。

## 市场:接触数百万用户？

Slack 将其应用市场命名为[应用目录](https://www.slack.com/apps)。从主 Slack 客户端可以很容易地访问 App 目录。每个团队(一组用户)可以免费安装多达 10 个应用程序。只有付费计划的用户才能安装更多应用。据报道，有超过 300 万用户(37%来自 DAU)——不幸的是，更多关于有多少球队付费或应用程序使用的统计数据没有公开。

<figure>![](../Images/b3b5258938333393da6cc7589593a514.png)

<figcaption>Get Essential Apps, that is a strong CTA.</figcaption>

</figure>

市场 UX 和用户界面是经过深思熟虑的。它在视觉上很吸引人，呈现的应用看起来也很新鲜(不像 Chrome 网络商店那样)。主页面应用程序选择强调手动应用程序管理，顶部是工作人员挑选的部分(分为功能、基本和新&值得注意)，每个应用程序类别的顶部挑选突出了 3 个管理的应用程序。

与其他市场相比，免费、免费增值或试用应用之间没有明显的区别。

> **Slack 建议**:市场应该明确区分免费和付费应用，这将有助于过滤掉没有预算支付应用的用户。

此外，它也没有明确标明谁是应用程序开发者，或者它是否是一个官方的应用程序。同样缺失的还有用户评分(不像 [Shopify](https://apps.shopify.com/) )或应用评论。在我看来，这两个重要的细节都是让用户在安装应用程序之前看到的——并且允许这个应用程序有很多潜在的侵入性权限。

### 浏览:

<figure>![](../Images/d96dd8a8459561e1e4e526a107bdf538.png)

<figcaption>Staff Picks list curated apps, and every category also highlight a top 3.</figcaption>

</figure>

*   应用程序按徽标、应用程序名称和单行描述列出。这为列出相对多的应用程序(30)提供了空间，这易于扫描，并为应用程序发现提供了有吸引力的布局。
*   分页只允许下一页/上一页，这使得用户很难导航。
*   浏览时不能改变排序(不像搜索)，这使得发现最新的应用程序更加困难。
*   一大加分；列表不是由像谷歌或苹果这样的主要品牌主导的，这给了初创公司和(独立)应用程序开发者更大的机会受到关注。
*   搜索效果很好。UX 和用户界面包括搜索自动完成，关键字突出，单行结果和无尽的滚动结果。

作为应用程序开发者，用户不清楚应用程序列表是如何排名的。没有显示统计数据。我认为这是基于应用程序的安装数量。如果排名的运作能更加透明就好了，尤其是对开发者而言。

### 应用程序页面:

<figure>![](../Images/bae5db857f03b97fe9d2732c953b5ee1.png)

<figcaption>Well designed, but maybe a bit too clean?</figcaption>

</figure>

*   应用程序页面也设计得很好:干净，有一个大的应用程序徽标和一个明显的 CTA 按钮(“访问网站以安装”或“安装”)，要求用户安装应用程序。
*   截图是应用程序页面的核心。你可以包括 Youtube 视频，但视频缩略图的质量非常低(我的支持者 T2 的推文)。
*   应用程序描述在可见空间上有点太有限，必须手动展开/折叠。
*   应用程序权限被清晰地描述出来，对用户来说可读性很强。但是每个权限对您隐私的影响程度并没有明显的区别。
*   不幸的是，没有安装或使用统计可用。缺少的其他细节有:定价、应用程序开发人员详细信息以及用户评论或评级。

### 应用程序安装:

安装流程很简单；安装⟶授权⟶应用程序登录页面。然后，应用程序有权访问该团队的 Slack API。

> **成长提示:**如果你安装了应用程序，你的应用程序机器人应该会发送一条欢迎消息和使用提示，然后直接出现在 Slack 客户端的用户面前。

默认情况下，任何团队用户都可以为整个 Slack 团队安装任何应用程序。这降低了安装新应用的门槛。这是一个很棒的特性。但对于团队管理员来说，这也意味着其他人可以授予权限，允许应用程序读取聊天历史，查看所有成员的详细信息，等等。

市场经验总结:

| 👍 | 👎 |
| --- | --- |
| 干净清爽的市场 | 每个团队只允许 10 个应用程序(免费) |
| 优秀的应用程序发现工具 | 没有使用统计或评论 |
| 优质应用程序管理 | 付费/免费/试用应用之间没有区别 |
|  | 可能是 app 页面太干净了？ |

参见' [Marketing](#) '了解更多关于如何让你的应用在疲软的市场中成长的信息。但是首先，应用程序开发...

## 发展:1-2-3

App 开发从 [Slack API](https://api.slack.com/) 开始。在这里，Slack 再次显示了它的奉献精神。文档是广泛的、最新的，并具有清晰的 API 请求/响应示例。在文档旁边，一个有用的 [FAQ](https://api.slack.com/faq) ，许多[教程](https://api.slack.com/tutorials)和大量的[库](https://api.slack.com/community)...

> 开发技巧:在开发之前，在 Slack 市场上研究类似的应用，看看它们是否以及如何维持自身(付费计划、病毒式增长等)。)!

Slack API 是成熟稳定的。大量的[方法](https://api.slack.com/methods)可用，允许访问几乎任何你需要的东西。 [Webhooks](https://api.slack.com/incoming-webhooks) 工作非常出色。[费率限制](https://api.slack.com/docs/rate-limits)很宽松。和 oAuth 2.0 进行权限处理。

我没有听说过应用被杀或者重要的[端点](https://api.slack.com/changelog)被弃用(不像[推特](https://techcrunch.com/2018/04/06/twitter-delays-api-change-that-could-break-tweetbot-twitterific-etc/))，这些都是可持续应用生态系统的好迹象。

一个问题可能是，有太多的选择来挂钩 Slack，如果你开始开发，这一切都有点势不可挡。

> **开发技巧:**在一个有几个应用程序设置的团队中研究 Slack，以广泛了解什么是建立良好接触点的基础。

但总的来说——在我看来——这是一个非常高质量的产品，开发者可以在上面开始构建。

<figure>![](../Images/422e61710d8e3b2231cc6ba498bb5b80.png)

<figcaption>This is how you do documentation people!</figcaption>

</figure>

### 仪表板:

仪表盘是您维护应用的地方。仪表板响应迅速，包含维护应用程序的关键功能，如 oAuth 凭证。Slack dashboard 包括撤销密钥、admin 2FA 和多帐户访问等高级功能。

> **开发提示:**有一点令人困惑的是，在创建应用程序之前，你必须先选择一个松散的团队。这很快会导致应用消失，如果你在创建应用时没有登录到特定的团队。建议，先专门为你的 app 开发创建一个新的 slack 团队。

### 提交:

Slack 允许任何使用 Slack API 的应用程序在其应用程序目录中列出。入职是经过深思熟虑的，有一个清晰的 36(！)项。

<figure>![](../Images/9b88523144677fb8af29afd4593e0e38.png)

<figcaption>The submission process takes you by the hand and demands you to confirm every checklist step that is mandatory before you can submit your app for approval.</figcaption>

</figure>

一个提交怪癖是要求精确的屏幕截图尺寸(1600 x 1000)。修正截图尺寸是一个痛苦(请调整大小和处理裁剪)！

> **开发提示:**草图和 Photoshop 的设计模板可用，这是开始使用正确字体和尺寸的好方法。

你不能在提交之前预览你的应用程序页面，这将导致不必要的重新提交，例如由于打字错误。也不清楚你是否可以在描述中使用 HTML 或 markdown，我最终发现这是一个定制的 [Slack markdown 变体](https://api.slack.com/docs/message-formatting)。应用程序细节的任何变化，需要重新提交和手动审查。

> **成长秘诀:** Slack 有巨大的 SEO 价值，他们在他们的目录中传递，所以确保你链接回你的网站。

从提交(到修改我的隐私政策)到[出版](https://www.slack.com/apps/AB35T2M1S-teampage)只用了 **7 天**。

发展经验总结:

| 👍 | 👎 |
| --- | --- |
| 伟大的 API | 没有计费集成 |
| 出色的文档 | 交互式方法请求(测试) |
| 快速提交流程 | 固定屏幕截图图像尺寸 |
| 公共路线图 | 缺少应用统计数据 |
| 许多可用的库 | 没有个人联系方式 |

## 营销:到月球？

每个 Slack 客户端都可以快速访问 Slack 应用市场。虽然 Slack 声称有 700 多个应用程序，但新的应用程序仍然可以在市场上获得黄金房地产，因为最明显的位置是由他们的团队策划的。

> **成长秘诀:**加入[联系 Slack 的开发团队](#)以获得特色。

实际的市场流量是未知的，它驱动的每日安装数量也是未知的。我确实发现提到“超过 200 个应用程序有超过一千家公司在积极使用它们”( [Jul，' 17](https://medium.com/slack-developer-blog/giving-apps-a-new-home-in-slack-daa2ba3a75ed) )。

仅凭市场列表很难确定你的应用能吸引多少安装量。我的一般经验是在商店里获得你的应用，不会让你的业务一夜成名(因为不要指望 Slack 会让你成功)。这适用于任何应用市场。所以确保你的期望是现实的。

这款应用 [Standuply](https://medium.com/slack-developer-blog/from-zero-to-25-000-mo-bf7caddea44d) 在被放在 marketplace 主页上之后声称“两周内(我们)有 750 个新注册用户”。我认为这对于市场上最显眼的位置来说并不令人印象深刻。Pull Reminders 还提到了来自市场的“T2”新用户流。

没有明确的病毒式工具来扩大你的安装基础，因为 Slack 团队是独立的筒仓，你不能在其他团队之间交叉推广你的应用。这使得你的病毒增长依赖于口口相传。

我已经确定了三种类型的应用程序，每一种都有针对 Slack 的潜在价值:

1.  **到达**:你有一个现有的 SaaS 或应用程序，并希望通过整合 Slack 来增加你的受众，并到达新的客户。这些应用程序可能发现不了多少价值。例如[团队页面](https://slack.com/apps/AB35T2M1S-teampage?next_id=0)。
2.  **功能**:你有一个现有的 SaaS 或应用程序，你的用户很闲，希望他们能从那里使用你的工具。例如[特雷罗](https://trello.com/platforms/slack)或者[韦转会](https://slack.com/apps/AAB31CEKS-wetransfer)。这些应用通常更成熟，松散集成只是另一个特征。
3.  **焦点**:你有一个为 Slack 定制的 app。在这种情况下，你有更多的机会让你的应用融入生态系统，并获得正确的增值部分。例如[海塔克](https://www.heytaco.chat/)和[单立](https://medium.com/slack-developer-blog/from-zero-to-25-000-mo-bf7caddea44d)。这些应用程序投入更多以使其正确，但也更加依赖(和有限)Slack 来增长。

> **我的经历**:3 类页面上市后(如媒体&新闻@ # [25](https://slack.com/apps/category/At7KK5H6AZ-media-news) )。到目前为止，我的列表没有带来任何显著的流量&安装。毕竟炒作是令人失望的。我必须指出，我的应用程序有一个小的松弛集成(导入团队成员)，所以结果会有所不同。我已经请求了一个突出显示的位置，这仍然是待定的(更新:很快)。

Slack 的营销经验:

| 👍 | 👎 |
| --- | --- |
| 用户可以轻松访问市场 | 没有推广所有新应用的地方 |
| 策展可以突出你的应用程序 | 没有性能统计(再次) |
|  | 没有超越单一团队的病毒工具 |

## 结论:你为懈怠而建设吗？

很明显，Slack 对他们的应用生态系统投入了很多。应用程序紧密集成到 Slack 本身。它为谷歌日历集成这样的主流事物和智能 Scrum 机器人这样的新兴事物提供了空间。

任何使用 Slack 的人都会遇到应用程序(轶事)。从应用程序开发的角度来看，Slack 是我见过的最好的体验之一。他们的营销接触点为发现和安装新应用创造了大量机会。

这一切似乎为懈怠带来了回报。但我确实认为 Slack 现在应该通过允许应用程序开发人员利用 Slack 的计费功能(类似于 [Cloudflare](https://www.cloudflare.com/apps/developer/docs/payments-and-billing/products) 或 [Github](https://developer.github.com/apps/marketplace/pricing-payments-and-free-trials/) )来让它更上一层楼。让应用程序开发人员制定定价计划，并让 Slack 处理支付事宜...

此外，Slack 应该开放应用程序的使用，以推动更多的开发人员采用。开发者的成功故事是有限的[可用的](https://medium.com/slack-developer-blog/3-7m-taco-emojis-later-1ea1799101af)，如果你与 Shopify 的“16.2 万美元是我们最顶尖的 25%应用开发者的平均年收入”相比，这里有很多收获。

> 反馈:这整个评论是实验性的，所以让我知道你的想法或分享来支持未来的帖子。

## 下一步是什么...

在 Twitter 上关注我@ [yvoschaap](https://twitter.com/yvoschaap) 。

### 关于

👋嗨，我是 Yvo Schaap，一个来自阿姆斯特丹的 32 岁的企业家，喜欢编码和构建伟大的东西。见我专业标注的[背景](https://yvoschaap.com/annotate.html)。在 yvo@yvoschaap.com 的T3 取得联系。

* * *

这是一篇编辑评论。我绝不隶属于 Slack 或由 Slack 支付。