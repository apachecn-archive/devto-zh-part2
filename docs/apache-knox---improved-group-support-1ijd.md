# 阿帕奇诺克斯-改进的小组支持

> 原文：<https://dev.to/risdenk/apache-knox---improved-group-support-1ijd>

### 概述

[Apache Knox](https://knox.apache.org/) 是一个反向代理，它简化了 Kerberos 安全 [Apache Hadoop](https://hadoop.apache.org/) 集群和其他相关组件前面的安全性。组支持对 Knox 至关重要，因为它支持不同拓扑的授权，而无需指定单个用户。从历史上看，Knox group 的支持是有限的，导致拓扑级别的授权很少。 [Apache Ranger](https://ranger.apache.org/) 与 Knox 的集成使得管理授权策略变得容易。随着 Apache Knox 中对组支持的改进，授权策略(如用 Ranger 创建的策略)可以有效地使用组。[诺克斯改进提案 1 (KIP-1)](https://cwiki.apache.org/confluence/display/KNOX/KIP-1+LDAP+Improvements) 创建于 2016 年 11 月，旨在关注集团改进。

### 阿帕奇诺克斯< 0.10.0 组限制

阿帕奇·诺克斯从一开始就依靠阿帕奇·希罗加入了团队支持。随着 Hadoop 生态系统的发展，组实现中出现了许多重叠。每个都必须正确实现分页、嵌套 ou、PAM 支持，并使用 memberOf 之类的计算属性。在 [KIP-1](https://cwiki.apache.org/confluence/display/KNOX/KIP-1+LDAP+Improvements) 创建后，诺克斯社区致力于改善团队支持。Apache Knox 0.10.0 是第一个获得重大群体改进的版本。

### Apache Knox 0.10.0 组改进

在 Apache Knox 0.10.0 之前，组支持仅限于使用<1000 groups. For large companies that rely on groups, 1000 groups is a small number. [KNOX-644](https://issues.apache.org/jira/browse/KNOX-644) 通过分页结果来消除这一限制的实现。在各种安全实现中尝试了几次之后，我拼凑了一个补丁来处理> 1000 个组。这改善了群体支持，但仍有一些限制，如性能可以通过 [KNOX-461](https://issues.apache.org/jira/browse/KNOX-461) 得到改善。然而，在 Knox 0.10.0 中有另一个改进，以不同的方式解决群体问题。

Linux PAM 支持通过 [KNOX-537](https://issues.apache.org/jira/browse/KNOX-537) 添加到 Knox 0.10.0 中。这使得诺克斯能够利用现有的团队工具，如 [SSSD](https://en.wikipedia.org/wiki/System_Security_Services_Daemon) 来处理复杂的场景。这包括缓存和利用现有的操作系统配置。这也支持嵌套 ou，这是现有 LDAP 组实现的一个限制。

Apache Knox 博客也详细介绍了 Knox 0.10.0 的改进[在这里](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=66854729)。

### Apache Knox 0.11.0 组改进

Apache Knox 0.11.0 继续改进了 0.10.0 中添加的组支持。主要的改进是在 [KNOX-237](https://issues.apache.org/jira/browse/KNOX-237) 中增加了对`HadoopGroupProvider`作为身份提供者的支持。有了这个补充，Knox 能够利用 [Apache Hadoop](https://hadoop.apache.org/) 社区在团队支持方面所做的工作。Hadoop 组模块已经过大量测试，因为它是许多 Hadoop 组件的组查找的基础。Apache Knox 博客有关于这个[的更多细节。](https://cwiki.apache.org/confluence/display/KNOX/2017/06/22/Hadoop+Group+Lookup+Provider)

### 阿帕奇 Knox 1.0.0 &现状

2018 年 2 月，Apache Knox 1.0.0 发布，标志着 Knox 项目的一个重要里程碑。自从 Knox 0.11.0 以来，改进组支持已经不是一个大的焦点，因为识别的问题已经被解决。今天，用大多数组后端配置 Apache Knox 没有任何问题是可能的。