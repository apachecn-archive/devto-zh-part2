# 共享 MongoDB 托管的五大优势

> 原文：<https://dev.to/scalegrid/top-5-benefits-of-shared-mongodb-hosting-37fi>

[![Shared MongoDB Hosting: Slow Query Analysis - ScaleGrid](../Images/44461ffbf05b4a00dfa59ecfcd265f19.png)T2】](https://scalegrid.io/blog/top-5-benefits-of-shared-mongodb-hosting/)

共享托管是在云中部署 MongoDB 的最具成本效益且易于设置的选项之一，世界上成千上万的公司使用它来托管他们的数据库。在本帖中，我们概述了使用共享 [MongoDB 主机](https://scalegrid.io/mongodb.html "Shared MongoDB Hosting Service")的[五大好处](https://scalegrid.io/blog/top-5-benefits-of-shared-mongodb-hosting/)，以帮助您决定它是否适合您的业务。

共享 MongoDB 托管计划通常最适合需要快速发展、开发客户场景或托管应用程序的开发或测试环境的初创企业和中型企业。最重要的是为 MongoDB 寻找一个完全托管的共享托管解决方案，这样您就有必要的专业知识来帮助您监控、备份和排除数据库操作的故障。否则，它会严重影响应用程序的安全性或稳定性，进而影响您的业务寿命。这也使您和您的团队能够专注于构建您的应用程序，而不是被不可预见的数据库问题所困扰。

## 共享集群的 MongoDB 托管配置

每个 MongoDB 进程运行在一个单独的 [Docker](https://www.docker.com/ "Docker Container for MongoDB Hosting") 容器中，分配给每个容器的 RAM 量是您使用的磁盘大小或存储的 1/10。支持的最小存储容量为 2GB(200 MB RAM)，您可以通过这些计划增加的容量没有上限。在 [AWS](https://aws.amazon.com/) 上可以以低至[10 美元/GB](https://scalegrid.io/pricing.html#section_pricing_shared "ScaleGrid Shared MongoDB Hosting Pricing") 的价格获得 MongoDB 的完全托管共享主机计划，并包括这里的所有五大优势，以及更多的附加功能，这些功能将帮助您开发一个面向增长的坚实基础设施。

## 五大共享 MongoDB 托管优势

1.  ### 高可用性【MongoDB 共享托管的最大好处之一是，您可以为[高可用性](https://www.techopedia.com/definition/1021/high-availability-ha "What is High Availability?")部署副本集，以确保您的应用程序免受潜在故障的影响。创建一个三节点副本集，其中每个节点位于您选择的云提供商的不同可用性区域(AZ)或不同数据中心。如果任何一个 AZ 出现故障，副本集将自动接管，因此您的应用程序保持在线，没有任何停机时间。

2.  ### 灾难恢复

    [备份](https://help.scalegrid.io/docs/mongodb-backups "Scheduled Backups & On-Demand Backups for Shared MongoDB Hosting")对于保护您的数据安全极其重要，应该始终设置在您的共享托管帐户中，以确保您为灾难恢复事件做好准备。有了共享的 MongoDB 服务，您可以通过定制的时间表自动执行备份，这样您就不再需要每天手动执行这项任务。按需备份也是共享集群的一个巨大优势，因此您可以在对应用程序进行任何更新之前备份数据。同样重要的是在发生故障时恢复数据的能力。通过共享的 MongoDB 托管，您可以对任何备份执行[一键恢复](https://help.scalegrid.io/docs/mongodb-backups-restores "Disaster Recovery: One-Click Restore of Backups")，以从数据中心崩溃或系统错误中恢复您的数据。另一个巨大的好处是能够执行[备份查看](https://help.scalegrid.io/docs/mongodb-backups-selective-restore)，或者选择性恢复，因此您可以在不影响您的主集群的情况下查看您的备份。如果您希望通过部分还原进行恢复，例如从单个集合中还原数据，这将非常有用，因此您不必覆盖整个集群。
3.  ### 动态、自动缩放

    作为一家成长中的公司，您不想被持续监控 MongoDB 集群的大小所困扰，以确保不会耗尽存储空间。共享主机的另一个好处是你的 MongoDB 集群会随着你的成长而自动扩展，所以你永远不用担心空间耗尽或者过早地为未使用的空间付费。分配给容器的 CPU 和 RAM 会随着集群中数据的扩展而动态扩展。例如，如果您的数据从 2GB 增加到 4GB，分配的 RAM 容量会自动从 200MB 增加到 400MB，而不会导致任何停机。
4.  ### 数据库监控和警报【MongoDB 的完全托管共享主机最受欢迎和最受欢迎的好处之一是为您的数据库提供自动监控和警报。这对于没有内部资源或时间来密切监控其集群的年轻公司来说极其重要，他们需要一个完全托管的解决方案来通知他们托管的集群是否有任何变化或问题。这包括运行慢速查询分析的能力，因此您可以快速识别任何可能降低应用程序速度的问题查询。您可以访问的其他令人惊叹的监控报告包括索引使用、执行的作业、系统内存、缓存活动等等。您还可以在全局和集群级别上配置任何 MongoDB 指标的警报，并定制何时发送通知的阈值和触发器。监控加警报相当于主动的 MongoDB 用户和健康、愉快的数据库。【T2![Shared MongoDB Hosting: Slow Query Analysis - ScaleGrid](../Images/c31f331458654473de1b7a986dc41546.png)

5.  ### MongoDB 版本自动升级

    升级您的数据库可能是一项非常[耗时且累人的任务](http://www.dbta.com/Columns/DBA-Corner/The-Importance-of-Keeping-Your-DBMS-Up-to-Date-111614.aspx)，许多公司推迟升级，这可能会导致您的数据安全出现严重漏洞。您还会错过新版本中可用的增强功能，这些功能是为了帮助您的数据库更高效地运行而引入的。MongoDB 自动升级是一个惊人的好处，但不是所有的共享主机服务都提供它，所以确保你[比较 MongoDB 提供商](https://scalegrid.io/mongodb/hosting-comparison.html "Compare MongoDB Hosting Providers")从长远来看节省你自己的时间和麻烦！通过使用自动为您处理数据库版本升级的 MongoDB 服务，您在执行升级之前有 30-60 天的通知时间，因此您有足够的时间对应用程序进行任何必要的修改。

这些只是从共享 MongoDB 托管中可以获得的令人难以置信的好处的一部分，但是清单并没有到此为止。期待访问高级功能，如月度报告、免费日志循环、操作系统补丁、免费 24/7 支持，以及对底层机器的 100% MongoDB 管理员访问和 SSH root 访问。你可以通过 Plan doc 在我们的 [MongoDB 特性上找到完整的特性列表。](https://help.scalegrid.io/docs/mongodb-features-by-plan)