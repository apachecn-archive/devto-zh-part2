# 红移物化视图

> 原文：<https://dev.to/brightdevs/redshift-materialised-views-3487>

在规范化模式上创建一个视图来连接和聚集数据通常很方便，尤其是当它需要复杂的查询时。

[![publish](../Images/2d832b15f84179dc2e5a84f9c39ac685.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ttvLpkTq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0x7tq927poqp5mwfr0w2.jpg)

在 PostgreSQL 中，您可以基于查询创建视图。

```
CREATE VIEW my_view AS SELECT
                         list_id,
                         event_id,
                         sum(price_paid) AS revenue,
                         count(qty_sold) AS qty
                       FROM sales
                         WHERE local_date >= '2018-01-01'
                       GROUP BY list_id, event_id; 
```

Enter fullscreen mode Exit fullscreen mode

每次从这样的视图中选择数据时，都会执行下面的查询。如果执行查询需要很长时间，可能会使用实例化视图。

```
CREATE MATERIALIZED VIEW my_view AS SELECT (...) ; 
```

Enter fullscreen mode Exit fullscreen mode

这个视图是在创建时用数据填充的，因此不需要每次访问数据时都运行耗时的查询。但是，每次数据发生变化，都需要用`REFRESH MATERIALIZED VIEW my_view`查询手动刷新视图。当您的数据很少发生可预见的变化时，物化视图特别有用。一个完美的用例是 ETL 过程——刷新查询可能作为它的一部分运行。

因为 Redshift 是基于 PostgreSQL 的，所以人们可能会认为 Redshift 有物化视图。遗憾的是，Redshift 并没有实现这个特性。红移中的常规视图有两个主要缺点:

*   红移查询规划器不通过视图进行优化；因此，从视图中获取数据而不是直接运行查询实际上可能会更慢，
*   Redshift 中的视图是连接到表的(不仅仅是它的名字)，所以在更改表时会遇到错误；使用`WITH NO SCHEMA BINDING`子句告诉 Redshift 不要绑定到底层数据库对象。

我们可以不使用视图，而是基于查询创建一个表(每次删除并重新创建它)。因为它是一个常规表，所以可以定义排序键来进一步提高性能。

数据刷新的便利性可能被认为是物化视图的一个优点。为了实现与表类似的行为，我们可以使用常规视图来实际存储查询。这种解决方案的缺点是，通过视图向表中插入数据要比使用查询花费更长的时间。通过视图重新创建一个包含数据的表可能非常简单，只需将下面两条语句打包到一个事务块中:

```
BEGIN TRANSACTION;
DROP TABLE my_view_table;
CREATE TABLE my_view_table AS SELECT * FROM my_view;
COMMIT TRANSACTION; 
```

Enter fullscreen mode Exit fullscreen mode

这段代码有两个主要问题:

*   事务中的`DROP`命令在表上放置了一个`LOCK`，因此当试图访问数据时，其他进程将需要等待；此外，如果您提交事务，等待的流程将收到类似`table 1234556 dropped by concurrent transaction`的错误，
*   添加到表中的任何排序关键字都将丢失。

从表中删除所有数据，虽然看起来很容易实现，但需要使用可能很长的`VACUUM`和`ANALYZE`。不合格的`DELETE`更快的替代物是`TRUNCATE`。但是，它会提交运行它的事务，并且不能回滚。另一种方法是使用`CREATE TABLE ... LIKE`语句创建一个中间表。该语句复制列名、数据类型和`NOT NULL`约束。用`LIKE`选项创建的表也继承分布样式和排序键(但不继承主键和外键约束)。

```
BEGIN TRANSACTION;
CREATE TABLE my_view_table_new LIKE my_view_table;
INSERT INTO my_view_table_new SELECT * FROM my_view;
ALTER TABLE RENAME my_view_table TO my_view_table_old;
ALTER TABLE RENAME my_view_table_new TO my_view_table;
DROP TABLE my_view_table_old;
COMMIT;
END TRANSACTION; 
```

Enter fullscreen mode Exit fullscreen mode

要查看用于创建视图的查询代码，您可以使用`psql`登录数据库并运行`\d+ my_view`。

Redshift 不实现物化视图，但是模拟类似的行为非常简单。唯一要问的问题是，我们是否需要更简单或更快速的数据刷新。

最初发布于 [brightinventions.pl](https://brightinventions.pl/blog/)

软件工程师@光明发明
[电子邮件](//agnieszka.olszewska@brightinventions.pl)