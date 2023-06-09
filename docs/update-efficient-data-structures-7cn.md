# 更新有效的数据结构

> 原文：<https://dev.to/frosnerd/update-efficient-data-structures-7cn>

# 简介

RUM 系列的[第一篇博文](https://dev.to/frosnerd/rum-conjecture---reasoning-about-data-access-4781)介绍了 RUM 猜想。它描述了在设计数据结构和访问方法时应该考虑的读取(RO)、更新(UO)和内存(MO)开销之间的权衡。

在这篇文章中，我们想仔细看看实践中常用的为低更新开销而设计的数据结构，即日志和日志，日志结构的合并树，以及 B 树和一些 B 树扩展。

这篇博文是朗姆酒系列的第三部分:

1.  [RUM 猜想——关于数据访问的推理](https://dev.to/frosnerd/rum-conjecture---reasoning-about-data-access-4781)
2.  [读取高效的数据结构](https://dev.to/frosnerd/read-efficient-data-structures-57i5)
3.  [更新高效的数据结构](#)
4.  [内存高效的数据结构](https://dev.to/frosnerd/memory-efficient-data-structures-2hki)

为了比较不同的实现，我们使用了与其他帖子中相同的例子。任务是实现一组整数，这一次关注一个小的更新开销。

该职位的结构如下。在第一部分，我们想回顾一下开篇文章中更新开销最小的解决方案。下一节揭示了如何在现实生活场景中采用更新最佳解决方案，也称为日志记录或日志记录。第三部分介绍了日志结构合并树(LSM 树)的概念，这是一类结合了不可变日志和高效读取数据结构的数据结构。下一节简要讨论 B 树，因为它们在数据库索引中扮演着重要角色，而且对于 LSM 树也是如此。倒数第二节将阐明在选择基于 LSM 树实现的数据库时的实际含义。我们通过总结要点来结束这篇文章。

# 更新-最优解

实现恒定(和最小)更新开销的最简单方法是将每个更新附加到事务日志中。以我们想要实现一组整数的用例为例，我们将插入和删除操作存储在一个序列中，总是附加最新的操作。由于点查询可能必须遍历整个日志，而日志也可能无限增长，因此我们得出了以下 RUM 开销:

*   *【ro】→*
*   *UO = 1*
*   *莫→ ∞*

与读取最佳解决方案相反，实际上使用的是更新最佳方法。根据上下文，它被称为日志记录或日志记录。

# 日志/日志

## 概念

下图展示了 set `{0..6}`在几次更新操作后的样子。

[![write ahead log](img/6d243d6badaf8d4940d8941917ef4f24.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PvxR5mWY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/udhy0j2muyn8v375u3f6.png)

写入是连续的，因为我们只追加新数据，而日志的其余部分保持不变。事实证明，磁盘上的顺序写入与内存中的随机存取相当，甚至更快。[1]

日志记录/日志记录的用例是什么？日志存在的时间越长，其读取和内存开销就越小。

这种非常简单的数据结构的糟糕的读取性能可以通过两种方式(或者两者的结合)来补偿。首先，我们可以通过顺序地只读取一次日志来限制对日志的读取量，异步地将数据传输到读取效率更高的数据结构，例如树。

像 [PostgreSQL](https://www.postgresql.org/) 这样的数据库将事务存储在一个[预写日志](https://en.wikipedia.org/wiki/Write-ahead_logging)文件中。使用[直接 IO](http://www.alexonlinux.com/what-is-direct-io-anyway) 绕过页面缓存，并附加到日志中，可以最大限度地缩短数据库确认事务的时间，而不会有丢失数据的风险。然后它可以异步遍历日志，将事务应用到实际的表中。该日志仅在应用时被读取一次。在崩溃的情况下，数据库还可以使用日志来重放尚未执行的事务。利用预写日志给我们带来了持久性和原子性，而不会牺牲吞吐量。

另一个非常相似的用例是在[日志文件系统](https://en.wikipedia.org/wiki/Journaling_file_system) (JFS)。在 JFSs 中，更改在应用到主文件系统结构之前首先存储在日志中。

其次，我们可以通过建立包含某些事务或消息的日志文件偏移量的索引来避免搜索整个日志。虽然这对于我们的整数集示例并不实用，因为索引不会比实际数据小很多，但它用于消息队列系统，例如 [Apache Kafka](https://kafka.apache.org/) ，这些系统依赖于高消息吞吐量，并且仅按顺序使用消息。

我们如何处理潜在的无限增长的内存开销？一种选择是对日志施加限制。Kafka 允许用户配置基于时间( [`log.retention.hours`](https://kafka.apache.org/08/documentation.html) )或大小( [`log.retention.bytes`](https://kafka.apache.org/08/documentation.html) )的保留策略。

另一种常用的技术叫做压缩。从上面的集合例子来看，只有当一个值以前被添加过，才需要存储这个值已经从集合中删除的信息。在压缩过程中，我们会删除不影响最终状态的事务。

下图说明了我们如何使用压缩来减少整数集的大小。我们正在删除冗余的事务，留下一个较短的日志。可以基于时间或空间约束来执行压缩。

[![compaction in logs](img/99927009e53ca6c14aa663f1a3db4d14.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6VbuGT8L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4xps7ltu8aq7fv0mkhb7.png)

## 朗姆酒饮料

在没有任何压缩、保留或索引的日志的最简单实现中，RUM 开销与更新优化解决方案中的相同。在大小为 *m* 的日志中，我们最多有 *m* 个读操作，直到我们发现一个值是否是集合的成员。更新开销是 1，因为我们只追加。除非保留策略到位，否则内存开销将无限增长。压缩也有助于减少内存开销。

## 渐近复杂性

渐近复杂度直接对应于 RUM 开销。根据日志大小，我们得到线性增长的读取和内存需求，而在一般和最坏的情况下，更新复杂性保持不变。如果我们执行常规的压缩，我们可以保持事务数量 *m* 接近整数数量 *n* 。

| 类型 | 平均的 | 最坏情况 |
| --- | --- | --- |
| 阅读 | *O(m)* | *O(m)* |
| 更新 | *O(1)* | *O(1)* |
| 记忆 | *O(m)* | *O(m)* |

研究人员和数据库开发人员花费了大量的时间将日志与其他数据结构和技术结合起来，以补偿糟糕的读取和内存开销。一种经常使用的结合了日志记录和索引的高效更新数据结构被称为日志结构合并树。

# 日志结构的合并树

## 概念

日志结构的合并树(LSM 树)[2]更多的是关于如何组合不同层次的数据结构的框架，而不是实际的数据结构。它们在概念层面上解决了上一节中讨论的仅附加日志的问题，我们将在本节中研究这些问题。LSM 树的实际性能很大程度上取决于具体的实现和使用的数据结构。

LSM 树的主要思想是维护数据结构的层次结构，其中的每一个都针对底层存储介质进行了优化。在两级 LSM 树中，第一级通常存储在内存中，而第二级存储在磁盘上。下图说明了两级 LSM 树的概念。

[![two level LSM tree](img/7e7bcd8d928da584c29a687259b9e4fb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--meeyQ4yN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tnpiz7cqfw0pem3qq0iv.png)

原始文件建议对单个树使用类似于 [B 树](https://en.wikipedia.org/wiki/B%20tree)的结构。这很有用，因为我们可以根据文件系统的块大小调整节点大小。然而，由于第一层驻留在内存中，我们可以使用其他自平衡树作为选择，如 [2-3 树](https://en.wikipedia.org/wiki/2%E2%80%933_tree)或 [AVL 树](https://en.wikipedia.org/wiki/AVL_tree)。

像[Bigtable](https://cloud.google.com/bigtable/)【3】或[Apache Cassandra](http://cassandra.apache.org/)【4】这样的数据库实际上并不将更高的级别存储为树，而是以排序字符串表(SSTable)文件格式存储。SSTable 是一个键值对文件，按键排序。另外，在数据文件中存储从键到偏移量的映射也很常见。

如前所述，当在内存中存储第一级时，我们通常将更新保存到一个简单的预写日志中，以确保持久性。读取查询首先到达内存级别，这是一种读取效率高的数据结构。如果找不到钥匙，我们就进入下一关。为了避免扫描所有层，我们可以使用近似索引，例如[布隆过滤器](https://en.wikipedia.org/wiki/Bloom_filter)，来确定一个关键字是否被包括在 LSM 树的部分中。

更新在内存级别就地执行。这不是问题，因为 RAM 对于随机访问来说足够快，我们可以在到达预写日志时立即确认事务。当内存级别已满时，我们会坚持到下一个级别。如果我们在所有级别上使用 B 树，它可以简单地保持树的原样。对于表，我们将记录存储为排序的键值对，并在需要时添加索引。

基于磁盘的更新最终会填满磁盘。这就是压缩再次派上用场的地方。我们可以不时地合并不同的树/表，有效地减少总大小。成功合并后，我们可以丢弃过时的数据。这也是名称*日志结构合并树*的来源。我们将更新存储在树的日志中，合并它们以便压缩，从而减少不断增长的读取和内存开销的影响。

## 朗姆酒饮料

LSM 树的实际 RUM 开销很大程度上取决于具体的实现。它们对应于不同级别的朗姆酒管理费。我们还必须努力将一个级别保持到下一个级别，并在压缩的情况下进行合并。

## 渐近复杂性

自然，渐近复杂性也取决于实际的实现和使用的数据结构。如果我们保持数据尽可能紧凑，我们可以根据集合 *n* 中的实际元素而不是事务 *m* 实现对数读取性能，假设我们对所有级别使用排序数据结构。更新复杂度将对应于内存中的数据结构。然而，如果我们使用预写日志，我们可以使它看起来像常数时间。

如果数据库负载很高，并且同时收到许多查询，那么性能就会回到正常日志所能保证的水平。一些实施，例如 [SwayDB](http://www.swaydb.io/) ，如果压实跟不上，则提供背压机制。

我们现在明白了为什么 LSM 树是一个强大的数据库工具，旨在实现卓越的更新性能。由于最初的 LSM 树论文建议使用类似 B 树的结构用于各个层次，我们想在下一节快速看一下 B 树。

# B 树

## 概念

b 树[5]是自平衡二分搜索法树的推广，其中一个节点可以有两个以上的孩子。对于每个节点，左边的子树只包含小于当前值的元素，这给了我们对数搜索的复杂性。下图显示了一个 B 树，其最大块大小为 4，包含集合 *{0..6}* 。

[![btree](img/3f08913f941979634b1c8ddd068bcbc7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2gpuhgXi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rvkqugt1jcjmmjzz8fzs.png)

b 树与二分搜索法树具有相同的渐进读取、更新和存储复杂度。那么，为什么我们在一篇关于编写高效数据结构的文章中提到它们呢？

在 B 树中，您可以调整存储在一个节点中的元素数量，以分别适应文件系统的块大小或硬盘的扇区大小。这使得它们比常规的二分搜索法树，例如上一篇文章中的红黑树，更适合存储在顺序存储介质上。此外，还有许多扩展和衍生，重点是减少更新开销。树和扩展广泛应用于数据库和文件系统实现中。

## 两个 B 树扩展

### B+树

与 B 树不同， [B+树](https://en.wikipedia.org/wiki/B%2B_tree)只在叶节点存储记录。虽然这个扩展在我们的整数集例子中不是很有用，但它确实严重影响了 B+树通常用作索引的数据库的性能。

它们并不是为了获得更好的更新性能而特别调整的，但是允许比 B 树更高的分支因子，因为只有键存储在中间节点中。这可能使我们能够将所有非叶节点存储在内存中，将磁盘 I/O 限制在必须修改数据库记录的时候。

此外，B+树存储从一个叶节点到下一个叶节点的指针，大大加快了跨多个叶节点的查询速度。

### 分形树

[分形树](https://en.wikipedia.org/wiki/Fractal_tree_index#cite_note-cola-1) [6]与 B 树具有相同的渐近读取复杂度。但是，它们提供了更好的更新性能。这是通过在每个节点中维护缓冲区，在中间位置存储插入来实现的。这提高了 B 树的最坏情况下的性能，在这种情况下，每次磁盘写入可能只改变少量数据(另见[写入放大](https://en.wikipedia.org/wiki/Write_amplification))。

分形树可用作 [MySQL](https://www.mysql.com/) 和 [MariaDB](https://mariadb.org/) (见 [TokuDB](https://en.wikipedia.org/wiki/TokuDB) )和 [MongoDB](https://www.mongodb.com/) (见 [TokuMX](https://en.wikipedia.org/wiki/TokuMX) )的存储引擎。

## 朗姆酒饮料

B 树的 RUM 开销取决于分支因子和树的当前状态。搜索查询必须从根节点开始，在节点内执行二分搜索法以找到要跟随的相应子指针。虽然我们可能需要比二分搜索法树中更少的指针，但是我们必须在一个节点中执行更多的比较操作。因此，读取开销与树中元素的数量成正比。

更新开销等于读取开销加上一次插入或删除元素的操作。为了保持树的平衡，我们可能需要分割(插入)或合并(删除)一个节点。与红黑树类似，这可能会影响树的更大部分。

内存开销也与存储在树中的值的数量成比例。但是，我们可以通过增加每个节点的最大大小来减少内存开销。这样我们将有一个更小的树，并且需要存储更少的指针。我们还可以将节点大小与机器的页面大小/块大小对齐。

## 渐近复杂性

如上所述，渐近复杂度相当于二分搜索法树的复杂度。

| 类型 | 平均的 | 最坏情况 |
| --- | --- | --- |
| 阅读 | *O(对数 n)* | *O(对数 n)* |
| 更新 | *O(对数 n)* | *O(对数 n)* |
| 记忆 | *O(n)* | *O(n)* |

# LSM 树在练习

现在我们已经对更新高效数据结构有了一个概述，让我们来看看一些数字。现在我想把重点放在 LSM 树上，因为它们在不同的数据库中被广泛使用。以下非详尽列表包含使用或支持 LSM 树来持久存储数据的数据库。

*   [Bigtable](https://cloud.google.com/bigtable/)
*   Apache hbase
*   [LevelDB](https://github.com/google/leveldb)
*   [MongoDB](https://www.mongodb.com/)
*   [SQLite4](https://sqlite.org/src4/doc/trunk/www/lsmusr.wiki)
*   [扫描工具](https://tarantool.io/en/developers.html)
*   [RocksDB](https://rocksdb.org/)
*   阿帕奇·卡珊德拉
*   [InfluxDB](https://www.influxdata.com/blog/new-storage-engine-time-structured-merge-tree/)

在为您选择处理持久性的数据库时，选择正确的产品并不容易。在您决定了一个配置之后，您可以坚持使用默认配置，也可以根据您的需要进行调整。

我想使用一个由开发人员在 [SwayDB](%5BSwayDB%5D(http://www.swaydb.io/)) 背后进行的基准测试来可视化不同配置参数之间的一些权衡。SwayDB 是一个高度可配置的、类型安全的和非阻塞的键值存储库，用于用 Scala 编写的单/多磁盘和内存存储。这些数字来自对一台【2014 年中期 MacBook 的测试。

我们将查看不同场景下的读写吞吐量(每秒操作数)。

*   第一个变量是读和写操作是以升序(顺序)还是无序(随机)键执行的。
*   第二个变量是数据库是如何设置的。我们正在比较 2 级内存 LSM 树、基于 8 级内存映射文件的 LSM 树( [`java.nio.MappedByteBuffer`](https://docs.oracle.com/javase/8/docs/api/java/nio/MappedByteBuffer.html) )和基于 8 级常规文件的 LSM 树( [`java.nio.channels.FileChannel`](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/FileChannel.html) )。

内存中的数据结构速度很快，但不持久。内存映射文件提供了持久性和读写性能，但由于它们不能保证在致命的服务器崩溃时写入，因此不如常规文件访问持久。使用直接 IO 可以实现最大的持久性，但是我不确定这是否可以在 SwayDB 中配置。

首先让我们看看压缩过程对数据库性能的影响。下图比较了随机读取压缩期间和之后的读取吞吐量。

[![compaction performance](img/d550037996f3dd46ce5590d543b61516.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--d8w_T3C7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rkwulpc59trwvhxqvkp9.png)

压缩过程会将数据库的随机读取性能降低 50%左右，这与持久性配置无关！当使用 LSM 树来保持相当好的读取性能并避免消耗太多空间时，压缩是必不可少的。但是，应该明智地选择压缩策略，因为如果它发生得太频繁或不够频繁，可能会付出很高的代价。

第二个有趣的问题是键排序对写性能的影响。下图比较了在两级内存 LSM 树中使用有序键和无序键时的写吞吐量。

[![lsm write performance](img/9f258f45f079c4ace64764898fb223a4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NVipPxFd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ziauq01juzuxiaae1shs.png)

太神奇了！当写入按顺序进行而不是混排时，我们可以获得大约 100%的速度提升。这表明，根据所使用的查询和数据结构，可能会有相当大的性能差异。SwayDB 在底层使用跳转列表，顺序插入只是追加到跳转列表的末尾，根据需要提升元素。然而，随机插入会导致在跳过列表的中间修改和重新链接多个元素和级别。

最后但并非最不重要的是，我们想调查用持久性换取吞吐量的性能影响。在下图中，我们比较了基于 2 级内存、8 级内存映射文件和 8 级常规文件的 LSM 树的随机写入和随机写入的写入吞吐量。

[![lsm durability trade-off](img/46c3bc44fbdf624fe2667899c6d8a731.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5fdJMTR6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e2322xpu0rjqjs1t0pg7.png)

正如所料，内存中的 LSM 树提供了最好的性能。虽然常规文件访问提供了最佳的持久性，但它也具有最差的性能。如果在致命崩溃的情况下，数据丢失和随后从预写日志中恢复是可以接受的，那么内存映射文件可以提供不错的性能。

# 总结

在这篇文章中，我们看了更新最佳解决方案，也称为日志记录，以及它在实践中是如何使用的。我们已经看到，通过维护树状结构(LSM 树)的日志，我们可以在不牺牲太多读取性能的情况下获得不错的写入吞吐量。

我们还简要介绍了 B 树，因为它们在 LSM 树和数据库索引中起着重要的作用。虽然 B 树没有被特别设计为更新高效，但是存在例如旨在减少写放大的扩展。

最后但同样重要的是，我们看到根据您的需求调优您的数据库可以产生显著的性能影响。使用 LSM 树时，压缩和压缩等主题也很重要。

你曾经使用过用 LSM 树来保存状态的数据库吗？你最喜欢的数据库使用的数据结构是什么？你知道任何其他旨在改善更新开销的 B 树扩展吗？它们是如何工作的？请在下面的评论中告诉我！下一篇文章将关注空间高效的数据结构。

# 参考文献

*   [1]雅各布斯，2009 年。大数据的病态。《美国计算机协会通讯》，第 52 卷第 8 期，第 36-44 页。 [ACM 队列发布](https://queue.acm.org/detail.cfm?id=1563874)
*   [2]p .奥尼尔，e .程，d .高立克和 e .奥尼尔，1996 年。日志结构的合并树(LSM 树)。《信息学报》，第 33 卷第 4 期，第 351-385 页。
*   [3] Chang，f .，Dean，j .，Ghemawat，s .，Hsieh，W.C .，Wallach，D.A .，Burrows，m .，Chandra，t .，Fikes，a .和 Gruber，R.E .，2008 年。Bigtable:结构化数据的分布式存储系统。《美国计算机学会计算机系统汇刊》(TOCS)，26(2)，第 4 页
*   [4] Cassandra 文档[“数据是怎么写的？”](https://docs.datastax.com/en/cassandra/3.0/cassandra/dml/dmlHowDataWritten.html)
*   [5]卡莫博士，1979 年。无处不在的 B 树。美国计算机学会计算调查(CSUR)，11(2)，第 121-137 页。
*   [6] Bender，M.A .，Farach-Colton，m .，Fineman，J.T .，Fogel，Y.R .，Kuszmaul，B.C .和 Nelson，j .，2007 年 6 月。缓存无关的流式 B 树。在第十九届 ACM 并行算法和体系结构年会论文集(第 81-92 页)。ACM。
*   封面图片来自美国国会图书馆印刷品和照片部。数字身份证 fsa.8c35555 .公共领域，[https://commons.wikimedia.org/w/index.php?curid=543535](https://commons.wikimedia.org/w/index.php?curid=543535)