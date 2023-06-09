# 将 Hortonworks HDP 集群迁移到新的数据中心

> 原文：<https://dev.to/risdenk/moving-hortonworks-hdp-clusters-to-a-new-data-center-1511>

### 概述

我的团队支持多个 [Hortonworks 数据平台(HDP)](https://hortonworks.com/products/data-platforms/hdp/) 集群，并且已经支持了很多年。大约一年前，我们开始着手为我们的平台增加多数据中心功能。我们的平台在过去几年中一直在扩展，我们需要购买更多的硬件。我们决定将新硬件保留在常规数据中心，并将旧硬件移至新的数据中心。这是一个有趣的挑战，也是我们能够成功应对的挑战。

### 移动要求

HDP 集群通过 TLS/SSL、LDAP 和 Kerberos 进行保护。还有一些其他与安全相关的配置。我们没有禁用或更改此移动的任何安全相关配置。集群按原样配置，然后移动到新的数据中心。

### 此举

2017 年初，领导层决定我们将支持多个数据中心。目标是在 2017 年 10 月前做好一切准备，之后继续改进。我们的新硬件定于 4 月份到货，需要在 5 月中旬准备好投入使用。在我的团队努力设置和配置新硬件的同时，我们还努力解决将集群从一个数据中心移动到另一个数据中心的后勤问题。

旧的硬件被分成 4 个集群，我们不仅想移动它们，还想将它们重新组合成 2 个更大的集群。我们计划将所有用例转移到新的硬件上，然后专注于重建旧的硬件集群。重建旧的硬件集群需要 4-6 周，因为我们的供应商还需要重新配置系统。到八月，旧的硬件集群已经被重新配置。因为集群是为灾难恢复和潜在的高可用性而设计的，所以我们在集群从网络上断开并交付之前执行了最终的数据同步。我们的网络和数据中心团队出色地确保了所有必要的安排。这包括隔离开关和其他步骤。我们雇佣了一家运输公司将硬件从一个数据中心转移到另一个数据中心。

在重建旧集群的同时，另一个数据中心团队正在为集群的到来做准备。这包括设置电源、网络和占地面积。提前设置了网络分支，并预先配置了交换机，以确保它们能够快速启用。所有准备工作完成后，我们等待了几天，等待集群到达新的数据中心。

### 在新的数据中心建立集群

集群完好无损地到达了新的数据中心，并需要在旅行后适应环境。我们反对一个紧张的截止日期，因为一个大公司范围的软件展示同时发生，限制了允许的变化。一旦集群适应了环境，我们的供应商和数据中心团队就开始重新连接集群。团队做了出色的工作，确保每根网线第一次连接成功。我们有一些小的硬件问题需要在搬迁后解决，但这是一次非常成功的重新连接。迁移需要更改整个集群的 IP 地址，这一过程也很顺利。在迁移过程中，我们将旧数据中心 IP 地址块中的主机名分配重新分配给了新数据中心。

关键时刻是在集群重新连接到网络并且机器启动之后。我们打开阿帕奇·安巴里，点击“开始全部”按钮，看看 HDP 是否会回来。令我惊讶的是，整个集群没有出现任何问题。在搬家的过程中，我们没有错过任何一步，一切都很顺利。

### 吸取的教训和顺利的事情

*   尽快选择运输公司
    *   运送集群比我们预期的要困难
*   为网络团队提供充足的时间来设置网络
*   为数据中心团队提供充足的准备时间
*   **不要**更改集群主机名
    *   我们没有故意更改主机名，因为那样会破坏安全模型，并且需要大量的工作来重建。
*   大量的努力和一点点的运气可以走很长的路
    *   参与搬迁的团队都出色地完成了计划
    *   整个过程中没有一步失误，令人印象深刻

### 结论

我们成功地将两个 HDP 集群从一个数据中心迁移到另一个数据中心，没有出现任何问题。在迁移过程中，我们也没有禁用或损害任何安全性。整个过程从头到尾花了大约 6 个月，包括前期规划。最终，我们成功地提前完成了集群的迁移。