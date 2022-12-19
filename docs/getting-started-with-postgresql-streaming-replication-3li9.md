# PostgreSQL 流复制入门

> 原文：<https://dev.to/scalegrid/getting-started-with-postgresql-streaming-replication-3li9>

[![PostgreSQL Streaming Replication for High Availability - ScaleGrid Blog](../Images/e4cb9dba75f8cafe57d3a27516f7fb29.png)T2】](https://scalegrid.io/blog/getting-started-with-postgresql-streaming-replication/)

在这篇博文中，我们将深入探讨在 PostgreSQL 中设置流复制(SR)的具体细节。流复制是在您的 [PostgreSQL 主机](https://scalegrid.io/postgresql.html)中实现高可用性的基础构建块，通过运行主从配置产生。

阅读原文:【PostgreSQL 流复制入门

# 主从术语

### 主/主要服务器

*   可以接受写操作的服务器。
*   也称为读/写服务器。

### 从属/备用服务器

*   数据与主服务器持续保持同步的服务器。
*   也称为备份服务器或副本。
*   热备用服务器是指在提升为主服务器之前无法连接的服务器。
*   相比之下，[热备用服务器](https://www.postgresql.org/docs/10/static/hot-standby.html)可以接受连接并提供只读查询。在接下来的讨论中，我们将只关注热备用服务器。

数据被写入主服务器，并传播到从服务器。如果现有的主服务器出现问题，其中一个从服务器将接管并继续进行写入，以确保系统的可用性。

# 基于沃尔玛运输的复制

## 什么是沃尔？

*   WAL 代表[预写日志](https://www.postgresql.org/docs/9.1/static/wal-intro.html)。
*   它是一个日志文件，对数据库的所有修改都是在应用/写入数据文件之前写入的。
*   WAL 用于数据库崩溃后的恢复，确保数据完整性。
*   WAL 在数据库系统中用于实现原子性和持久性。

## WAL 是如何用于复制的？

预写日志记录用于保持数据库服务器之间的数据同步。这通过两种方式实现:

### 基于文件的日志传送

*   WAL 日志文件从主服务器传送到备用服务器，以保持数据同步。
*   主服务器可以直接将日志复制到备用服务器存储，也可以与备用服务器共享存储。
*   一个 WAL 日志文件可以包含多达 16MB 的数据。
*   WAL 文件只有在达到该阈值后才被发送。
*   *这将导致复制延迟，如果主服务器崩溃且日志未归档，还会增加数据丢失的几率*

### 流式沃尔记录

*   WAL 记录块由数据库服务器进行流式传输，以保持数据同步。
*   备用服务器连接到主服务器以接收 WAL 块。
*   WAL 记录在生成时进行流式处理。
*   WAL 记录的流式传输不需要等待 WAL 文件被填充。
*   与基于文件的日志传送相比，这允许备用服务器保持最新。
*   默认情况下，流复制是异步的，尽管它也支持同步复制。

这两种方法各有利弊。使用基于文件的传送可以实现时间点恢复和连续归档，而流式传输可以确保备用服务器上的数据即时可用。但是，您可以配置 PostgreSQL 来同时使用这两种方法，并享受其中的好处。在这篇博客中，我们主要关注实现 PostgreSQL 高可用性的流复制。

# 如何设置流式复制？

[![PostgreSQL Streaming Replication](../Images/e19564c306bcc9ad1bd7ab8422276cd0.png)T2】](https://scalegrid.io/blog/wp-content/uploads/2018/09/postgres_streaming.jpg)

在 PostgreSQL 中设置流复制非常简单。假设 PostgreSQL 已经安装在所有服务器上，您可以按照以下步骤开始:

## 主节点上的配置

*   使用 initdb 实用程序初始化主节点上的数据库。
*   通过运行以下命令，创建具有复制权限的角色/用户。运行命令后，您可以通过运行\du 在 psql 上列出它们来验证它。
    *   创建用户<user_name>复制登录加密密码“<password>”；</password></user_name>
*   Configure properties related to streaming replication in the master PostgreSQL configuration (postgresql.conf) File:

    | # The possible values are *copy &#124; minimum &#124; logical*
    **wal _ level** = copy
    # When the standby server is out of sync with the primary server, the required number of pg_rewind function
    **wal _ log _ hints** = on
    # Set the maximum number of concurrent connections of the standby server.
    **Max _ WAL _ SENDERS** = 3
    # The following parameters are used to tell the master device to keep the minimum number of
    # WAL logs so that they will not be deleted before they are consumed in standby.
    # each segment is 16mb
    **wal _ keep _ segments** = 8
    # The following parameters enable the read-only connection when the node is in
    # standby role. This will be ignored when the server is running as the primary server.
    **Hot standby** = on |

*   Add replication entry in pg _ hba.conf file to allow replication connections between the servers:

    | # Allow replication connections from the local host,
    # Execute by users with replication permission.
    # Type database user address method
    Host copy repl _ User IP Address (CIDR) MD5 |

*   重新启动主节点上的 PostgreSQL 服务，以使更改生效。

## 备用节点上的配置

*   Create the base backup of master node using pg_basebackup utility and use it as a starting point for the standby.

    | # Explain several options used by pg_basebackup utility
    #-x option is used to include the required transaction log files (WAL files) in
    # backup. When you specify stream, this will open the second connection to the server
    # and start transferring the transaction log while running the backup.
    #-C is the checkpoint option. Setting it to fast will force the checkpoint to be # created soon.
    #-W forces pg_basebackup to prompt for a password before connecting
    # to the database.
    ***pg _ base backup** -D < data _ directory > -h < master _ host > -X stream-c fast-U repl _ user-W* |

*   Create replication configuration file if not present (it is created automatically if-r option is provided in pg _ base backup):

    | # Specify whether to start the server as a standby server. In streaming copy,
    # This parameter must be set to on.
    **standby _ mode** =' on'
    # Specify a connection string for the standby server to connect
    # with the primary server.
    **primary _ conn info** = ' host = < master _ host > port = < postgres _ port > user = < replication _ user > p Assword = < password > application _ name = "host _ name"'
    # Specify the recovery to a specific timeline. By default, the recovery will follow the same timeline as it was when the basic backup was performed.
    # Setting this item to latest can restore to the latest timeline
    # found in the archive, which is useful in standby servers.
    **Recovery _ Target _ Timeline** =' Latest' |

*   启动备用。

必须在所有备用服务器上完成备用配置。配置完成并启动备用服务器后，它将连接到主服务器并启动流日志。这将设置复制，并且可以通过运行 SQL 语句"**SELECT * FROM pg _ stat _ replication；** *”。*

默认情况下，流式复制是异步的。如果您希望使其同步，则可以使用以下参数对其进行配置:

| # num_sync 是事务
#需要等待回复的同步备用数量。
# standby_name 与 recovery.conf
中的 application_name 值相同#如果所有备用服务器都必须考虑同步，则设置值“*”
#如果只需要考虑特定的备用服务器，则将其指定为# standby_name 的逗号分隔列表。
#用于此目的的备用服务器的名称是
#备用服务器的 application_name 设置，如
#备用服务器的 WAL 接收器的 primary_conninfo 中所设置。
**同步备用名称** = '数字同步(备用名称[，...] )' |

对于同步复制，Synchronous_commit 必须设置为 on，这是默认设置。PostgreSQL 为同步提交提供了非常灵活的选项，可以在用户/数据库级别进行配置。有效值如下:

*   **Off** -甚至在事务记录被实际刷新到该节点上的 WAL 日志文件之前，事务提交就被确认给客户端。
*   **Local** -只有在事务记录被刷新到该节点上的 WAL 日志文件之后，事务提交才会被确认给客户端。
*   **Remote_write** -只有在 synchronous_standby_names 指定的服务器确认事务记录已写入磁盘缓存后，事务提交才会向客户端确认，但不一定是在刷新到 WAL 日志文件后。
*   **On** -只有在 synchronous_standby_names 指定的服务器确认事务记录被刷新到 WAL 日志文件后，事务提交才会被确认给客户端。
*   **Remote_apply** -只有在 synchronous_standby_names 指定的服务器确认事务记录被刷新到 WAL 日志文件并被应用到数据库后，事务提交才会被确认给客户端。

在同步复制模式下，将 synchronous_commit 设置为 off 或 local 将使它像异步一样工作，并可以帮助您获得更好的写入性能。但是，这将增加备用服务器上数据丢失和读取延迟的风险。如果设置为 remote_apply，它将确保备用服务器上的即时数据可用性，但写入性能可能会降低，因为它应该应用于所有/提到的备用服务器。

如果您计划使用连续归档和时间点恢复，可以启用归档模式。虽然这不是流复制的强制要求，但启用归档模式有额外的好处。如果存档模式没有打开，那么我们需要使用**复制插槽**功能，或者确保 **wal_keep_segments** 值根据负载设置得足够高。

参考这个[精彩的演示](https://pgconf.ru/media/2018/02/19/Alvaro_Modern%20PostgreSQL%20High%20Availability.pdf "Modern PostgreSQL High Availability")来深入了解 PostgreSQL 中高可用性的更多细节。在下一篇博文中，我们将向您介绍使用流复制来管理 PostgreSQL 高可用性的工具世界。