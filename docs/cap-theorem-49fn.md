# CAP 定理

> 原文：<https://dev.to/g33kzone/cap-theorem-49fn>

CAP 定理是 Eric Brewer 在 2000 年分布式计算原理研讨会(PODC)上创造的术语，它指出分布式数据存储不可能同时提供一致性、可用性和分区容差中的两个以上。它有助于系统设计者在设计分布式数据系统时了解实际的权衡。

**一致性** -确保分布式集群中的每个节点都返回最新的成功写入。

**可用性** -每个非故障节点在合理的时间内返回对所有读写请求的响应，但不保证它包含最近的写入。

**分区容忍** -尽管存在网络分区，系统仍能继续运行并维持其一致性保证。一旦分区修复，分布式系统也必须正常恢复。

[![](img/7ce20b046f28dbd9b183884c967a3622.png)T2】](https://1.bp.blogspot.com/-h0hSJTgzZaA/Ww09w6ecuiI/AAAAAAAAzR0/MuH9VIqutv4YyTMqlvlpscnCNP-0b7JyACPcBGAYYCw/s1600/CAP.PNG)

CAP 定理将系统分为三类

`CP (Consistent and Partition Tolerant)` -一致的、分区容忍的但从不可用的系统。换句话说，只有在网络分区的情况下才牺牲可用性。

`CA (Consistent and Available)` -在没有任何网络分区的情况下，CA 系统是一致且可用的系统。单节点数据库可以归为这一类。

`AP (Available and Partition Tolerant)` -这些系统是可用的，并且允许分区，但不能保证一致性。例如，Cassandra 数据库提供最终一致性功能。