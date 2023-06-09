# 具有交叉复制的 Redis 集群

> 原文：<https://dev.to/dzeban/redis-cluster-with-cross-replication-2h31>

在我上一篇关于 Redis 高可用性的文章中，我说过 Redis 集群有一些尖锐的地方，并承诺会告诉大家。

这篇文章将涉及交叉复制集群的复杂情况，因为这是我所使用的。如果您有一个简单的平面拓扑，在专用节点上有一个 Redis 实例，那就没问题了。但这不是我的案子。

所以让我们开始吧。

## [T1】简介](#intro)

首先，让我们定义一些术语，以便相互理解。

*   节点–物理**服务器**或虚拟机，您将在其中运行 Redis 实例。
*   实例 Redis 服务器**以集群模式处理**。

其次，让我描述一下我的 Redis 集群拓扑是什么样子，什么是交叉复制。

Redis 集群是从以集群模式运行的多个 Redis 实例构建的。每个实例都是独立的，因为它服务于主角色**或从角色**中特定的键子集。对角色的强调是有意的——每个分片主服务器和每个分片副本都有单独的 Redis 实例，例如，如果您有 3 个复制因子为 3 的分片(2 个附加副本),您必须运行 9 个 Redis 实例。这是我第一次天真地尝试在 3 个节点上创建集群:

```
$ redis-trib create --replicas 2 10.135.78.153:7000 10.135.78.196:7000 10.135.64.55:7000
>>> Creating cluster
*** ERROR: Invalid configuration for cluster creation.
*** Redis Cluster requires at least 3 master nodes.
*** This is not possible with 3 nodes and 2 replicas per node.
*** At least 9 nodes are required. 
```

Enter fullscreen mode Exit fullscreen mode

(`redis-trib`是创建 Redis 集群的“官方”工具)

这里重要的一点是，所有的 Redis 工具都使用 Redis 实例，而不是节点，所以将实例放在正确的冗余拓扑中是您的责任。

## 交叉复制的动机

Redis 集群需要至少 3 个节点，因为要在网络分区中存活，它需要一个主节点多数(像 Sentinel 中一样)。如果你想要 1 个副本，然后再添加 3 个节点，嘣！现在，您有一个 6 节点集群要操作。

如果你在云中工作，你只需要花费很少的钱就可以构建一打小节点，这没什么问题。不幸的是，并不是每个人都加入了云党，必须操作真正的金属节点，服务器硬件通常从 32 GiB 的 RAM 和 8 核 CPU 开始，这对 Redis 节点来说是一个真正的大杀器。

因此，为了节省硬件，我们可以做一个小把戏，在一个节点上运行几个实例(并且可能将它与其他服务放在一起)。但是请记住，在这种情况下，您必须手动在节点之间分配主机，并配置**交叉复制**。

交叉复制仅仅意味着您没有用于副本的专用节点，您只是将数据复制到下一个节点。

