# RUM 猜想——关于数据访问的推理

> 原文：<https://dev.to/frosnerd/rum-conjecture---reasoning-about-data-access-4781>

# 动机

在我之前的一篇文章中，我谈到了[选择正确的数据模型](https://dev.to/frosnerd/making-the-invalid-impossible---choosing-the-right-data-model-9e6)以避免无效状态并使代码更容易理解。除了选择正确的数据模型，选择正确的实现也很重要。您可能需要决定您的集合是使用基于散列的实现还是基于树的实现。对于在数据库中持久化数据，你也有不同的选择，例如 [SQLite](https://www.sqlite.org/index.html) 、 [PostgreSQL](https://www.postgresql.org/) 或 [Cassandra](http://cassandra.apache.org/) 。如何决定用哪一个？

当*读取*(R)*更新* (U)并将数据存储在*内存* (M)中时，数据系统研究人员开发了一个理论模型来推理和比较不同数据结构的开销。他们在所谓的 *RUM 猜想*【1】中公式化了结果。在这篇博文中，我们将探讨朗姆酒的开销以及与之相关的权衡。我们也在研究这个猜想以及它对我们开发者的意义。

这篇博文是关注访问开销的数据结构系列文章的第一部分:

1.  [RUM 猜想——关于数据访问的推理](#)
2.  [读取高效的数据结构](https://dev.to/frosnerd/read-efficient-data-structures-57i5)
3.  [更新高效的数据结构](https://dev.to/frosnerd/update-efficient-data-structures-7cn)
4.  [内存高效的数据结构](https://dev.to/frosnerd/memory-efficient-data-structures-2hki)

该职位的结构如下。第一部分正式定义了朗姆酒管理费用。下一节介绍 RUM 猜想，它解释了三个开销是如何联系在一起并相互影响的。我们还将看一些示例实现。之后会有一个关于 RUM 猜想对我们开发者的意义的讨论。我们将结束这篇博文，并展望未来的文章。

# 朗姆酒饮料

给定*基础数据*作为存储在数据管理系统中的主要数据，*辅助数据*作为索引等附加数据以提高访问性能，我们可以定义以下访问度量:

*   *BR* 为读取的基本数据量
*   *AR* 为读取的辅助数据量
*   *RR* 读查询返回的数据量
*   *BU* 为更新的基础数据量
*   *AU* 为更新的辅助数据量
*   *BM* 存储基本数据所需的空间量
*   *AM* 为存储辅助数据所需的空间量

[1]中的作者将 RUM 开销定义如下:

*   读取开销 *RO = (BR + AR) / RR*
*   更新开销 *UO = (BU + AU) / BU*
*   内存开销 *MO = (BM + AM) / BM*

虽然这个定义在我看来很直观，但有一个问题。执行更新时，您可能需要首先执行读取操作(例如，查找要更新的值)，这取决于您使用的访问模式和数据结构。本文中描述的更新开销没有包括这一点。

在我看来，如果更新需要读取，那么在更新开销计算中包含读取开销是有意义的。在后面的部分，我们将看到一个简单的例子，以及它的重要性。因此，在这篇博文的其余部分，我们将使用以下定义:

*   **读取开销** *RO = (BR + AR) / RR*
*   **更新开销** *UO = (BU + AU) / BU + RO*
*   **内存开销** *MO = (BM + AM) / BM*

现在，让我们看看在为数据选择正确的访问方法时，不同的开销是如何相互关联的。

# 朗姆猜想

## 定义

> **朗姆猜想。**一种访问方法，可以为读取、更新和内存开销中的两个设置上限，也可以为第三个开销设置下限。[1]

换句话说，选择正确的访问方法变成了一个优化问题。例如，如果您的数据结构应该支持快速读取并且消耗很少的空间，那么您必须接受更高的更新开销。为了形象化这种效应，我们来看一个与论文中的例子有些不同的例子。

## 尽量减少朗姆酒开销

考虑以下问题:我们想要存储一组 *N* 整数(即不允许重复)。为了直观地了解不同开销之间的权衡，我们将查看三种不同的实现，每种实现都使三种开销中的一种最小化。为了简单起见，让我们假设我们可以存储的数字在 0 到 1000 之间。

给出的例子是非常简单的解决方案。您可能知道其他更适合的数据结构(例如基于散列或树的实现)。然而，看看下面的实现，可以直观地看出最小化一个开销会如何影响其他开销。

### 最小化 RO

为了最小化 RO，我们可以将整数存储在大小为`1001`的布尔数组`a`中。数组元素用`false`初始化。为了将一个整数`i`标记为集合的成员，我们设置`a[i] := true`。点查询现在可以通过访问相应索引处的数组来直接查找成员资格。更新以同样的方式执行，不需要额外的读取，因为我们可以通过索引访问元素。这种数据结构所需的空间取决于可以存储在集合中的可能值，并且理论上是无限的。

当选择该实现时，我们得到 RO 最小化实现的如下 RUM 开销:

*   *RO = 1*
*   *UO = 1*
*   *莫→ ∞*

### 最小化 UO

为了最小化 UO，我们将每个插入和删除操作存储在一个支持常量时间追加的序列中(例如一个列表)。更新对应于将值和操作(无论是插入还是删除)追加到更新日志。点查询要求我们从最近一次更新开始遍历日志。如果我们发现一个插入操作的值是我们正在寻找的，那么它就是集合的一个成员。如果我们发现一个删除操作或者到达日志的末尾，那么这个值就不是一个成员。该数据结构所需的空间随着每次更新操作而增长。

对于 UO 最小化实现，我们可以推导出以下 RUM 开销:

*   *【ro】→*
*   *UO = 1*
*   *莫→ ∞*

### 最小化 MO

为了最小化 MO，我们将集合的所有值存储在一个密集数组中。点查询现在需要对数据进行完全扫描。更新操作也需要完全扫描:插入操作需要检查值是否已经存在以避免重复，删除操作需要从序列中删除值。此外，每次删除时，我们都必须填补空白。然而所需的空间是最小的，因为只存储整数，不需要辅助数据。

这为最小化 MO 的实现提供了以下 RUM 开销:

*   ro≤n
*   *UO ≤ N + 2*
*   *MO = 1*

# 寓意

当选择数据的物理表示(或者选择使用特定表示的数据库)时，总是要在读取、更新和内存开销之间进行权衡。考虑到内存层次结构(参见我的前一篇博文)，开销会产生不同的影响，这取决于数据是在 CPU 缓存、主内存还是持久存储中被访问。

记住这一点，首先了解您要存储的数据是什么样的以及典型的访问模式是什么是有意义的。会有很多写吗？读取所需的响应时间是多少？你有多少空间？

# 展望

展望未来，我希望更多地了解一些常见的数据结构，重点关注低读取(例如 B 树[2]和跳过列表[3])、写入(例如日志结构的合并树[4])和空间(例如 Bloom Filters [5]和 zonemap[6])开销。也有关于可以基于访问模式(例如数据库破解[7]和自适应索引[8])进行调整的数据结构的研究。所以请继续关注即将发布的帖子。

在选择数据结构或数据库时，您是否考虑过 RUM 开销？您了解您每天使用的数据库的内部吗？你同意我对更新开销的修改吗，包括读取开销？请在评论中告诉我你的想法。

# 参考文献

*   [1]阿萨纳苏利斯，m .，凯斯特，m .，马斯，L.M .，斯托伊察，r .，伊德里奥斯，s .，艾拉马基，a .，卡拉汉，m .，2016 年。设计访问方法:RUM 猜想。在 EDBT(2016 卷，第 461-466 页)。 [PDF](https://stratos.seas.harvard.edu/files/stratos/files/rum.pdf)
*   [2]格雷费。现代 B 树技术。找到了。趋势数据库，3(4):203–402，2011。
*   [3] W .皮尤。跳过列表:平衡树的概率替代。CACM，33(6):668–676，1990 年。
*   [4] P. E .奥尼尔、e .程、d .高立克和 E. J .奥尼尔。日志结构的合并树(LSM 树)。信息学报，33(4):351–385，1996。
*   [5]布鲁姆。允许误差的哈希编码中的空间/时间权衡。CACM，13(7):422–426，1970 年。
*   [6]弗朗西斯科。Netezza 数据设备架构:高性能数据仓库和分析平台。IBM 红皮书，2011 年。
*   [7] S. Idreos，M. L. Kersten 和 S. Manegold。数据库破解。2007 年在 CIDR。
*   [8] G .格雷费、f .哈利姆、s .伊德里奥斯、h .库诺和 s .马内戈尔德。自适应索引的并发控制。PVLDB，5(7):656–667，2012。
*   由 O'Dea 在 WikiCommons 制作的封面图片，CC BY-SA 3.0，[https://commons.wikimedia.org/w/index.php?curid=12469449](https://commons.wikimedia.org/w/index.php?curid=12469449)