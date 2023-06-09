# MySQL 半同步复制中的数据完整性和性能考虑

> 原文：<https://dev.to/scalegrid/data-integrity-and-performance-considerations-in-mysql-semisynchronous-replication-5aa9>

[![Data Integrity and Performance Considerations in MySQL Semisynchronous Replication - ScaleGrid Blog](img/bf1214029ed6462c353783c67ae74130.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--D3bqI-hf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t1m090hi0g1bswpqvwmg.png)

[MySQL 半同步复制](https://dev.mysql.com/doc/refman/5.5/en/replication-semisync.html)提供了改进的数据完整性，因为当提交成功返回时，已知数据至少存在于两个地方——主服务器和从服务器。在这篇博文中，我们回顾了一些影响半同步复制的数据完整性和性能方面的 MySQL 配置。我们将在一个 3 节点副本集中使用 [InnoDB 存储引擎](https://scalegrid.io/blog/calculating-innodb-buffer-pool-size-for-your-mysql-server/ "MySQL Server - Calculating InnoDB Buffer Pool Size")和基于 GTID 的复制(主副本和 2 个从副本)，这将确保从副本中有冗余。这意味着如果一个从服务器出现问题，我们可以依靠另一个。

阅读原文:[MySQL 半同步复制中的数据完整性和性能考虑](https://scalegrid.io/blog/data-integrity-and-performance-considerations-in-mysql-semisynchronous-replication/)

## 适用于主节点和从节点的配置

*   [innodb _ flust _ log _ at _ Trx _ commit](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_flush_log_at_trx_commit)= 1
*   [sync_binlog](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_sync_binlog) = 1

这些配置保证了数据的高持久性和一致性设置。也就是说，每个提交的事务都保证存在于二进制日志中，并且日志也被刷新到磁盘。因此，在电源故障或操作系统崩溃的情况下，MySQL 的数据一致性始终得以保持。

## 主节点上的配置。

*   [rpl _ semi _ sync _ master _ wait _ for _ slave _ count](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_rpl_semi_sync_master_wait_for_slave_count):

该选项用于配置在半同步主机提交事务之前必须发送确认的从机数量。在 3 节点副本集中，我们建议将其设置为 1，这样我们就可以始终保证数据在至少一个从属服务器上可用，同时避免因等待来自两个从属服务器的确认而对性能造成任何影响。

*   [rpl _ semi _ sync _ master _ time out](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_rpl_semi_sync_master_timeout):

该选项用于配置半同步主机在切换回异步模式之前等待从机确认的时间。我们建议将该值设置为一个较大的值，这样就不会退回到异步模式，否则会破坏我们的数据完整性目标。由于我们使用 2 个从机进行操作，并且[rpl _ semi _ sync _ master _ wait _ for _ slave _ count](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_rpl_semi_sync_master_wait_for_slave_count)设置为 1，我们可以假设至少有一个从机在合理的时间内进行了确认，从而将该设置的性能影响降至最低。

## 从节点上的配置

在从机中，非常准确地跟踪两个位置总是很重要的:中继日志中 SQL 线程的当前执行位置，以及 IO 线程的当前位置，该位置指示主二进制文件被读取和复制到从机多远。不坚持这些立场的后果是很明显的。如果从系统崩溃并重启，SQL 线程可能会从错误的偏移量开始处理事务，或者 IO 线程可能会从主二进制日志中的错误位置开始提取数据。这两种情况都会导致数据损坏。

通过以下配置确保从机的碰撞安全性很重要:

*   [relay _ log _ info _ repository = TABLE](https://dev.mysql.com/doc/refman/5.7/en/replication-options-slave.html#sysvar_relay_log_info_repository)
*   [relay_log_recovery = ON](https://dev.mysql.com/doc/refman/5.7/en/replication-options-slave.html#sysvar_relay_log_recovery)

将[relay _ log _ info _ repository](https://dev.mysql.com/doc/refman/5.7/en/replication-options-slave.html#sysvar_relay_log_info_repository)设置为 TABLE 将确保 SQL 线程的位置随着从属线程上的每个事务提交一起更新。然而，很难保持 IO 线程的准确位置并刷新到磁盘。这是因为读取主二进制日志和写入从中继日志不是基于事务的。如果每次写入从属中继日志后都必须更新 IO 线程位置并将其刷新到磁盘，对性能的影响会非常大。一个更好的解决方案是设置 [relay_log_recovery](https://dev.mysql.com/doc/refman/5.7/en/replication-options-slave.html#sysvar_relay_log_recovery) = ON，在这种情况下，如果 MySQL 重新启动，当前的中继日志将被认为是损坏的，并将根据 SQL 线程位置从主服务器上重新获取。

最后但并非最不重要的一点是，必须注意，半同步复制确保数据在主服务器提交事务之前刚刚“到达”其中一个从服务器，并不意味着事务在从服务器上提交。因此，确保 SQL 线程以良好的性能运行是很好的。在理想情况下，SQL 线程与 IO 线程一起移动，因此我们不仅可以从接收事务中获益，还可以提交事务。建议采用多线程从配置，这样我们可以提高从 SQL 线程的性能。多线程从机的重要配置有:

*   [slave_parallel_workers](https://dev.mysql.com/doc/refman/5.7/en/replication-options-slave.html#sysvar_slave_parallel_workers) :设置为> 1，启用多个从 SQL 线程工作器。根据在主服务器上写入的线程数量，我们可以决定一个最佳数量，这样从服务器就不会延迟。
*   [从并行型](https://dev.mysql.com/doc/refman/5.7/en/replication-options-slave.html#sysvar_slave_parallel_type) =逻辑时钟
*   [从保存提交顺序](https://dev.mysql.com/doc/refman/5.7/en/replication-options-slave.html#sysvar_slave_preserve_commit_order) =1

上述配置将保证从机的并行性，同时保持主机上的事务顺序。

总之，通过在 MySQL 副本集上使用上述配置，我们能够保持高数据完整性和最佳性能。

一如既往，如果您有任何问题，请给我们留下评论，在 Twitter 上通过 [@scalegridio](https://twitter.com/scalegridio) 联系我们，或者发送电子邮件到 [support@scalegrid.io](mailto:support@scalegrid.io) 给我们。