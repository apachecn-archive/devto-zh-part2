# 在 PostgreSQL 中选择 DISTINCT ON

> 原文：<https://dev.to/bradymholt/select-distinct-on-in-postgresql-324b>

PostgreSQL 有一个非常有趣和强大的构造，叫做`SELECT DISTINCT ON`。不，这不是典型的`DISTINCT`。这不一样。当您有多组相似的数据，并且希望根据特定的顺序从每组中提取一条记录时，这种方法是最理想的。

让我们以一些日志数据为例。您有一个存储请求的`url`和`request_duration`的日志表，T1 是处理该 URL 请求所用的时间。它还包含一个`timestamp`列。如果您想回答“每个唯一 URL 的最近持续时间是多少？”您可能会得到类似下面这样的查询:

```
SELECT l.url, l.request_duration
FROM log l
INNER JOIN (
  SELECT url, MAX(timestamp) as max_timestamp
  FROM log
  GROUP BY url
) last_by_url ON l.url = last_by_url.url AND l.timestamp = last_by_url.max_timestamp; 
```

Enter fullscreen mode Exit fullscreen mode

带有子查询的`INNER JOIN`用于确定每个 URL 的最后时间戳。然后，外部查询从日志表中提取数据，并使用该子查询的结果将结果限制为 URL 的最后请求。有几种方法可以做到这一点，包括使用一个`WHERE IN`子句(假设只有一个标识符可以使用)、一个`LATERAL`连接或一个`WINDOW`函数。这些方法是可行的，但是它们都需要某种类型的两步查询，其中第一步是识别目标行，第二步是实际提取目标行。这不是很复杂的 SQL，但是可能会有点麻烦。

让我们考虑一下`DISTINCT`条款。当您使用一个`SELECT DISTINCT`子句时，您将丢弃重复的行，只保留一行。但是，保留的那个和其余的是一样的。如果您可以告诉`DISTINCT`只考虑*的一些*字段进行区分，然后从这组非常相似但略有不同的行中选择哪一行，会怎么样？这就是`SELECT DISTINCT ON`的作用。

**使用`DISTINCT ON`，您告诉 PostgreSQL 为由`ON`子句定义的每个不同的组返回一行。*用`ORDER BY`子句指定返回该组中的哪个*行。**

回到我们的日志示例。为了完成我们在上面用`SELECT DISTINCT ON`做的事情，它看起来像这样:

```
SELECT DISTINCT ON (url) url, request_duration
FROM logs
ORDER BY timestamp DESC 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！我们告诉 PostgreSQL“按照 url ( `ON (url)`)将日志放入不同的组中，按照最近的(`ORDER BY timestamp DESC`)对每个组进行排序，然后返回每个组中第一条记录的字段(`url, request_duration`)。