[![Redis cluster with cross replication](img/6d56ea83111f2ff653ba1d62fcb13def.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BBvdoCA_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://alex.dzyoba.com/img/redis-cluster-cross-replication.png)

这样可以节省集群大小，您可以在 3 个节点而不是 9 个节点上创建一个包含 2 个副本的 Redis 集群。因此，您需要操作的东西更少了，节点得到了更好的利用，而不是每 9 个节点一个单线程轻量级 Redis 进程，现在您将在 3 个节点上有 3 个这样的进程。

要创建一个集群，您必须运行带有`cluster-enabled
yes`参数的`redis-server`。对于交叉复制集群，您在一个节点上运行多个 Redis 实例，因此您必须在不同的端口上运行它。你可以查看这[两本](https://linode.com/docs/applications/big-data/how-to-install-and-configure-a-redis-cluster-on-ubuntu-1604/) [手册](http://codeflex.co/configuring-redis-cluster-on-linux/)了解详情，但最重要的部分是配置。这是我正在使用的配置文件:

```
protected-mode no
port {{ redis_port }}
daemonize no
loglevel notice
logfile ""
cluster-enabled yes
cluster-config-file nodes-{{ redis_port }}.conf
cluster-node-timeout 5000
cluster-require-full-coverage no
cluster-slave-validity-factor 0 
```

Enter fullscreen mode Exit fullscreen mode

`redis_port`变量为每个碎片取值 7000、7001 和 7002。启动 Redis 服务器的 3 个实例，分别在 3 个节点上安装 7000、7001 和 7002，这样总共有 9 个实例，让我们继续。

## 构建交叉复制集群

当您构建集群时，可能会遇到第一个惊喜。如果你像这样调用

```
$ redis-trib create --replicas 2 10.135.78.153:7000 10.135.78.196:7000 10.135.64.55:7000 10.135.78.153:7001 10.135.78.196:7001 10.135.64.55:7001 10.135.78.153:7002 10.135.78.196:7002 10.135.64.55:7002 
```

Enter fullscreen mode Exit fullscreen mode

然后，它可能会将所有主实例放在一个节点上。这是因为，同样，它假设每个实例都位于单独的节点上。

所以你必须手工分配主人和奴隶。为此，首先从主节点创建一个集群，然后为每个主节点添加从节点。

```
# Create a cluster with masters
$ redis-trib create 10.135.78.153:7000 10.135.78.196:7001 10.135.64.55:7002
>>> Creating cluster
>>> Performing hash slots allocation on 3 nodes...
Using 3 masters:
10.135.78.153:7000
10.135.78.196:7001
10.135.64.55:7002
M: 763646767dd5492366c3c9f2978faa022833b7af 10.135.78.153:7000
slots:0-5460 (5461 slots) master
M: f63c210b13d68fa5dc97ca078af6d9c167f8c6ec 10.135.78.196:7001
slots:5461-10922 (5462 slots) master
M: 5f4bb09230ca016e7ffe2e6a4e5a32470175fb66 10.135.64.55:7002
slots:10923-16383 (5461 slots) master
Can I set the above configuration? (type 'yes' to accept): yes
>>> Nodes configuration updated
>>> Assign a different config epoch to each node
>>> Sending CLUSTER MEET messages to join the cluster
Waiting for the cluster to join.
>>> Performing Cluster Check (using node 10.135.78.153:7000)
M: 763646767dd5492366c3c9f2978faa022833b7af 10.135.78.153:7000
slots:0-5460 (5461 slots) master
0 additional replica(s)
M: 5f4bb09230ca016e7ffe2e6a4e5a32470175fb66 10.135.64.55:7002
slots:10923-16383 (5461 slots) master
0 additional replica(s)
M: f63c210b13d68fa5dc97ca078af6d9c167f8c6ec 10.135.78.196:7001
slots:5461-10922 (5462 slots) master
0 additional replica(s)
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered. 
```

Enter fullscreen mode Exit fullscreen mode

这是我们现在的集群:

```
127.0.0.1:7000> CLUSTER NODES                
763646767dd5492366c3c9f2978faa022833b7af 10.135.78.153:7000@17000 myself,master - 0 1524041299000 1 connected 0-5460
f63c210b13d68fa5dc97ca078af6d9c167f8c6ec 10.135.78.196:7001@17001 master - 0 1524041299426 2 connected 5461-10922
5f4bb09230ca016e7ffe2e6a4e5a32470175fb66 10.135.64.55:7002@17002 master - 0 1524041298408 3 connected 10923-16383 
```

Enter fullscreen mode Exit fullscreen mode

现在为每个主服务器添加 2 个副本:

```
$ redis-trib add-node --slave --master-id 763646767dd5492366c3c9f2978faa022833b7af 10.135.78.196:7000 10.135.78.153:7000
$ redis-trib add-node --slave --master-id 763646767dd5492366c3c9f2978faa022833b7af 10.135.64.55:7000 10.135.78.153:7000

$ redis-trib add-node --slave --master-id f63c210b13d68fa5dc97ca078af6d9c167f8c6ec 10.135.78.153:7001 10.135.78.153:7000
$ redis-trib add-node --slave --master-id f63c210b13d68fa5dc97ca078af6d9c167f8c6ec 10.135.64.55:7001 10.135.78.153:7000

$ redis-trib add-node --slave --master-id 5f4bb09230ca016e7ffe2e6a4e5a32470175fb66 10.135.78.153:7002 10.135.78.153:7000
$ redis-trib add-node --slave --master-id 5f4bb09230ca016e7ffe2e6a4e5a32470175fb66 10.135.78.196:7002 10.135.78.153:7000 
```

Enter fullscreen mode Exit fullscreen mode

现在，这是我们全新的交叉复制集群，有两个副本:

```
$ redis-cli -c -p 7000 cluster nodes
763646767dd5492366c3c9f2978faa022833b7af 10.135.78.153:7000@17000 myself,master - 0 1524041947000 1 connected 0-5460
216a5ea51af1faed7fa42b0c153c91855f769321 10.135.78.196:7000@17000 slave 763646767dd5492366c3c9f2978faa022833b7af 0 1524041948515 1 connected
0441f7534aed16123bb3476124506251dab80747 10.135.64.55:7000@17000 slave 763646767dd5492366c3c9f2978faa022833b7af 0 1524041947094 1 connected
f63c210b13d68fa5dc97ca078af6d9c167f8c6ec 10.135.78.196:7001@17001 master - 0 1524043602115 2 connected 5461-10922
f90c932d5cf435c75697dc984b0cbb94c130f115 10.135.78.153:7001@17001 slave f63c210b13d68fa5dc97ca078af6d9c167f8c6ec 0 1524043601595 2 connected
00eb2402fc1868763a393ae2c9843c47cd7d49da 10.135.64.55:7001@17001 slave f63c210b13d68fa5dc97ca078af6d9c167f8c6ec 0 1524043600057 2 connected
5f4bb09230ca016e7ffe2e6a4e5a32470175fb66 10.135.64.55:7002@17002 master - 0 1524041948515 3 connected 10923-16383
af75fc17e552279e5939bfe2df68075b3b6f9b29 10.135.78.153:7002@17002 slave 5f4bb09230ca016e7ffe2e6a4e5a32470175fb66 0 1524041948000 3 connected
19b8c9f7ac472ecfedd109e6bb7a4b932905c4fd 10.135.78.196:7002@17002 slave 5f4bb09230ca016e7ffe2e6a4e5a32470175fb66 0 1524041947094 3 connected 
```

Enter fullscreen mode Exit fullscreen mode

## 集群节点的故障转移

如果我们失败了(使用`DEBUG SEGFAULT`命令),我们的第三个节点(10.135.64.55)集群将继续工作:

```
127.0.0.1:7000> CLUSTER NODES
763646767dd5492366c3c9f2978faa022833b7af 10.135.78.153:7000@17000 myself,master - 0 1524043923000 1 connected 0-5460
216a5ea51af1faed7fa42b0c153c91855f769321 10.135.78.196:7000@17000 slave 763646767dd5492366c3c9f2978faa022833b7af 0 1524043924569 1 connected
0441f7534aed16123bb3476124506251dab80747 10.135.64.55:7000@17000 slave,fail 763646767dd5492366c3c9f2978faa022833b7af 1524043857000 1524043856593 1 disconnected
f63c210b13d68fa5dc97ca078af6d9c167f8c6ec 10.135.78.196:7001@17001 master - 0 1524043924874 2 connected 5461-10922
f90c932d5cf435c75697dc984b0cbb94c130f115 10.135.78.153:7001@17001 slave f63c210b13d68fa5dc97ca078af6d9c167f8c6ec 0 1524043924000 2 connected
00eb2402fc1868763a393ae2c9843c47cd7d49da 10.135.64.55:7001@17001 slave,fail f63c210b13d68fa5dc97ca078af6d9c167f8c6ec 1524043862669 1524043862000 2 disconnected
5f4bb09230ca016e7ffe2e6a4e5a32470175fb66 10.135.64.55:7002@17002 master,fail - 1524043864490 1524043862567 3 disconnected
af75fc17e552279e5939bfe2df68075b3b6f9b29 10.135.78.153:7002@17002 slave 19b8c9f7ac472ecfedd109e6bb7a4b932905c4fd 0 1524043924568 4 connected
19b8c9f7ac472ecfedd109e6bb7a4b932905c4fd 10.135.78.196:7002@17002 master - 0 1524043924000 4 connected 10923-16383 
```

Enter fullscreen mode Exit fullscreen mode

我们可以看到 10.135.78.196:7002 上的副本接管了插槽范围 10923-16383，现在它是主服务器

```
127.0.0.1:7000> set a 2
-> Redirected to slot [15495] located at 10.135.78.196:7002
OK 
```

Enter fullscreen mode Exit fullscreen mode

我们是否应该恢复第三个节点上的 Redis 实例集群将恢复

```
127.0.0.1:7000> CLUSTER nodes
763646767dd5492366c3c9f2978faa022833b7af 10.135.78.153:7000@17000 myself,master - 0 1524044130000 1 connected 0-5460
216a5ea51af1faed7fa42b0c153c91855f769321 10.135.78.196:7000@17000 slave 763646767dd5492366c3c9f2978faa022833b7af 0 1524044131572 1 connected
0441f7534aed16123bb3476124506251dab80747 10.135.64.55:7000@17000 slave 763646767dd5492366c3c9f2978faa022833b7af 0 1524044131367 1 connected
f63c210b13d68fa5dc97ca078af6d9c167f8c6ec 10.135.78.196:7001@17001 master - 0 1524044130334 2 connected 5461-10922
f90c932d5cf435c75697dc984b0cbb94c130f115 10.135.78.153:7001@17001 slave f63c210b13d68fa5dc97ca078af6d9c167f8c6ec 0 1524044131876 2 connected
00eb2402fc1868763a393ae2c9843c47cd7d49da 10.135.64.55:7001@17001 slave f63c210b13d68fa5dc97ca078af6d9c167f8c6ec 0 1524044131877 2 connected
19b8c9f7ac472ecfedd109e6bb7a4b932905c4fd 10.135.78.196:7002@17002 master - 0 1524044131572 4 connected 10923-16383
af75fc17e552279e5939bfe2df68075b3b6f9b29 10.135.78.153:7002@17002 slave 19b8c9f7ac472ecfedd109e6bb7a4b932905c4fd 0 1524044131000 4 connected
5f4bb09230ca016e7ffe2e6a4e5a32470175fb66 10.135.64.55:7002@17002 slave 19b8c9f7ac472ecfedd109e6bb7a4b932905c4fd 0 1524044131572 4 connected 
```

Enter fullscreen mode Exit fullscreen mode

但是，主节点**没有在原始节点上恢复到**，它仍然在第二个节点上(10.135.78.196)。重启后，第三个节点只包含从属实例

```
$ redis-cli -c -p 7000 cluster nodes | grep 10.135.64.55
0441f7534aed16123bb3476124506251dab80747 10.135.64.55:7000@17000 slave 763646767dd5492366c3c9f2978faa022833b7af 0 1524044294347 1 connected
00eb2402fc1868763a393ae2c9843c47cd7d49da 10.135.64.55:7001@17001 slave f63c210b13d68fa5dc97ca078af6d9c167f8c6ec 0 1524044293138 2 connected
5f4bb09230ca016e7ffe2e6a4e5a32470175fb66 10.135.64.55:7002@17002 slave 19b8c9f7ac472ecfedd109e6bb7a4b932905c4fd 0 1524044294553 4 connected 
```

Enter fullscreen mode Exit fullscreen mode

第二个节点服务于两个主实例。

```
$ redis-cli -c -p 7000 cluster nodes | grep 10.135.78.196
216a5ea51af1faed7fa42b0c153c91855f769321 10.135.78.196:7000@17000 slave 763646767dd5492366c3c9f2978faa022833b7af 0 1524044345000 1 connected
f63c210b13d68fa5dc97ca078af6d9c167f8c6ec 10.135.78.196:7001@17001 master - 0 1524044345000 2 connected 5461-10922
19b8c9f7ac472ecfedd109e6bb7a4b932905c4fd 10.135.78.196:7002@17002 master - 0 1524044345000 4 connected 10923-16383 
```

Enter fullscreen mode Exit fullscreen mode

现在，有趣的是，如果第二个节点在这种状态下失败，我们将失去 3 个主节点中的 2 个，并且我们将**失去整个集群**，因为没有主节点仲裁。

```
$ redis-cli -c -p 7000 cluster nodes
763646767dd5492366c3c9f2978faa022833b7af 10.135.78.153:7000@17000 myself,master - 0 1524046655000 1 connected 0-5460
216a5ea51af1faed7fa42b0c153c91855f769321 10.135.78.196:7000@17000 slave,fail 763646767dd5492366c3c9f2978faa022833b7af 1524046544940 1524046544000 1 disconnected
0441f7534aed16123bb3476124506251dab80747 10.135.64.55:7000@17000 slave 763646767dd5492366c3c9f2978faa022833b7af 0 1524046654010 1 connected
f63c210b13d68fa5dc97ca078af6d9c167f8c6ec 10.135.78.196:7001@17001 master,fail? - 1524046602511 1524046601582 2 disconnected 5461-10922
f90c932d5cf435c75697dc984b0cbb94c130f115 10.135.78.153:7001@17001 slave f63c210b13d68fa5dc97ca078af6d9c167f8c6ec 0 1524046655039 2 connected
00eb2402fc1868763a393ae2c9843c47cd7d49da 10.135.64.55:7001@17001 slave f63c210b13d68fa5dc97ca078af6d9c167f8c6ec 0 1524046656075 2 connected
19b8c9f7ac472ecfedd109e6bb7a4b932905c4fd 10.135.78.196:7002@17002 master,fail? - 1524046605581 1524046603746 4 disconnected 10923-16383
af75fc17e552279e5939bfe2df68075b3b6f9b29 10.135.78.153:7002@17002 slave 19b8c9f7ac472ecfedd109e6bb7a4b932905c4fd 0 1524046654623 4 connected
5f4bb09230ca016e7ffe2e6a4e5a32470175fb66 10.135.64.55:7002@17002 slave 19b8c9f7ac472ecfedd109e6bb7a4b932905c4fd 0 1524046654515 4 connected 
```

Enter fullscreen mode Exit fullscreen mode

让我重申一下——对于交叉复制的集群，在单个节点连续重启两次后，您可能会丢失整个集群。这就是为什么每个 Redis 实例最好有一个专用节点的原因，否则，对于交叉复制，我们真的应该注意主节点的分布。

为了避免上述情况，我们应该手动将第三个节点上的一个从节点故障转移为主节点。

为此，我们应该连接到现在是副本的 10.135.64.55:7002，然后发出`CLUSTER FAILOVER`命令:

```
127.0.0.1:7002> CLUSTER FAILOVER
OK

127.0.0.1:7002> CLUSTER NODES
763646767dd5492366c3c9f2978faa022833b7af 10.135.78.153:7000@17000 master - 0 1524047703000 1 connected 0-5460
216a5ea51af1faed7fa42b0c153c91855f769321 10.135.78.196:7000@17000 slave 763646767dd5492366c3c9f2978faa022833b7af 0 1524047703512 1 connected
0441f7534aed16123bb3476124506251dab80747 10.135.64.55:7000@17000 slave 763646767dd5492366c3c9f2978faa022833b7af 0 1524047703512 1 connected
f63c210b13d68fa5dc97ca078af6d9c167f8c6ec 10.135.78.196:7001@17001 master - 0 1524047703000 2 connected 5461-10922
f90c932d5cf435c75697dc984b0cbb94c130f115 10.135.78.153:7001@17001 slave f63c210b13d68fa5dc97ca078af6d9c167f8c6ec 0 1524047703000 2 connected
00eb2402fc1868763a393ae2c9843c47cd7d49da 10.135.64.55:7001@17001 slave f63c210b13d68fa5dc97ca078af6d9c167f8c6ec 0 1524047703110 2 connected
5f4bb09230ca016e7ffe2e6a4e5a32470175fb66 10.135.64.55:7002@17002 myself,master - 0 1524047703000 5 connected 10923-16383
af75fc17e552279e5939bfe2df68075b3b6f9b29 10.135.78.153:7002@17002 slave 5f4bb09230ca016e7ffe2e6a4e5a32470175fb66 0 1524047702510 5 connected
19b8c9f7ac472ecfedd109e6bb7a4b932905c4fd 10.135.78.196:7002@17002 slave 5f4bb09230ca016e7ffe2e6a4e5a32470175fb66 0 1524047702009 5 connected 
```

Enter fullscreen mode Exit fullscreen mode

## 更换故障节点

现在，假设我们已经完全丢失了第三个节点，并希望用一个全新的节点替换它。

```
$ redis-cli -c -p 7000 cluster nodes
763646767dd5492366c3c9f2978faa022833b7af 10.135.78.153:7000@17000 myself,master - 0 1524047906000 1 connected 0-5460
216a5ea51af1faed7fa42b0c153c91855f769321 10.135.78.196:7000@17000 slave 763646767dd5492366c3c9f2978faa022833b7af 0 1524047906811 1 connected
0441f7534aed16123bb3476124506251dab80747 10.135.64.55:7000@17000 slave,fail 763646767dd5492366c3c9f2978faa022833b7af 1524047871538 1524047869000 1 connected
f90c932d5cf435c75697dc984b0cbb94c130f115 10.135.78.153:7001@17001 slave f63c210b13d68fa5dc97ca078af6d9c167f8c6ec 0 1524047908000 2 connected
f63c210b13d68fa5dc97ca078af6d9c167f8c6ec 10.135.78.196:7001@17001 master - 0 1524047907318 2 connected 5461-10922
00eb2402fc1868763a393ae2c9843c47cd7d49da 10.135.64.55:7001@17001 slave,fail f63c210b13d68fa5dc97ca078af6d9c167f8c6ec 1524047872042 1524047869515 2 connected
19b8c9f7ac472ecfedd109e6bb7a4b932905c4fd 10.135.78.196:7002@17002 master - 0 1524047907000 6 connected 10923-16383
af75fc17e552279e5939bfe2df68075b3b6f9b29 10.135.78.153:7002@17002 slave 19b8c9f7ac472ecfedd109e6bb7a4b932905c4fd 0 1524047908336 6 connected
5f4bb09230ca016e7ffe2e6a4e5a32470175fb66 10.135.64.55:7002@17002 master,fail - 1524047871840 1524047869314 5 connected 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们必须通过在集群的每个单个节点(甚至是从节点)的**上发出`CLUSTER FORGET <node-id>`来忘记丢失的节点。** 

```
for id in 0441f7534aed16123bb3476124506251dab80747 00eb2402fc1868763a393ae2c9843c47cd7d49da 5f4bb09230ca016e7ffe2e6a4e5a32470175fb66; do 
    for port in 7000 7001 7002; do 
        redis-cli -c -p ${port} CLUSTER FORGET ${id}
    done
done 
```

Enter fullscreen mode Exit fullscreen mode

检查我们是否忘记了故障节点:

```
$ redis-cli -c -p 7000 cluster nodes
763646767dd5492366c3c9f2978faa022833b7af 10.135.78.153:7000@17000 myself,master - 0 1524048240000 1 connected 0-5460
216a5ea51af1faed7fa42b0c153c91855f769321 10.135.78.196:7000@17000 slave 763646767dd5492366c3c9f2978faa022833b7af 0 1524048241342 1 connected
f63c210b13d68fa5dc97ca078af6d9c167f8c6ec 10.135.78.196:7001@17001 master - 0 1524048240332 2 connected 5461-10922
f90c932d5cf435c75697dc984b0cbb94c130f115 10.135.78.153:7001@17001 slave f63c210b13d68fa5dc97ca078af6d9c167f8c6ec 0 1524048240000 2 connected
19b8c9f7ac472ecfedd109e6bb7a4b932905c4fd 10.135.78.196:7002@17002 master - 0 1524048241000 6 connected 10923-16383
af75fc17e552279e5939bfe2df68075b3b6f9b29 10.135.78.153:7002@17002 slave 19b8c9f7ac472ecfedd109e6bb7a4b932905c4fd 0 1524048241845 6 connected 
```

Enter fullscreen mode Exit fullscreen mode

现在启动一个新节点，在其上安装 redis，并使用我们的集群配置启动 3 个新实例。

这 3 个新节点对集群一无所知:

```
[root@redis-replaced ~]# redis-cli -c -p 7000 cluster nodes
9a9c19e24e04df35ad54a8aff750475e707c8367 :7000@17000 myself,master - 0 0 0 connected
[root@redis-replaced ~]# redis-cli -c -p 7001 cluster nodes
3a35ebbb6160232d36984e7a5b97d430077e7eb0 :7001@17001 myself,master - 0 0 0 connected
[root@redis-replaced ~]# redis-cli -c -p 7002 cluster nodes
df701f8b24ae3c68ca6f9e1015d7362edccbb0ab :7002@17002 myself,master - 0 0 0 connected 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们必须将这些 Redis 实例添加到集群:

```
$ redis-trib add-node --slave --master-id 763646767dd5492366c3c9f2978faa022833b7af 10.135.82.90:7000 10.135.78.153:7000
$ redis-trib add-node --slave --master-id f63c210b13d68fa5dc97ca078af6d9c167f8c6ec 10.135.82.90:7001 10.135.78.153:7000
$ redis-trib add-node --slave --master-id 19b8c9f7ac472ecfedd109e6bb7a4b932905c4fd 10.135.82.90:7002 10.135.78.153:7000 
```

Enter fullscreen mode Exit fullscreen mode

现在我们应该对第三个碎片进行故障转移:

```
[root@redis-replaced ~]# redis-cli -c -p 7002 cluster failover
OK 
```

Enter fullscreen mode Exit fullscreen mode

啊，完成了！

```
$ redis-cli -c -p 7000 cluster nodes
763646767dd5492366c3c9f2978faa022833b7af 10.135.78.153:7000@17000 myself,master - 0 1524049388000 1 connected 0-5460
f90c932d5cf435c75697dc984b0cbb94c130f115 10.135.78.153:7001@17001 slave f63c210b13d68fa5dc97ca078af6d9c167f8c6ec 0 1524049389000 2 connected
af75fc17e552279e5939bfe2df68075b3b6f9b29 10.135.78.153:7002@17002 slave df701f8b24ae3c68ca6f9e1015d7362edccbb0ab 0 1524049388000 7 connected
216a5ea51af1faed7fa42b0c153c91855f769321 10.135.78.196:7000@17000 slave 763646767dd5492366c3c9f2978faa022833b7af 0 1524049389579 1 connected
f63c210b13d68fa5dc97ca078af6d9c167f8c6ec 10.135.78.196:7001@17001 master - 0 1524049389579 2 connected 5461-10922
19b8c9f7ac472ecfedd109e6bb7a4b932905c4fd 10.135.78.196:7002@17002 slave df701f8b24ae3c68ca6f9e1015d7362edccbb0ab 0 1524049388565 7 connected
9a9c19e24e04df35ad54a8aff750475e707c8367 10.135.82.90:7000@17000 slave 763646767dd5492366c3c9f2978faa022833b7af 0 1524049389880 1 connected
3a35ebbb6160232d36984e7a5b97d430077e7eb0 10.135.82.90:7001@17001 slave f63c210b13d68fa5dc97ca078af6d9c167f8c6ec 0 1524049389579 2 connected
df701f8b24ae3c68ca6f9e1015d7362edccbb0ab 10.135.82.90:7002@17002 master - 0 1524049389579 7 connected 10923-16383 
```

Enter fullscreen mode Exit fullscreen mode

## 重述

如果您必须处理裸机服务器，想要一个高度可用的 Redis 集群并有效地利用您的硬件，您有一个构建 Redis 集群的交叉复制拓扑的好选择。

这将非常有效，但有 2 个警告:

1.  集群构建是一个手动过程，因为您必须将主节点放在不同的节点上。
2.  您必须监视您的主服务器的分布，以避免在单个节点故障后出现集群故障。