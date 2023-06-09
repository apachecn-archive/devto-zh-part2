# 凤凰涅槃-开源密码交换平台

> 原文：<https://dev.to/skatkov/phoenix-reborn---open-source-crypto-exchange-peatio-2pdg>

[![Crypto exchange](img/41eeef30300e2bece865b9968addd344.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bEe6ZeaA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4juecuzy6jqzcfo8vva3.png)

我喜欢帮助那些拥有 ruby 的公司。Upwork 帮助我找到了新的线索。我是就业版块的常客，现在我开始注意到一些趋势- [观点](https://github.com/peatio/peatio)经常出现。但是在我检查了开源代码之后，要让它准备好投入生产，似乎有太多的工作要做:

*   最初的项目被广泛批评为废弃品
*   我见过的其他分叉解决了一些问题，但很快也变得无法维护。

我从未接受过那些工作——预算/计划似乎总是不合适。

不久前，一个朋友找到我，让我使用另一个 fork-[rubykube](https://github.com/rubykube/)。令人惊讶的是，它正在积极开发中...我的拉动式请求得到了快速的反馈，总体来说周转很快。

Rubykube fork 似乎令人印象深刻。所以我同意帮忙！

## 卢布

我个人为任何试图通过开源项目谋生的公司鼓掌。货币化是一件极其复杂的事情——我们都听说过成功的故事，但与所有的失败故事相比，它们是罕见的。

我偶然在 Upwork 上发现了他们的一份招聘启事——下面是他们对自己公司潜在新员工的描述:

> 我们是一家法国初创公司，有 60 多名全职开发人员和一个由 30-40 名成员组成的远程团队。我们支持一个由才华横溢的开发人员组成的 Telegram 社区，他们相信我们的产品是我们利基市场中最具创新性的解决方案

阅读[完整的工作提案](https://www.upwork.com/jobs/_~01a910275c4ab11cd4/)，如果你感兴趣就申请吧。

我愿意相信，公司不太可能为了潜在的新雇员而对公司撒谎。因此，这种描述可能是最接近现实的(与他们的营销材料相比)。

看起来公司在这个项目上投入了大量资金。拥有大约 100 名员工，并且还在增长——看起来像是一艘稳定的大船，不会很快减速。

## 岔开变化

### 变化最大

最初的 Peatio 项目是一个完整的 rails 应用程序。我不会去争论它是好是坏，但是 rubykube fork 脱离了它，转向了微服务架构。这是一个相当合理的权衡——部署/监控所有这些服务将变得更加复杂，但这也有很大的好处。

*   从安全角度来看，将一些组件与公共访问隔离开来会更容易
*   由于每个服务都有一个 API，因此定制接口并与上游保持同步要容易得多。
*   有一个普遍的假设，即它更容易扩展

### 事件 API

有趣的是看到 pusher 依赖被从项目中移除。它被事件源的模式和事件 API 和 RabbitMQ 所取代。

事件采购在 Ruby 社区是一个时髦的关键词。这太受欢迎了，有一整个会议专门讨论事件源+ Ruby/Rails。

我也相信，这是最安全的扩展方式。

### 插件

Rubykube 开发人员增加了插件功能。到目前为止还没有插件，但是我觉得把一些可选的依赖项转移到插件上是非常合理的——比如 sentry(错误捕捉器)。

### 加密钱包

期待即将到来的 0.19 版本-它大大提高了加密钱包的支持。许多问题表明情况会进一步改善。[下面是发行说明](https://github.com/rubykube/peatio/pull/1538/commits/5a2a42dcb1d211d860358b8c67ce12b78bea30da)中的一段预告:

> 1.  Multiple wallet support.
> 2.  A new blockchain synchronization mechanism.
> 3.  Full ERC20 token support.
> 4.  Blockchain read-write service and client split.

## 入门

首先有一个[非常活跃的电报社区](https://t.me/peatio)。一定要加入！

如果你想在本地玩 project，建议使用[极简的本地环境](https://github.com/rubykube/peatio#minimalistic-local-development-environment-with-docker-compose)。我直奔[工作台](https://github.com/rubykube/workbench)，它运行 Docker 中 rubykube fork 的所有组件。

有基于 Kubernetics 的生产设置，但它似乎是针对谷歌云平台，目前没有对 AWS 的官方支持(至少 yeat 没有)。根据 Denys Tun 在电子邮件中的引用:

> 我要补充的是，目前我们没有资源来分散我们对 AWS 的关注，我们将继续致力于改善 GCP 部署基础设施。在 RKCP 栈的 2.0 版本之后，AWS 将出现在我们的主题列表中。我想还需要 2-3 个月..

我们和 convox.com 一起设法让它在 AWS 上运行。这需要几个变通办法，但它是可行的。所以我的猜测是，AWS 将很快得到官方支持。如果 RabbitMQ 能在 AWS 上换成 SQS 就太棒了...我知道这可能不是一个现成的替代品，但是...*眨眼*

## 那么，怎样才能建立自己的交易所呢？

引自 rubykube:

> “您可能不需要在 Peatio 源代码方面有活跃的开发人员，但是，我们建议以下团队设置:1 名开发/运营人员、3 名前端开发人员(react / angular)、2 名 QA 工程师和 1 名安全官。”

我基本同意这个建议。但是...如果我们粗略估计..它可能需要 3 个月才能得到第一个运行版本。之后是持续成本-至少一个开发/运营部门来维持正常运转？做定期安全审计？我没有这些问题的答案。

为什么需要这么多前端工程师？很简单，所有后端都是 rubykube 写的。它配有非常基本的登陆/交易页面和[道德上和技术上过时的交易界面](https://github.com/rubykube/peatio-trading-ui)。预计它将被一个现代的面向客户端的应用程序所取代，该应用程序将通过 API 调用([最好是通过 react 或 preact](https://github.com/rubykube/peatio-react) )与 peatio 通信。

在一个完美的世界里，这应该是一个 3 点计划:

*   开始运作
*   开始添加大量硬币
*   获得自己的前端

但是不要期望事情会很容易，世界并不完美。这不是你可以轻易外包或便宜买到的东西。如果你没有可以信任的技术人员，那么[雇佣 rubykube](http://peatio.tech/) 。

## 游戏中的皮肤

我没有打电话，如果 rubykube fork 值得一个严重的交换项目，别人做了。但是:

*   这是我见过的最好的项目。
*   虽然我可能会争论一些实现细节，但我大体上同意 forks direction。
*   事情进展得非常快，非常稳定的发布周期。

对于任何想参与这个项目的人来说，我强烈建议做出贡献。我有一次受欢迎的经历——如果我们计划将我们的资源投入到项目中，公司会给他们的服务提供很大的折扣。项目领导提供了一个可能的治理模型，人们在项目的未来有投票权。