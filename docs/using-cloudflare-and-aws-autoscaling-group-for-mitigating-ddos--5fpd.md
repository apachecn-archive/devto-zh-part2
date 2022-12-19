# Cloudflare 和 AWS Autoscaling Group 用于缓解 DDoS 攻击

> 原文：<https://dev.to/dianamaltseva8/using-cloudflare-and-aws-autoscaling-group-for-mitigating-ddos--5fpd>

因基础架构故障而离线的平均每小时成本约为 100，000 美元。更重要的是，你的品牌和客户会因此失去客户的信任。

不幸的是，今天 DDoS 攻击的频率和强度都在上升。此外，由于使用多个不安全的物联网设备，僵尸网络发动分布式体积攻击的开发比以往任何时候都更容易。

### DDoS 攻击是如何工作的？

通过用恶意软件感染计算机系统并通过电子邮件、网站和社交媒体传播恶意软件，攻击者创建了一个具有远程控制能力的受感染机器网络。

这组被感染的机器被称为“僵尸网络”。攻击者为他们创建僵尸网络，以产生巨大的流量洪流来阻塞和淹没正常流量，并使目标服务过载。

为此，僵尸网络可以向目标发送多个连接请求，或者让计算机发送大量随机垃圾数据。

由于僵尸网络产生的流量是使用成千上万的来源设计的，每个僵尸都代表一个合法的互联网设备，因此将正常用户流量与有害用户流量分开是一个真正的挑战。

### 如何防范 DDoS 攻击

由于 DDoS 攻击会严重损害公司(从金融机构到新闻网站)，使访问重要信息变得非常困难，并导致请求丢失，因此防御 DDoS 攻击成为主要关注点。

应对 DDoS 攻击的关键挑战是将正常流量与攻击分开。越是复杂的多向量攻击，任务就越复杂。因此，主要攻击者的目标是使缓解过程尽可能复杂。

另一个困难是 DDoS 流量可能以不同的形式出现，从无欺骗的单源攻击到复杂的多向量 DDoS 攻击，这些攻击使用各种途径来使目标服务过载。

当使用包括丢弃或限制流量在内的缓解措施，以便将正常流量排除在有害流量之外时，您应该注意，多向量自适应 DDoS 攻击也可以适应所应用的对策。

在这种情况下，一个好的选择是使用分层解决方案。现在，让我们考虑减轻分布式拒绝服务攻击的最佳工具。为了创建最佳的 DDoS 攻击缓解策略，网络管理员通常以各种方式组合使用它们。

另外，了解更多关于 [类型的 DDoS 攻击。](https://smartym.pro/blog/distributed-denial-of-service-ddos-attacks-meaning-types-and-tools-to-mitigate/) 现在，让我们考虑最佳的 DDoS 缓解方案。

### Cloudflare 工具简介

[Cloudflare](https://www.cloudflare.com/) 是最好的 DDoS 缓解解决方案之一，深受 web 开发团队的喜爱和赞赏。2017 年 12 月，Forrester 独立研究公司将 Cloudflare 命名为 DDoS 攻击防护的领先工具。

*在本次调查中，基于对各种标准的分析，Cloudflare 显示出自己是最成功的解决方案，这些标准涉及其定价模式、DDoS 缓解能力、可扩展性、实施长度以及对不同类型的 [DDoS 攻击的缓解。](https://www.cloudflare.com/learning/ddos/what-is-a-ddos-attack/)T6】*

Cloudflare 支持保护、优化和加速任何 web 属性(网站、SaaS 服务、API 和其他互联网连接的属性)，而无需安装软件或更改代码。

在 Cloudflare 的保护下，所有 web 流量都通过智能、安全的全球网络传输。此外，随着每项新的在线服务的增加，网络变得更加智能，并且由于网站性能的提高、流量的优化和垃圾邮件水平的降低而得到改善。

### AWS 自动缩放组简介

[亚马逊网络服务](https://aws.amazon.com/) 提供灵活可靠的基础设施和各种服务，帮助开发人员防范 DDoS，并按照 AWS DDoS 缓解最佳实践构建高可扩展性架构。

深入了解针对 DDoS 弹性的 [AWS 最佳实践。【T2](https://d1.awsstatic.com/whitepapers/Security/DDoS_White_Paper.pdf)

有很多 AWS 服务可以用于 DDoS 弹性:Amazon CloudFront，AWS WAF，AWS Elastic Load Balancing，Amazon Route 53，旨在管理流量，拒绝不可接受的请求，减少应用停机时间和延迟。

AWS Shield 是一种 DDoS 攻击缓解服务，与 Amazon Web Services 集成，允许即时检测和自动内嵌缓解技术来保护运行在 AWS 上的目标服务。

了解 **[如何利用 Cloudflare 和 AWS 缓解 DDoS 攻击。](https://smartym.pro/blog/ddos-attack-mitigation-using-cloudflare-and-aws-autoscaling-group-in-our-work/)T6】**