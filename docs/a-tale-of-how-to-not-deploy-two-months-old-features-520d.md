# 如何不部署两个月前的旧功能的故事

> 原文：<https://dev.to/brunoluiz/a-tale-of-how-to-not-deploy-two-months-old-features-520d>

[![Photo by rawpixel.com on Unsplash](../Images/5307edb38fd09e7be3413dffaa600a37.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PSXwbhDd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://brunoluiz.net/blog/2018/mar/a-tale-of-how-to-not-deploy-two-months-old-features/cover.jpg)

在电子商务和零售市场上，有一个重要且非常特殊的日子，叫做黑色星期五。对许多人来说，它意味着“折扣价格”，“出售！”，“五折”，但对于开发者和 IT 人来说，这是一次具有挑战性的冒险。

首先，两周前，特性部署被冻结，一切都通过了负载测试。在这一周，机器会按比例增加，在活动当天，所有人的目光都集中在指标和日志上。

但是这整个故事中有一个陷阱:功能开发不能停止。这意味着特性部署冻结后，特性将很快开始堆积，通常在事件发生后还会持续一段时间，尤其是当您的软件是多租户的，并且每个客户都有自己的黑色星期五日程时。

在这篇文章的标题中，部署被延迟到临近圣诞节的时候，因为是圣诞节，团队倾向于不部署到下一年。

一月过去了，在某个时候，推迟的部署将不得不开始。自从黑色星期五以来，已经过了四次 sprint，通常的过程是自动部署每个 sprint 包，在生产环境中监控它的结果。但这，这不是一个理想的世界。

[![Photo by rawpixel.com on Unsplash](../Images/1b5b56cd28afc96467d359bb43693d27.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fMTC1KNc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://brunoluiz.net/blog/2018/mar/a-tale-of-how-to-not-deploy-two-months-old-features/surprises.jpeg)

## 产品开发生活充满惊喜

当一个产品处于早期阶段时，第一批客户是技术早期采用者，开发团队必须接近他们，因为他们的投入是有价值的。

在一月的某一天，一个主要平台的客户通知他们将在系统上推出更多的商店。除了通常需要做一些准备之外，最大的问题是需要一个特定的特性。它已经被开发出来了，但是还没有经过很好的测试。

更糟糕的是，这个特性依赖于其他特性，在 11 月底和 12 月初实现，这意味着“再见原子部署”:团队将不得不准备一个包含所有四个 sprint 特性的部署包。很危险，不是吗？还记得*挑战冒险*那部分吗？

在完成和润色所需的特性时，QA 团队必须小心翼翼地再次测试所有特性，以确保一切都相当顺利。即使是那些已经测试过的补丁，因为从 11 月份开始，一些补丁已经在生产环境中发布了，谁知道它们是否会影响已经测试过的特性。

特性完成后，QA 和管理团队一起测试它。经过一段时间的测试后，他们确认它运行正常，没有任何错误或业务规则缺陷。尽管这个令人惊讶的特性让我们疯狂地工作，但部署看起来就像是在公园散步。

[![Photo by SpaceX on Unsplash](../Images/adba5036226778e3a7ce520c6dcd334f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VRZ0ZOOo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://brunoluiz.net/blog/2018/mar/a-tale-of-how-to-not-deploy-two-months-old-features/deploy.jpeg)

## (e ploy)D-天

整个部署开始时是凌晨 2 点。低流量和没有那么多订单涌入系统使它成为部署应用程序的最佳时机。在部署它的时候，一切看起来都很好，尽管需要一些小的调整和迁移。时钟一敲到凌晨 3 点 30 分，队员们已经在互道“晚安”了。

对于一个有很多功能和一个非常关键的功能的包来说，它是非常顺利的…这就是事情开始分崩离析的时候。下午 4 点，其中一个客户开始抱怨由于 HTTP 400 错误，一些订单没有注册，一些库存没有无缘无故地更新。_“CK 到底是怎么回事！”*每个人都绝望地说。

所有开发人员都停下来调查这个问题，当然，隐藏在一个 sprint 发布包中间的是两个 bug:一个与应用程序如何处理库存队列生成器有关，另一个与如何验证地址有关。十行代码的麻烦。

大约一个小时的紧张之后，一切又恢复了平静，但是这次部署的结果改变了整个团队。

[![Photo by Dustin Lee on Unsplash](../Images/52285f9e0063e31557876b1878f88dae.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bUnZyOb7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://brunoluiz.net/blog/2018/mar/a-tale-of-how-to-not-deploy-two-months-old-features/postmortem.jpeg)

## 表示剖验及其结果

不久之后，验尸报告写好了。每个人都合作:开发人员、经理和 QA。这份文件中包含的信息将使团队能够学习、成长，并更好地了解如何在未来处理此类问题。

前几天，安排了一个晨会来讨论验尸报告中的观点。除了部署本身的问题之外，在激烈的讨论中还出现了许多其他问题。所有成员都同意指出的问题，并建议采取一些行动来解决这些问题。

### 部署年龄

人们可以指着代码说这是一个 bug，但不仅仅如此。太久的部署是一个问题，因为它有太久以前开发的特性，开发人员和测试人员可能不记得这些特性的所有细节。这可能导致数据库问题(如果忘记了迁移)、模式验证问题、业务规则缺陷等等。

解决这个问题的最好方法是在 sprint 完成后不久部署特性包。对于所有团队成员来说，一切都将是新鲜的，不需要 git 噱头(主要是 rebase hell)。

### 仓促部署

不要仓促部署，尤其是大型的。团队会有压力，沟通可能不是最好的，文档(比如发布说明)可能会有错误或者遗漏信息。在这个部署中，有一个特性没有在 JIRA 发布版上指定，但是在 GitHub 发布版上有。有更多时间的开发人员会注意到缺少了一些东西。

这些发行说明通常不仅由客户使用，也由 QAs 使用，因为它将包含将要部署的特性。因为这些对每个人都有影响，所以最好的办法是对什么可以完全交付(开发人员方面)和什么可以很好地测试(QA 方面)达成共识。这个想法是为了避免那些因为匆忙而产生的可能有缺陷或者没有被很好测试的特性。

### 创建流程

通常，人们喜欢抱怨“流程”。有些人甚至说他们喜欢创业公司，因为他们的缺乏。但它们的存在是有原因的，在这种情况下，如果一切都遵循一个明确定义的过程，就会有所帮助。

已经有了一个过程，但它不是那么具体。事件发生后，团队聚在一起编写了一份文档，描述了产品功能开发的每一步，并提供了每一步应该做什么的管道:功能讨论和开发、同行评审指标、测试环境的部署和发布说明、测试流程，最后是生产部署。

是不是更官僚？肯定的！但是到目前为止，团队成员，尤其是客户没有任何抱怨。

### 更多自动化测试

QAs 在一个测试环境中测试了每个 sprint 特性包。最好的场景是有许多不同的自动化测试场景，这将 100%保证没有任何东西会破坏 API。

但是，这不是一个完美的世界。通常是让它的一部分自动化，一部分由人来测试。但是，由于这种老化部署是紧急的，而且是仓促进行的，一些功能没有经过很好的测试就通过了。

如何解决这个问题？嗯，问题的一个方面是组织:一个板工具将有助于这一点，但由于老化部署，如这一个有太多的任务，最好的事情是组织发布说明。QA 团队将能够计划如何进行测试。

另一方面，稍微复杂一点，就是创建更多的自动化测试。不仅是 QA，还有开发者。每个人都知道有人想要跳过测试开发，但是这可以帮助避免无数的问题(特别是在未来)。其他开发人员不应该允许一个特性在没有自动测试的情况下继续运行。这同样适用于 QAs。

### 错误度量、监控和警报

在部署后不久，该团队已经检查了一些关于 [Graylog](https://www.graylog.org) 和 [CloudWatch](https://aws.amazon.com/cloudwatch/) 的错误度量，但这些错误度量只是针对系统的一部分，而不是整个系统。如果监控了更多的指标，问题可能早就被发现了，影响也会小一些。

团队应该通过仪表板甚至应用程序日志来手动监控这些指标中的至少一部分，但是解决这个问题的最好方法是通过自动警报。如今，所有主要的 IaaS 提供商都有度量和日志服务。有了 CloudWatch (AWS ),团队可以在应用程序在某个确定的时间段给出太多错误 HTTP 响应时设置警报。它可以发送电子邮件、短信，甚至可以与手机应用程序集成，如 [OpsGenie](https://www.opsgenie.com/) 。

除此之外还有其他工具，如 [NewRelic](https://newrelic.com/) 和 [Librato](https://www.librato.com/) ，但它们更具体一些，团队应该分析它们是否真的需要。

[![Photo by Kupono Kuwamura on Unsplash](../Images/f3680efe7cbe0941a2366f59288ea46f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rHXD4t1c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://brunoluiz.net/blog/2018/mar/a-tale-of-how-to-not-deploy-two-months-old-features/noproblems.jpeg)

## 问题结束？

这不会是这个团队需要努力解决的最后一个问题。像这样的压力时刻会出现，但这是团队学习和成长的时候，随着时间的推移，能够上升并朝着高风险的目标前进。