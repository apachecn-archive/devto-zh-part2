# Apache Storm -拓扑权限

> 原文：<https://dev.to/risdenk/apache-storm---topology-permissions-38b6>

### 概述

Apache Storm 是一个为处理信息流而设计的分布式系统。每个处理单元称为一个螺栓，一组具有初始喷口的螺栓称为一个拓扑。多个拓扑可以部署在一个 Apache Storm 集群上。Apache Storm 集群中的多租户需要能够防止任何用户删除拓扑或查看拓扑日志。大部分信息来自 [Apache Storm 安全文档](http://storm.apache.org/releases/current/SECURITY.html)，但是并不完全清楚每个设置的作用。

### 阿帕奇风暴拓扑权限

默认情况下，为 Apache Storm 启用安全性时，只有部署拓扑的用户有权执行管理操作，如重新平衡、激活、停用和终止。以下配置可以在群集或拓扑级别设置。如果在拓扑中定义了它们，它们将覆盖全局配置。

| 配置 | 描述 |
| --- | --- |
| `topology.users` / `topplogy.groups` | 这允许指定的用户/组充当拓扑的所有者。这允许用户执行拓扑管理操作，如重新平衡、激活、停用和终止。 |
| `logs.users` / `logs.groups` | 这允许指定的用户/组查看拓扑的日志。 |

### Apache Storm 集群管理员权限

有一个集群级别的配置可以让一组用户成为整个 Apache Storm 集群的管理员。

| 配置 | 描述 |
| --- | --- |
| `nimbus.admins` | 这些用户将在部署的所有拓扑上拥有超级用户权限。他们将能够执行其他管理操作(如重新平衡、激活、停用和终止)，即使他们不是拓扑的所有者。 |