# ElastiCache 和 EC2 上的自托管 Redis:哪个适合你？

> 原文：<https://dev.to/totalcloudio/elasticache-or-self-hosted-redis-on-ec2-which-is-the-one-for-you--5dng>

通常，有时您必须在托管服务和自托管服务之间做出选择，尤其是在云环境中。这两种服务各有利弊。但是，如果您非常了解您的用例，这些服务中的每一个都提供了额外的优势。这个理论适用于 EC2 上的 ElastiCache 和自托管 Redis。

这篇文章比较了这两种服务的实用和不实用之处，因此你可以根据自己的使用情况选择合适的服务。

## 实际比较:EC2 上的 ElastiCache 与自托管 Redis

Redis 是领先的开源内存键值存储产品之一。这是一个很好的缓存工具。如果您是 AWS 用户，您可以通过 EC2 实例(通过自托管)或 ElastiCache 来利用这个工具。

使用 ElastiCache 的好处是 AWS 管理托管 Redis 的服务器。然而，在 EC2 上使用自托管 Redis 的好处是，它提供了在配置之间进行调整的自由。还有其他几个类似的差异。了解这些差异会让你有足够的能力做出正确的选择。

首先，让我们带您了解一下 EC2 上 ElastiCache 和自托管 Redis 之间的主要区别。

[![ElastiCache Vs. Self-hosted Redis on EC2 ](../Images/4e9b15a461b3bbf4e0f1a786210a9973.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nBgqc_Od--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/il0kg7wusepxr86p83lo.png)

## 深入探究 EC2 上 ElastiCache 和自托管 Redis 的实用性

#### **elastic cache:支持完全托管的 Redis 和 Memcached**

elastic cache 无缝部署、运行和扩展 Redis 以及 MemCached 内存数据存储。它自动执行管理任务，如软件修补、设置、配置、硬件供应、故障恢复、备份等。没有丢失工作负载的风险，因为它持续监控集群。这使得它非常适合为媒体共享、社交网络、游戏、广告技术、金融、医疗保健、物联网等构建数据密集型应用。

#### **elastic cache:根据需求自动缩放**

ElastiCache 最受欢迎的特性之一是它的可伸缩性。它可以根据应用需求进行横向扩展、纵向扩展和纵向扩展。此外，分片支持写入和内存扩展，而副本提供读取扩展。

#### **elastic cache:具有多个 vCPU 的实例不能利用所有的核心**

Redis 使用单线程执行读/写操作。只有一个线程/进程负责数据库中的读/写操作。这确保了不会因为多线程向磁盘中写入/读取多个信息而出现死锁。就性能而言，这是 Redis 的一个非常强大的特性，因为它消除了管理锁和闩锁的需要。但是，这一个线程只能使用一个内核，vCPU 完成所有工作。因此，您没有使用多个 CPU 的自由。因此，具有 1 个以上 CPU 的 ElastiCache 实例面临额外 vcpu 的浪费。

为了更好地了解 CPU 利用率，亚马逊在 2018 年 4 月的某个时候推出了 [【工程利用率】指标](https://aws.amazon.com/about-aws/whats-new/2018/04/amazon-elastiCache-for-redis-introduces-new-cpu-utilization-metric-for-better-visibility-into-redis-workloads/) 。

#### **EC2 上的自托管 Redis:允许您尽快更新最新版本**

使用自托管 Redis 集群的主要优势之一是，您可以随时获得最新版本。你甚至可以在世界上的其他人开始使用它之前利用这个软件的最好的特性。

#### **EC2 上的自托管 Redis:提供修改配置的自由**

EC2 上的自托管 Redis 提供了理解其底层功能和根据您的需求修改配置的自由。例如，要修改 Redis 配置以连续拍摄快照，您可以:

```
save 900 1

save  300 10

save  60 10000
```

如果在 900 秒内至少有 1 次更改，则创建快照

如果在 300 秒内至少有 10 次更改，则创建快照

如果在 900 秒内至少有 10000 次更改，则创建快照

其他配置如“bgsave-error 时停止写入”和“maxmemory”是非常有用的配置更改。如果你正在寻找更多的调整细节，请查看下面的列表:

*   [https://scaleyourcode.com/blog/article/25](https://scaleyourcode.com/blog/article/25)
*   [https://scaleyourcode.com/blog/article/15](https://scaleyourcode.com/blog/article/15)
*   [https://dzon . com/articles/redis 性能基准](https://dzone.com/articles/redis-performance-benchmarks)

#### **EC2 上的自托管 Redis:相关指标的不可用性使得维护变得繁琐**

尽管 EC2 上的 Redis 在配置方面提供了灵活性，但很难维护。监控指标并不容易。您要么需要使用第三方工具，比如 AppDynamics，要么手动调用 API 来监控 Redis 中的指标。您可以自动扩展/更新/升级/安全补丁等。使用 Ansible、Chef 或 Puppet 等工具。这具有成本效益，但需要投入大量精力。

#### **EC2 上的自托管 Redis:实例限制**

亚马逊建议用户只使用基于 HVM 的 EC2 实例。由于延迟问题，只有少数基于 PV 的实例可用。

## 总之:

如果成本是一个大问题，自托管 Redis 集群是一个更好的选择。如果成本不是问题，无缝维护是重点，那么 Elasticache 是更好的选择。下面是一年的对比:

[![ElastiCache or Self-hosted Redis on EC2](../Images/faa4da54f08f8777cde30185ee356c62.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ksvK7FKj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/why2zbr85lk4yepibax1.png)

如果你想知道选择哪种服务，你知道去哪里找！

注:最初发布于 2018 年 10 月 17 日 [blog.totalcloud.io](http://blog.totalcloud.io/elasticache-or-self-hosted-redis-on-ec2-which-is-the-one-for-you/) 。