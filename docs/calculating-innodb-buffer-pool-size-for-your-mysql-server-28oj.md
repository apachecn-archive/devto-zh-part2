# 计算 MySQL 服务器的 InnoDB 缓冲池大小

> 原文：<https://dev.to/scalegrid/calculating-innodb-buffer-pool-size-for-your-mysql-server-28oj>

[![chart](img/39c8cea97f77bc03a8c770aaac73ef6e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yCQX-P0C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://mongodb.scalegrid.io/hubfs/Calculating-InnoDB-Buffer-Pool-Size-for-your-MySQL-Server-ScaleGrid-TW.jpg)

## 什么是 InnoDB 缓冲池？

[InnoDB 缓冲池](https://dev.mysql.com/doc/refman/5.5/en/innodb-buffer-pool.html)是保存 InnoDB、缓冲区、缓存、索引甚至行数据的内存数据结构的内存空间。 [innodb_buffer_pool_size](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_buffer_pool_size) 是 [MySQL](https://www.mysql.com/) 的配置参数，指定 MySQL 分配给 innodb 缓冲池的内存量。这是 MySQL 配置中最重要的设置之一，应该根据可用的系统 RAM 进行配置。

在本文中，我们将向您介绍设置 InnoDB 缓冲池大小值的两种方法，分析这些方法的优缺点，并提出一种独特的方法来根据系统 RAM 的大小获得最佳值。

## 方法 1。经验法则

最常见的做法是将该值设置为系统 RAM 的 [70% - 80%](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) 。尽管这种方法在大多数情况下效果很好，但它并不是在所有配置中都是最佳的。让我们以一个 192GB 内存的系统为例。根据上面的方法，我们得出大约 150GB 的缓冲池大小。然而，这并不是一个真正的最佳数字，因为它没有充分利用系统中可用的大 RAM 大小，并且留下了大约 40GB 的内存。当我们转向具有更大配置的系统时，这种差异可能会更加显著，因为我们应该在更大程度上利用可用的 RAM。

## 方法 2。更微妙的方法

这种方法基于对 InnoDB 缓冲池内部及其交互的更详细的理解，这在《高性能 MySQL》一书中有很好的描述。

让我们看看下面计算 InnoDB 缓冲池大小的方法。

1.  从可用的总 RAM 开始。
2.  减去 OS 所需的适当数量。
3.  减去适合所有 MySQL 需求的数量(比如各种 MySQL 缓冲区、临时表、连接池和复制相关的缓冲区)。
4.  将结果除以 105%，这是管理缓冲池本身所需开销的近似值。

例如，让我们看一个系统，它有 192GB RAM，只使用 InnoDB，日志文件总大小约为 4GB。我们可以按照上述书中的建议，使用“最大 2GB 或总 RAM 的 5%”这样的规则来分配操作系统需求，总计约 9.6GB。然后，我们还将为其他 MySQL 需求分配约 4GB，主要考虑日志文件的大小。对于我们的 InnoDB 缓冲池大小，这种方法产生了大约 170GB，这大约是可用 RAM 大小的 88.5%的利用率。

尽管我们使用“最大 2GB 或总 RAM 的 5%”规则来计算我们的内存分配，以满足上述操作系统需求，但同样的规则并不适用于所有情况，特别是对于具有 2GB 到 32GB 之间的中等大小 RAM 的系统。例如，在一个具有 3GB RAM 的系统中，分配 2GB 来满足操作系统的需求不会给 InnoDB 缓冲池留下太多空间，而分配 5%的 RAM 对于我们的操作系统需求来说太少了。

因此，让我们微调上面的操作系统分配规则，并检查各种 RAM 配置的 InnoDB 计算方法:

### 对于具有小容量 RAM (<= 1GB)的系统

对于运行内存小于 1GB 的系统，最好使用 InnoDB 缓冲池大小的 [MySQL 默认配置值](https://dev.mysql.com/doc/refman/5.7/en/innodb-buffer-pool-resize.html)128 MB。

### 对于具有中等大小 RAM(1GB–32GB)的系统

考虑 RAM 大小为 1GB - 32GB 的系统，我们可以使用以下粗略的启发式方法来计算操作系统需求:

*256MB + 256 * log2(RAM 大小以 GB 为单位)*

这里的合理性在于，对于低 RAM 配置，我们从 256MB 的基本值开始，以满足操作系统的需求，并随着 RAM 数量的增加以对数比例增加这一分配。这样，我们就可以想出一个确定性的公式来为我们的操作系统需求分配 RAM。我们还将为 MySQL 的其他需求分配相同数量的内存。例如，在一个具有 3GB RAM 的系统中，我们将公平地分配 660MB 用于操作系统需求，另外 660MB 用于 MySQL 其他需求，这样 InnoDB 缓冲池大小的值大约为 1.6GB。

### 对于具有更大内存(> 32GB)的系统

对于 RAM 大小大于 32GB 的系统，我们将返回到按照系统 RAM 大小的 5%来计算操作系统需求，以及 MySQL 其他需求的相同数量。因此，对于一个 RAM 大小为 192GB 的系统，我们的方法对于 InnoDB 缓冲池大小来说将达到大约 165GB，这也是要使用的最佳值。

## InnoDB 缓冲池大小计算器

计算任意大小 RAM 的最佳值:[在原始帖子](https://scalegrid.io/blog/calculating-innodb-buffer-pool-size-for-your-mysql-server/)上尝试计算器

## 不同 RAM 大小的 InnoDB 缓冲池大小图

[![chart](img/305279ff9ccf3d5b6a88d56c48a03413.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--shy1MRso--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://scalegrid.io/blog/wp-content/uploads/2018/01/chart.png)

## InnoDB 缓冲池大小计算的注意事项

这篇博文中考虑的是专用于 MySQL 的 Linux 系统。对于 Windows 系统或运行多个应用程序以及 MySQL 的系统，这些观察可能是不准确的。同样重要的是要注意，尽管我们可以使用这些工具作为参考，但要获得适合 innodb_buffer_pool_size 的正确大小，确实需要良好的经验、实验、持续的监控和微调。