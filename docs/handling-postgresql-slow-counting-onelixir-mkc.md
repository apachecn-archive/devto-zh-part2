# 处理 PostgreSQL 慢计数依赖于药剂

> 原文：<https://dev.to/edipox/handling-postgresql-slow-counting-onelixir-mkc>

* * *

不久前，我在处理一个简单的 [JSON API](http://jsonapi.org/) 项目时发现了一个意外的问题，这个项目应该提供分页资源。

大多数 JSON API 友好的库执行记录计数，以便在每个响应上提供分页链接，特别是对于最后一个页面链接。但是在某些情况下，一个简单的计数查询可能会**非常**慢:

```
SELECT COUNT(*) FROM table; 
```

具体来说，在这种情况下，对超过 270 万条记录的表执行该查询需要 6 秒多的时间。

* * *

## 理解问题

由于 [PostgreSQL 的 MVCC 实现](http://momjian.us/main/writings/pgsql/mvcc.pdf)，该计数查询非常耗时。MVCC(多版本并发控制)是一种通常用于在多个进程访问相同数据时提供数据一致性的方法。这通常是通过拍摄数据快照来完成的，因此当并发事务处理相同的数据时，每个事务将根据事务时间读取给定数据库对象的“版本”。

为了计算表的行数，PostgreSQL 需要确保它们存在。但是考虑到每个记录的多个快照/版本，PostgreSQL 无法直接汇总整个表。因此 PostgreSQL 读取每一行，执行一个**顺序扫描。！**

## 这个怎么修？

有不同的方法可以解决这个问题，包括基于[触发器的机制](http://www.varlena.com/GeneralBits/120.php)。在我的例子中，使用估计的行数是可以接受的，幸运的是， [PostgreSQL 通过 **pg_class reltuples**](https://www.postgresql.org/docs/current/static/catalog-pg-class.html) 提供了这一点:

```
SELECT reltuples::BIGINT AS estimate FROM pg_class WHERE relname=<table_name> 
```

### 等待..什么是 reltuples？

*【是】表格中的行数。这只是计划者使用的估计。*-[PostgreSQL:Documentation:pg _ class](https://www.postgresql.org/docs/current/static/catalog-pg-class.html)

此估算的准确性取决于计划员更新 pg _ class reltuples 的频率:

它由 VACUUM、ANALYZE 和一些 DDL 命令(如 CREATE INDEX)更新。-[PostgreSQL:Documentation:pg _ class](https://www.postgresql.org/docs/current/static/catalog-pg-class.html)

您可能不经常执行 CREATE 和 INDEX，但是不要担心，ANALYZE 也会更新这个值。真空和分析由默认启用的[自动真空后台程序](https://www.postgresql.org/docs/current/static/routine-vacuuming.html#AUTOVACUUM)自动执行。

请注意，这个解决方案是基于**总计**估计记录计数的，因此它不适用于需要过滤查询结果的情况。

* * *

## 好了，我们来写点仙丹代码吧！

在我们的实现中，我们使用了 [scrivener_ecto](https://github.com/drewolson/scrivener_ecto) ，这是一个非常好的包，可以帮助您为您的 ecto 查询添加分页。

最近，我们在`scrivener_ecto`中添加了 [total_entries](https://github.com/drewolson/scrivener_ecto/pull/42) 选项，因此可以使用任何自定义逻辑来计算 total_entries 并修复类似情况。

这是基于给定的`conn`和页面参数处理分页的函数。我们用包含`total_entries`选项的配置结构将查询传递给`Scrivener.paginate`:

```
@spec handle_pagination(conn :: Plug.Conn.t(), page :: number | atom, page_size :: number | atom, query :: module) ::
        Plug.Conn.t()
defp handle_pagination(conn, page, page_size, query) do
  config = %Scrivener.Config{
    module: Repo,
    page_number: page,
    page_size: page_size,
    options: [total_entries: total_entries(query)]
  }

  query |> Scrivener.paginate(config)
end 
```

注意上一个函数对`total_entries`的调用。我们通过执行 SQL 代码直接检索`reltuples`得到估计值:

```
# Retrieves the estimated record count
@spec total_entries(query :: module) :: number
defp total_entries(query) do
  table_name = table_name(query)

  {:ok, result} =
    Ecto.Adapters.SQL.query(Repo, "SELECT reltuples::BIGINT AS estimate FROM pg_class WHERE relname=$1", [table_name])

  result.rows |> List.first() |> List.first()
end 
```

鉴于我们不知道要查询的表名 *pg_class' reltuples* ，我们在这个难题上缺少了一块。为了解决这个问题，我们从查询中读取了`__schema__(:source)`:

```
# Determines the table name based on a given query
@spec table_name(query :: module) :: module
defp table_name(query) do
  module_from_query(query).__schema__(:source)
end

# Retrieves the model module from a given query
@spec module_from_query(module :: module) :: module
defp module_from_query(%{from: {_table, module}}), do: module
defp module_from_query(module), do: module 
```

* * *

## 结论

在某些情况下，当有可能使用相对准确的值时，我们可以利用 PostgreSQL 的估计计数功能。再加上非常开放的 elixir 社区，它允许我们向现有的软件包添加新的选项，几乎毫不费力地从一个不可接受的问题转变为一个非常有效的终点:在我的计算机中用了不到 0.5 毫秒的时间就有了 50 万条记录。

非常欢迎建议和评论！