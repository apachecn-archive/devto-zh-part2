# MongoDB 写问题:3 个必须知道的警告

> 原文：<https://dev.to/scalegrid/mongodb-write-concern-3-must-know-caveats-504k>

[![](../Images/a2fee10757044ded8b8c7a100f8d90b2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Dtgv-OGE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://uploads.disquscdn.com/images/125e5ddc69932d0f2b8972bd59962a00965e6cb6ed359e27007570ff88239555.png)

[MongoDB](https://scalegrid.io/mongodb.html)中的描述了你可以从它那里得到的写确认的级别。在您的写操作中，这是一个非常重要的设置，理解它的行为是非常有用的，尤其是在分布式 MongoDB 部署中(即[副本集](https://docs.mongodb.com/manual/reference/glossary/#term-replica-set)和[分片集群](https://docs.mongodb.com/manual/reference/glossary/#term-sharded-cluster))。在本帖中，我们讨论了使用 MongoDB 写问题时的 3 个问题。

阅读原文: [MongoDB 写关注:3 个必须知道的告诫](https://scalegrid.io/blog/mongodb-write-concern-3-must-know-caveats/)

## MongoDB 写问题

[MongoDB 的文档](https://docs.mongodb.com/manual/reference/write-concern/)将写问题定义为“*向 MongoDB 请求的对独立 mongod、副本集或分片集群的写操作的确认级别。*

简而言之，写问题是与对 MongoDB 的写操作一起传递的“持久性”的指示。为了澄清，让我们看看语法:

```
{ w: <value>, j: <boolean>, wtimeout: <number> }
Where*,
 w can be an integer | "majority" | , it represents the number of members that must acknowledge the write. Default value is 1.
 j Requests that a write be acknowledged after it is written to the on-disk journal as opposed to just the system memory. Unspecified by default.
wtimeout specifies timeout for the applying the write concern. Unspecified by default.
```

*您可以在[写关注规范文档](https://docs.mongodb.com/manual/reference/write-concern/#write-concern-specification)中找到详细的语法。

*在我们的[了解 MongoDB](https://scalegrid.io/blog/understanding-durability-write-safety-in-mongodb/) 博客中的&写安全耐久性中，了解更多关于可用于常见写关注值的不同“标签”的信息。

示例:

```
db.inventory.insert(
    { sku: "abcdxyz", qty : 100, category: "Clothing" },
    { writeConcern: { w: 2, j: true, wtimeout: 5000 } }
)
```

上述插入的写入问题可以理解为:当“副本集的至少 2 个成员在 5000 毫秒内将其写入它们的日志，或者返回错误”时，确认该写入。选项的写关注值为[多数，](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern._dq_majority_dq_)表示“r *请求确认写操作已经传播到大多数[投票节点](https://docs.mongodb.com/manual/reference/write-concern/#majority-definition)，包括主节点。*

写关注的重要性是显而易见的。增加 w 值会增加写入的延迟，同时也会降低其丢失[的概率。](https://docs.mongodb.com/manual/core/replica-set-rollbacks/)为写操作选择正确的值取决于正在执行的写操作的延迟和持久性要求。

有了关于什么是写问题的背景知识，让我们继续讨论使用写问题时需要记住的三个注意事项。

### 警告 1 : 在没有 wtimeout 的情况下对副本集设置写关注可能会导致写无限期阻塞

上面的[多数定义](https://docs.mongodb.com/manual/reference/write-concern/#majority-definition)(适用于 MongoDB 3.0 及更高版本)指出，确认请求来自大多数“投票节点”。[请注意](https://docs.mongodb.com/manual/reference/write-concern/#wtimeout)*“如果您不指定`wtimeout`选项，并且无法达到写关注级别，写操作将无限期阻塞。”*

这可能会产生意想不到的后果，例如，考虑 2+1 副本集(即主要副本、次要副本和仲裁副本)。如果您的唯一读取副本出现故障，那么所有写操作 w 选项为“多数”的写操作都将无限期阻塞。如果 w 选项设置为 2，也会发生同样的情况。另一个极端的例子是在 3+2 副本集的情况下(主要的、2 个次要的和 2 个仲裁器，[不是](https://docs.mongodb.com/manual/tutorial/add-replica-set-arbiter/#add-an-arbiter)推荐的配置)。即使单个数据节点不可用，所有“多数”写入也会被阻止，因为多数数在本例中为 3。

缓解这个问题的最简单的方法是始终指定一个 wtimeout 值，以便在无法强制执行写操作时查询可以超时。然而，在这种超时错误的情况下，MongoDB [不会撤销](https://docs.mongodb.com/manual/reference/write-concern/#wtimeout)在超时发生之前对一些成员已经成功的写操作。

目前也没有设置来确保写操作到达当前可到达的大多数节点，因此在根据拓扑、期望的持久性和可用性设置写操作关注 w 的值时要小心。

### 警告 2 : 即使使用 w: majority 也可能会丢失数据

似乎很直观的是，一旦一个写被大多数投票成员认可，它的持久性就得到保证。然而，事实并非如此！请记住，当 j 选项未指定时，写操作会在写入内存后立即得到确认。

因此，如果异常断电导致写入所传播到的大部分节点中断(在 [syncPeriodSecs](https://docs.mongodb.com/manual/reference/configuration-options/#storage.syncPeriodSecs) 之前，即在写入被刷新到磁盘之前)，这样的写入可能会丢失。

为了确保写入的持久性，最好不要关闭数据库上的[日志记录](https://docs.mongodb.com/manual/core/journaling/)并将 j 选项设置为 true。事实上，从 MongoDB 3.6 开始，对于使用 WiredTiger 存储引擎的副本集成员来说，nojournal 标志已经被[弃用](https://docs.mongodb.com/manual/reference/program/mongod/#cmdoption-mongod-nojournal)。

当 w 值为“majority”并且在副本集上未指定 j 选项时，确切的持久性行为取决于副本集配置[writeConcernMajorityJournalDefault](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.writeConcernMajorityJournalDefault)的值。当设置为真时(并且当[日志](https://docs.mongodb.com/manual/core/journaling/)被启用时)，它在写入大多数投票成员的日志后确认写入。

旁白:即使打开了[日志功能，](https://scalegrid.io/blog/should-you-enable-mongodb-journalling/)如果在[commit intervals](https://docs.mongodb.com/manual/reference/configuration-options/#storage.journal.commitIntervalMs)持续时间内发生中断，您的写入仍然可能在 [MMAPv1 存储引擎](https://docs.mongodb.com/manual/core/journaling/#journaling-and-the-mmapv1-storage-engine)上丢失。另一方面，WiredTiger 存储引擎[在接收到 j 选项设置为 true 的写操作时，会强制同步日志文件](https://docs.mongodb.com/manual/core/journaling/#journaling-process)。此外，即使 j 设置为 false，只有当大多数数据节点同时崩溃时，对基于最新 WiredTiger 部署[的确认“多数”写入才会丢失。](https://docs.mongodb.com/manual/core/journaling/#journaling-process)

### 警告 3 : w: 0 而设置 j: true 并不能提高写性能

一旦你想起来，这很容易推理，但同样容易忘记。将 w 选项设置为 0 通常是为了以一种“一劳永逸”的方式写入数据库——当您对数据库基础设施相当有信心，并且更关心延迟而不是每次写入的持久性时。但是，如果将 j 选项设置为 true，w 选项将被有效地覆盖，因为数据库将确保在返回之前将写操作写入磁盘日志。

如果您使用写问题来保证写操作的成功，请确保您记住了这三个重要的警告！我们在这里提供帮助，所以请随时通过 [Twitter](https://twitter.com/scalegridio) 或通过[电子邮件](mailto:support@scalegrid.io)联系任何问题。