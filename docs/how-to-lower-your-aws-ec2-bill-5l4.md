# 如何降低您的 AWS EC2 账单

> 原文：<https://dev.to/liquid_chickens/how-to-lower-your-aws-ec2-bill-5l4>

因为在云中启动虚拟机非常简单，所以每月的账单也很容易增加。几台虚拟机的每月费用可能从几百美元开始，但很快就涨到了数万美元。

在较大的企业中，我看到这种情况变得如此糟糕，以至于管理层抓狂，将所有东西都搬回内部，即使增长是合法的，不是浪费或管理不善的结果。

开发人员和运营人员也有一种倾向，认为他们的基础设施和应用程序的财务方面“不是我的工作”，这种想法让我的大脑因其产生的愤怒而受伤。

(诺益)诺彡

(诺益)诺彡

(诺益)诺彡

翻了三张桌子，我还是写不出“不是我的工作”这个角度。它将不得不等待另一篇博文。现在，让我们假设你，亲爱的读者，是一个最低功能的成年人，理解拥有你所建造的东西的概念。

## 大小合适

当您构建基础设施时，测试仪器以了解利用率总是一个好主意。大多数人认为这是故障排除和监控的必备工具，但也有成本问题。

假设您已经有了基础设施，降低 AWS EC2 成本的第一步是确定您实际需要什么。

您可以使用您应该已经通过 AWS(或您选择的云提供商)收集的性能和库存数据，推出您自己的解决方案来调整您的基础架构，或者您可以使用像 [CloudHealth Tech](https://www.cloudhealthtech.com/) 或 [CloudCheckr](https://cloudcheckr.com/) 这样专门为成本优化而构建的服务。

SaaS 解决方案做了许多与成本优化相关的事情，但它们提供的主要内容之一是利用率报告和建议。

也许你认为你的应用程序需要 50 个 4.2 倍大的镜头。也许你真正需要的是 25 个 t2.mediums(一个月 14k 美元的差价)。第一次运行利用率报告时，我发现差距如此之大。

你可能会想“了解他们的应用程序的人绝不会让这种事情发生”，这在大多数情况下是正确的。问题是:

1.  很少有人真正了解他们运行的应用程序。
2.  很少有人认为运行这些应用程序的成本是他们自己的东西。
3.  与第一点相关，许多人没有运行他们自己开发的应用程序的奢侈。他们拿到了别人的劣质应用程序，并被告知要保持照明。

掌握实际利用率往往也有助于对话，如“也许我们应该花时间让我们的应用程序无状态，这样我们就可以自动扩展服务器”或“也许是时候将这个应用程序转换为 PaaS 了。”

_ **注意**:如果您最终将一批服务器切换到 t2 层，请确保您监控 CPU 信用。这一层有一个 [CPU 使用配额](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/t2-credits-baseline-concepts.html)，如果您超过这个配额，您的服务器将被节流，直到信用重建。我见过有人翻到 t2s，心想“它的规格和我的 m4/5 一样”，然后想知道为什么他们的高 CPU 使用率应用程序几个小时后就开始爬行了。_

## 购买基线

通常用来证明混合云合理性的一个论点是“我们有一个内部基准，所以我们将只使用公共云来实现爆发弹性”，这是合理的。然而，你可以在云中做同样的事情。

一旦您掌握了您的基线需求，[保留实例](https://aws.amazon.com/ec2/pricing/reserved-instances/) (RIs)就成了一个选项。用 RIs 将成本减半是完全现实的。

风险投资以较低的实例价格换取一定的灵活性，类似于你在世界其他地方看到的金融工具，如手机计划。

> “你可以在没有合同的情况下按月支付，也可以通过签订最低期限协议来节省 X%。”

与手机计划不同，这里有一个 RI 市场，如果你需要卖掉它们，你可以在那里转售你的 RI，并且围绕[还有许多选择，将它们转换成不同的实例大小](https://aws.amazon.com/about-aws/whats-new/2016/09/amazon-ec2-convertible-reserved-instances-and-the-reserved-instance-regional-benefit/)。将 RIs 转换成不同的实例族也是可能的，但是有点棘手。

_ **注意:**销售您的 RIs 有一定的限制，而且许多地区的市场容量不大，因此销售机会可能有限。因此，不要购买 100%确定可以转售的 RIs。_

RI 折扣主要与期限(合同期限)和预付百分比(无、部分或全部)相关。三年期全额预付风险投资将是最便宜的选择。

在过去的一年里，我学到了一个技巧，那就是许多像 SHI 和 CDW 这样的经销商为 AWS RIs 提供融资。因此，您可以与您的经销商在一个期限内分期偿还“预付”成本，而不是预先支付 1 年或 3 年的费用。显然，你要支付利息作为融资的一部分，但如果决定是购买 10%的无预付款 RI 还是购买 60%的全额预付款 RI，支付几个点的利息是显而易见的。

有趣的事实:还可以为 RDS 实例购买 RIs，这几乎总是轻而易举的事，因为与基础设施的其余部分相比，您的数据库服务器可能是“永久的”。

## 斑点实例是你的朋友

假设您已经购买了 RIs 基线，以上的任何额外实例将按照“按需”或“现货实例”价格。

通过 [spot instances](https://aws.amazon.com/ec2/spot/) ，您实际上是在对 AWS 的过剩产能进行竞价，因此定价是基于需求的高度动态的。因为“点”是多余的，所以当其他用户需要该容量时，他们将被拉回到 AWS 的库存中，并支付按需或 RI 费率。

当 Spot 实例对您正在做的事情(测试环境、容错应用程序、EMR 处理，如 Spark)可行时，它们是非常棒的。你可能会得到 50%的 RI 折扣，但我见过高达 80-90%的现货实例折扣。

有些人[做了一项惊人的工作](https://medium.com/cloud-uprising/the-data-science-of-aws-spot-pricing-8bed655caed2)分析他们需要的实例类型的历史现货价格，并拥有购买算法，帮助他们跟踪价格趋势，并在最便宜的时候购买&运行他们的实例。

_ **注:**这里有一个警告——现货价格采购是你要留意的。因为它是需求驱动的，现货定价有时(虽然不经常)会超过按需定价的价格。因此，您需要确保在您构建的任何自动化中已经考虑到了这一点。_

## 你没有被卡住

人们对其 EC2 环境的一个常见假设是，在那里运行的应用程序可以关闭或迁移到 PaaS 之前，成本“是什么就是什么”。幸运的是，这很少是真的。通过在您的配置中利用一些财务工具，您很有可能能够降低 EC2 环境的成本。

如果你在企业工作，这些东西会让商界人士喜欢你，他们很可能控制你的薪水。

如果你正在经营一家初创公司，这些东西会让你能够雇用更多的人，甚至继续经营下去。

这些都不难，只是需要有人来解释。