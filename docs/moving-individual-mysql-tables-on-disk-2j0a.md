# 移动磁盘上的单个 MySQL 表

> 原文：<https://dev.to/moxio/moving-individual-mysql-tables-on-disk-2j0a>

您可能会遇到这样的情况，您希望将一个单独的 MySQL 数据库表移动到另一个(磁盘上的)位置，例如为了释放磁盘空间。事实证明，这一过程远非简单明了。在这篇文章中，我将描述几个我们失败的方法(因为失败是一个学习的好机会)，以及我们最终想出的[解决方案](https://www.moxio.com/blog/28/moving-individual-mysql-tables-on-disk#solution)。

### 为什么要在磁盘上移动表格？

不久前，我们收到一个警报，说我们在 Moxio 的一台服务器上的磁盘空间逐渐变少。我们不断监控我们的服务器，寻找可能威胁正常运行的因素，并在可用磁盘空间低于总磁盘大小的 30%时收到第一个警报。这样，在磁盘实际满之前，我们仍然有足够的时间来处理它。

<figure>[![Graph of free disk space before moving tables](../Images/cbc380a36c78e244bc8668a0f0c223b4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---JfKyzyo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/823/0%2A1K_X4cy158Bclsjb.png) 

<figcaption>移动表格前的空闲磁盘空间图</figcaption>

</figure>

通常在这种情况下，我们可以通过删除旧的备份和临时文件，或者将某些文件存储目录从主硬盘移动到外部存储阵列来释放足够的磁盘空间。然而，这次几乎所有的磁盘空间都被 MySQL 数据库服务器占用了。一些表随着时间的推移不断增长，消耗了越来越多的磁盘空间。由于这是我们必须保留的数据，我们唯一可持续的解决方案是将这些表移动到另一个磁盘上。

### 期望的情况

我们的理想解决方案是只将大型的单个表移动到外部存储阵列。通过这种方法，我们可以将数据库中的其他表保存在(快得多的)SSD 磁盘上，而不会不必要地牺牲性能。那么 MySQL 有可能做到这一点吗？

历史上，MySQL 曾经将所有表数据和索引存储在 [*系统表空间*](https://dev.mysql.com/doc/refman/5.7/en/glossary.html#glos_system_tablespace) 中，由磁盘上的一个或多个 [ibdata 文件](https://dev.mysql.com/doc/refman/5.7/en/glossary.html#glos_ibdata_file)表示。这意味着来自多个数据库和表的数据存储在同一个文件中，不可能将其中一个移动到另一个位置。然后 MySQL 4.1.1 为 InnoDB 引入了带有 [innodb_file_per_table](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_file_per_table) 设置的[file-per-table tablespaces](https://dev.mysql.com/doc/refman/5.7/en/innodb-multiple-tablespaces.html)，它将在单独的[中存储新创建的表的数据和索引。每个表的 ibd 文件](https://dev.mysql.com/doc/refman/5.7/en/glossary.html#glos_ibd_file)。这个设置在 MySQL 5.6.6 中默认启用。

从 MySQL 5.6 开始，在使用每个表一个文件的表空间时，可以在 CREATE TABLE 中使用 DATA DIRECTORY = 'path '子句[将表的数据放在主 MySQL 数据目录](https://dev.mysql.com/doc/refman/5.7/en/tablespace-placing.html)之外。这意味着所需的设置在技术上是可能的。

### 失败的方法

然而，在我们的例子中，我们处理的是现有的表。在最终找到令人满意的解决方案之前，我们尝试了几种不成功的方法来将它们移动到另一个目录。

#### 创建后更改数据目录

由于在创建表时可以指定一个数据目录来更改表的存储位置，因此在创建后通过使用 ALTER TABLE 的相同选项来更改该位置似乎是合乎逻辑的。事实上，ALTER TABLE [在语法上支持数据目录作为表选项。然而，文档明确指出这个选项](https://dev.mysql.com/doc/refman/5.7/en/alter-table.html)[被忽略](https://dev.mysql.com/doc/refman/5.7/en/alter-table.html#alter-table-options)(分区时除外)。因此，似乎不可能更改现有表的数据目录。

#### 对数据文件进行符号链接

我们想到的另一种方法是手动将表的数据文件移动到另一个磁盘，将它们符号链接回 MySQL 主数据目录中的原始位置。然而，根据文档，虽然 MySQL 支持符号链接[整个数据库目录](https://dev.mysql.com/doc/refman/5.7/en/symbolic-links-to-databases.html)或[单个 MyISAM 表](https://dev.mysql.com/doc/refman/5.7/en/symbolic-links-to-tables.html)，但是使用到 InnoDB 表[的符号链接不被支持](https://dev.mysql.com/doc/refman/5.7/en/symbolic-links-to-tables.html)，并且可能导致奇怪的问题。

#### 在所需位置创建副本

由于数据目录只能在创建表时指定，我们还尝试在所需的位置创建表的副本，将所有数据从旧表复制到新副本，并重命名新表以取代旧表。这看起来有点像:

```
SHOW CREATE TABLE `table_name`; 
```

Enter fullscreen mode Exit fullscreen mode

这将返回创建 table_name 表结构的 CREATE TABLE 语句。我们现在执行确切语句，但是替换一个新的表名并附加一个数据目录子句:

```
CREATE TABLE `table_name_new` /* ... */ DATA DIRECTORY='/path/to/desired/location'; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们在模式中有了一个空的 table_name 副本。然后，我们将现有表中的所有数据复制到新表中，并发出一个[原子重命名](https://dev.mysql.com/doc/refman/5.7/en/rename-table.html)来交换这两个表:

```
INSERT INTO `table_name_new` SELECT * FROM `table_name`; RENAME TABLE `table_name` TO `table_name_old`, `table_name_new` TO `table_name`; 
```

Enter fullscreen mode Exit fullscreen mode

然而，在这里我们遇到了问题。当将 INSERT 运行到...选择查询，MySQL 失败，错误消息为

```
ERROR 1206 (HY000): The total number of locks exceeds the lock table size 
```

Enter fullscreen mode Exit fullscreen mode

事实证明，当使用 INSERT INTO 时...SELECT，MySQL 需要对从源表中读取的行设置锁，以确保正确的复制。这些锁占用了 [InnoDB 缓冲池](https://dev.mysql.com/doc/refman/5.7/en/innodb-buffer-pool.html)中的空间。这个池的大小可以使用 [innodb_buffer_pool_size](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_buffer_pool_size) 进行配置，但是默认设置为 128 MB。由于我们试图移动的表非常大(超过 5000 万行)，MySQL 必然会耗尽其容纳锁的空间。

即使我们可以充分地增加 innodb_buffer_pool_size 来实现这一点，但仔细想想，这种方法还是非常低效的。当将数据插入新副本时，MySQL 必须将数据序列化到磁盘并为其建立索引，这需要花费大量时间。然而，我们已经有了序列化的数据和索引:它们就在原始表中！我们不应该让 MySQL 从头开始重新创建整个索引和数据文件，而是应该寻找一种方法来重用已经存在的表数据。

### 解

最终，基于 MySQL 手册中关于将表空间复制到另一个 MySQL 实例[的指南，我们找到了在磁盘上平滑移动表的解决方案。解决方案归结为移动磁盘上的现有表空间，删除并重新创建具有所需数据目录的表，然后将保存的表空间重新附加到新表。](https://dev.mysql.com/doc/refman/5.7/en/tablespace-copying.html)

一步一步，这看起来如下。首先，我们确保所有数据都从 MySQL 的缓存和缓冲区刷新到磁盘上的表空间:

```
FLUSH TABLES `table_name` FOR EXPORT; 
```

Enter fullscreen mode Exit fullscreen mode

FLUSH TABLES 还会在连接期间锁定表，或者直到我们解除锁定。该锁确保在我们移动文件时表中的数据不会改变。为了维护这些锁，我们应该保持打开 MySQL 连接，我们在其中运行刷新表....在新的终端窗口中，我们将表空间文件移动到一个临时位置。在这种情况下，我们使用我们的主目录:

```
$ mv /var/lib/mysql/database_name/table_name.{ibd,cfg} ~ 
```

Enter fullscreen mode Exit fullscreen mode

现在回到我们打开的 MySQL 会话，我们可以释放锁(表空间已经安全地保存在一致的状态下)，删除并在所需的位置重新创建表:

```
UNLOCK TABLES; SHOW CREATE TABLE `table_name`; DROP TABLE `table_name`; CREATE TABLE `table_name` /* ... */ DATA DIRECTORY='/path/to/desired/location'; 
```

Enter fullscreen mode Exit fullscreen mode

这也将为新创建的表在磁盘上(所需位置)创建新的表空间文件。我们不想要这些(因为我们想放回旧的表空间文件)，所以我们丢弃了这个新的表空间:

```
ALTER TABLE `table_name` DISCARD TABLESPACE; 
```

Enter fullscreen mode Exit fullscreen mode

此时，我们可以将保存的原始表空间文件复制到 MySQL 现在需要它们的位置，确保保留所有权和权限:

```
$ cp -a ~/table_name.{ibd,cfg} /path/to/desired/location 
```

Enter fullscreen mode Exit fullscreen mode

最后一步是让 MySQL 从磁盘导入原始表空间文件:

```
ALTER TABLE `table_name` IMPORT TABLESPACE; 
```

Enter fullscreen mode Exit fullscreen mode

除了将表空间文件复制到另一个磁盘之外，整个过程非常快，因为 MySQL 只获取原始的表数据和索引，而不必重新构建它们。

### 重述

在几次失败的尝试之后(尽管在这次尝试中我们学到了很多关于 MySQL 内部的知识)，我们最终找到了一个有效地将表移动到磁盘上另一个位置的解决方案。使用这种方法，我们将一些最大且增长最快的表移动到外部存储阵列，释放了保持服务器运行所需的磁盘空间。

<figure>[![Graph of free disk space after moving tables](../Images/b5d0b558fd689ad2c595c81c5e270c38.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vSxwuDxM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/828/0%2AIDJHzccNPmcY6iFG.png) 

<figcaption>移动表后的空闲磁盘空间图</figcaption>

</figure>

另一件要记住的事情是:如果你知道一个数据库表会变得很大，那么要提前计划它在磁盘上的位置。当创建表时，在磁盘上指定一个自定义位置(使用数据目录)很简单，可以省去本文中描述的麻烦。

* * *

##### 喜欢看这个帖子？

我和我的同事们定期写类似这样的与 webdev 相关的博客帖子来分享我们学到或发现的东西。你可以在 [Moxio 博客](https://www.moxio.com/blog)上找到我们所有的帖子。通过在 Twitter 上关注 [me](https://twitter.com/arnoutboks) 或 [Moxio](https://twitter.com/moxio) 或订阅我们的 [RSS 源](https://www.moxio.com/rss/blog)来获得新帖子的通知。