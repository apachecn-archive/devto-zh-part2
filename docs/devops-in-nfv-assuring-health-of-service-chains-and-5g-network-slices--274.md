# NFV 的 DevOps:确保服务链和 5G 网络切片的健康

> 原文：<https://dev.to/sagarnangare/devops-in-nfv-assuring-health-of-service-chains-and-5g-network-slices--274>

我们处于 2018 年的最后一个季度，我们可以看到广泛领先的网络设备供应商、解决方案提供商和通信服务提供商如何沉迷于向下一代连接网络(即 5G 电信)的转变。对这种转变的这种考虑导致在开源社区的积极贡献以及围绕商业供应商联合实施的新的创新概念验证的工作[的推动下](https://osm.etsi.org/wikipub/index.php/OSM_PoC_1_-_DevOps_in_Service_Chains_and_5G_Network_Slices) [NFV 技术](http://calsoftinc.com/resources/ebooks/nfv-2018-market-statistics-predictions-use-cases-industry-insights/#utm_source=Blog&utm_medium=Article)日益成熟和发展。

在这篇文章中，我们来谈谈英特尔、Netrounds、Rift.io、Arctos labs 等厂商如何在 ETSI 开源软件 MANO (OSM)的帮助下实现 DevOps 方法。

## 挑战——对服务保障的需求

在一个场景的路线图中，多个 vnf 链接在一起以创建网络服务和 5G 功能的实施，即网络切片，其中网络通道被切片以支持 5G 支持的不同用例，服务提供商关心的是确保和维护终端用户的一致服务质量。毕竟，向 5G 转型是为了提供良好的客户体验，这是服务提供商的最终目标。但是，向 5G 转型会给网络架构和运营带来巨大的复杂性，还会包括共享环境；例如，虚拟机中部署的 VNFs 可能需要更新以增强服务功能，或者可能需要立即修补以避免故障。很可能，由于用软件代替网络设备，这种变化会更加频繁。为了解决更新问题，服务提供商将很难执行手动测试和验证，这可能会停止向活跃的最终客户提供服务。服务提供商应通过在网络服务每个阶段积极监控和测试来确保服务提供稳定性。此外，在网络切片功能的情况下，这对于服务提供商来说变得尤为重要，因为客户需求与具有低延迟的用例相一致。每个网络片都定义了要满足的关键 SLA 要求。

综上所述，服务提供商需要

通过主动监控和测量在网络切片上收到的指标，验证每个网络切片所需的关键 SLA(匹配客户和用例)。在不影响服务交付的情况下，找出问题并在运行时解决。
在监控/测试网络服务和确保客户服务质量的方式与可用的工具和解决方案之间存在差距。

## 实施 PoC 以应对挑战

为了应对上述挑战，服务提供商必须通过在服务链中使用可编程 API 以及对服务的端到端监控来进行高度自动化的主动测试和验证。

由 Telenor、Intel、Arctos Labs、Netrounds、Rift.io 和 ETSI OSM 公司实施的 PoC 解决了上述监控和服务保证问题。

PoC 利用 MANO 进行自动化网络服务生命周期管理以及端到端服务激活、验证和操作。DevOps 方法用于网络运营和网络服务设计，并支持服务链和 5G 切片的自动化部署和维护。

本演示的主要特点是在网络服务或服务链的设计阶段增加了新服务的测试。此外，对新网络服务的交付或更新在运行时得到验证；如果任何更新未能执行，可以在到达客户之前回滚。

## PoC 架构及示例

作为此 PoC 的示例案例，考虑包含三个不同切片的网络切片场景，这三个切片具有不同类型的特征、客户输出和 KPI，以满足服务提供商提供的服务的要求。在本例中，涉及的部分包括:移动宽带有一个重要的 KPI，即吞吐量，大规模物联网的目标 KPI 是数据包丢失，工业自动化的目标 KPI 是延迟。

[![PoC Example](../Images/e22560b3f43e69964fe11b72a388777b.png "PoC Example")](https://res.cloudinary.com/practicaldev/image/fetch/s--AOgFGCkD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.calsoftinc.com/wp-content/uploads/2018/10/PoC-Example.png)T3】

<center>Fig – PoC Example</center>

ETSI 开源 MANO 与 RIFT 结合使用。IO 的 RIFT.ware 解决方案用于 NFV 流程编排，而 OpenStack 用作虚拟基础架构管理器(VIM)。

[![PoC demo architecture](../Images/08f636c7905408e6ae6bff2c3a5e4ea8.png "PoC demo architecture")](https://res.cloudinary.com/practicaldev/image/fetch/s--e7S-Kh83--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.calsoftinc.com/wp-content/uploads/2018/10/PoC-Architecture.png)T3】

<center>Fig – PoC demo architecture</center>

为了测试和确保网络服务的预期服务质量水平，并能够测量网络片的 KPI，Netround 的 VTA(虚拟测试代理)被部署为服务的一部分。这些测试代理用于主动生成流量、分析细节以及跨服务应用和接口进行实时测量。VTA 通过 Netrounds 的统一控制中心进行远程控制和更新，该中心由 Netrounds 端或内部托管。以下是示例网络服务的外观

[![Network service with virtual test agents](../Images/b85e768465d4ea78be4765353f4739ea.png "PoC demo architecture")](https://res.cloudinary.com/practicaldev/image/fetch/s--RjNWe0ZO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.calsoftinc.com/wp-content/uploads/2018/10/Network-service-with-virtual-test-agents.png)T3】

<center>Fig – Network service with virtual test agents</center>

## 总结

对于由不同但有针对性的服务链组成的 5G 网络切片的大规模实施，可以通过支持自动化操作的 DevOps 进行实时监控和测试来实现主动的端到端服务保证。此 PoC 的关键部分是部署虚拟测试代理，作为网络服务链的一部分，以在服务的生命周期中保持关注。它可以向控制中心发送有关服务运行状况的实时信息。

您可以从发布在 [ETSI OSM 网站](https://osm.etsi.org/wikipub/index.php/OSM_PoC_1_-_DevOps_in_Service_Chains_and_5G_Network_Slices)上的这份[白皮书](https://osm.etsi.org/wikipub/images/3/3c/PoC_1_White_Paper.pdf)中获得关于此概念验证的更多细节，并在此观看现场演示[。](https://www.youtube.com/watch?v=_7dPGIQOD7s)