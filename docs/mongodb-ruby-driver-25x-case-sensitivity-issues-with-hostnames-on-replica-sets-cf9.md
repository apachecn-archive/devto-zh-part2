# 副本集上主机名的 MongoDB Ruby Driver 2.5.x 区分大小写问题

> 原文：<https://dev.to/scalegrid/mongodb-ruby-driver-25x-case-sensitivity-issues-with-hostnames-on-replica-sets-cf9>

[![ScaleGrid Blog: MongoDB Ruby Driver 2.5.x Case-Sensitivity Issues with Hostnames on Replica Sets](img/751e8118911023b6912a4a29e3820f3d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_KjBcmi---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lp7zpp7ot61cl3800pxn.png)

将 [MongoDB Ruby 驱动](https://docs.mongodb.com/ruby-driver/master/)升级到 2.5.x 后，连接到 [MongoDB](https://www.mongodb.com/) 副本集有问题？我们最近收到了一些关于最新 MongoDB Ruby 驱动程序版本的问题的询问，并写了这篇文章来分享我们对问题和原因的发现。

尝试连接时遇到的错误消息是-

```
No server is available matching preference: #<Mongo::ServerSelector::Primary:...>
```

这个问题已经被报告给 MongoDB，并且正在这里被跟踪。我们花了一些时间来调查这个问题以及 2.5.x 中驱动程序代码中引入的根本原因。

# MongoDB Ruby 驱动程序 2.5.x 问题摘要

此问题存在于 2.5.x 版本的 MongoDB Ruby 驱动程序中，当包含副本集的主机名包含区分大小写的字符(例如 ABC-server1.example.com)时会遇到此问题。可能的解决方法有:

1.  降级到 2.4.x 或在 2.6.x 可用后升级。
2.  将副本集所有成员的主机名更改为 downcase。例如，将上面示例中的主机名更改为 abc-server1.example.com。

# 该问题的详细情况

在 Ruby 上启用详细的日志记录提供了一个线索:

```
...
#19140] DEBUG -- : MONGODB | Topology type 'replica set' initializing.
#19140] DEBUG -- : MONGODB | Server sg-connectiontest1-13622.servers.example.com:27017 initializing.
#19140] DEBUG -- : MONGODB | Server description for sg-connectiontest1-2.servers.example.com:27017 changed from 'unknown' to 'unknown'.
#19140] DEBUG -- : MONGODB | Server sg-connectiontest1-13623.servers.example.com:27017 initializing.
#19140] DEBUG -- : MONGODB | Server description for sg-connectiontest1-3.servers.example.com:27017 changed from 'unknown' to 'secondary'.
#19140] DEBUG -- : MONGODB | Server sg-connectiontest1-13624.servers.example.com:27017 initializing.
#19140] DEBUG -- : MONGODB | Server description for sg-connectiontest1-4.servers.example.com:27017 changed from 'unknown' to 'arbiter'.
#19140] DEBUG -- : MONGODB | There was a change in the members of the 'replica set' topology.
C:/Ruby24-x64/lib/ruby/gems/2.4.0/gems/mongo-2.5.1/lib/mongo/server_selector/selectable.rb:119:in `select_server': No server is available matching preference: # using server_selection_timeout=30 and local_threshold=0.015 (Mongo::Error::NoServerAvailable)
from lib/ruby/gems/2.4.0/gems/mongo-2.5.1/lib/mongo/database.rb:157:in `command'
from lib/ruby/gems/2.4.0/gems/mongo-2.5.1/lib/mongo/client.rb:404:in `list_databases'
from lib/ruby/gems/2.4.0/gems/mongo-2.5.1/lib/mongo/client.rb:385:in `database_names'
...

```

很明显，驱动程序无法正确检测副本集的主要角色。当与 2.4.x 的日志进行比较时，很明显这在该版本中是正确的，即主节点被正确地标识为主节点。

```
] DEBUG -- : MONGODB | Topology type 'replica set' initializing.
] DEBUG -- : MONGODB | Server sg-connectiontest1-13622.servers.example.com:27017 initializing.
] DEBUG -- : MONGODB | Server description for sg-connectiontest1-2.servers.example.com:27017 changed from 'unknown' to 'primary'.
] DEBUG -- : MONGODB | Server sg-connectiontest1-13623.servers.example.com:27017 initializing.
] DEBUG -- : MONGODB | Server description for sg-connectiontest1-3.servers.example.com:27017 changed from 'unknown' to 'secondary'.
] DEBUG -- : MONGODB | Server sg-connectiontest1-13624.servers.example.com:27017 initializing.
] DEBUG -- : MONGODB | Server description for sg-connectiontest1-4.servers.example.com:27017 changed from 'unknown' to 'arbiter'.

```

我们得到的另一个线索是，我们的服务器名称在日志中略有变化。而不是实际的名字**SG**-connection test1-2 . servers . example . com，它被记录为**SG**-connection test1-2 . servers . example . com

通过检查在连接初始化期间如何解析主机名的代码以及如何为主机名分配角色，我们能够确定驱动程序代码在解析连接字符串时是小写的(例如，将 ABC.example.com 转换为 abc.example.com)。接下来，驱动程序连接到主服务器，使用 [isMaster](https://docs.mongodb.com/manual/reference/command/isMaster/) 命令发现拓扑。在解析这个命令的结果时，驱动程序决定每个节点的角色。

但是，在将命令返回的主机名与连接字符串中的主机名进行比较时，驱动程序忽略了执行不区分大小写的匹配。这导致 MongoDB 报告的主服务器名称与驱动程序从连接字符串中确定的名称不匹配。这导致主服务器的角色变得未知，并导致连接失败。角色检测代码中的地址匹配检查是在 2.5.x 中添加的。

对该问题的修复已经针对驱动程序的版本 2.6.0 。