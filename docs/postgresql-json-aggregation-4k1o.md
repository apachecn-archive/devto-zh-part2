# PostgreSQL: JSON 聚合

> 原文：<https://dev.to/moser/postgresql-json-aggregation-4k1o>

在客户项目中，我必须在表格结构中进行搜索，并返回包括一些子对象的结果。N+1 查询问题的经典实例，如果我使用 ORM 的话。我决定不对这个特性使用 ORM，因为它将是应用程序中最热门的路径之一，而且我希望对查询有更多的控制(相当复杂的逻辑和`LIKE` &排序)。但是过滤/排序不是今天的主题，所以我将在示例中省略它。

为了说明，让我们假设下面的模式:

```
CREATE TABLE parents (
  parent_id INTEGER PRIMARY KEY,
  name VARCHAR
);

CREATE TABLE children (
  child_id INTEGER PRIMARY KEY,
  name VARCHAR,
  birthdate DATE,
  parent_id INTEGER,
  FOREIGN KEY (parent_id) REFERENCES parents
); 
```

没有任何进一步的思考，我会做一个类似这样的查询和一些代码，用它们各自的子对象构造父对象。

```
SELECT * FROM parents JOIN children USING (parent_id) ORDER BY parent_id; 
```

```
parent_id | name | child_id | name | birthdate  
-----------+-------+----------+---------+------------
         1 | Jim | 1 | Tamara | 2017-02-01
         1 | Jim | 3 | Tom | 2005-10-01
         1 | Jim | 5 | Tonja | 2011-07-17
         2 | Jenny | 2 | Tim | 2000-11-02
         2 | Jenny | 4 | Theresa | 2017-04-30 
```

就在我开始编写将结果分离并放入其对象结构的逻辑之前，我想，“让数据库将一个父对象的所有子对象放入一个数组中会很好。”

我已经知道 [`array_agg`](https://www.postgresql.org/docs/9.6/static/functions-aggregate.html) 把所有的值聚集成一个数组。读了一些之后，我发现了 [`json_build_object`](https://www.postgresql.org/docs/9.6/static/functions-json.html) ，它接受一系列的键和值，并从中创建一个 JSON 对象。

所以我最后的查询看起来是这样的:

```
SELECT
    parent_id
  , p.name
  , array_agg(json_build_object(
      'child_id', c.child_id
    , 'name', c.name
    , 'birthdate', c.birthdate
  )) as children
FROM parents p
JOIN children c USING (parent_id)
GROUP BY 1, 2; 
```

```
parent_id | name | children                                                                                                              
-----------+-------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
         1 | Jim | {"{\"child_id\" : 1, \"name\" : \"Tamara\", \"birthdate\" : \"2017-02-01\"}","{\"child_id\" : 3, \"name\" : \"Tom\", \"birthdate\" : \"2005-10-01\"}","{\"child_id\" : 5, \"name\" : \"Tonja\", \"birthdate\" : \"2011-07-17\"}"}
         2 | Jenny | {"{\"child_id\" : 2, \"name\" : \"Tim\", \"birthdate\" : \"2000-11-02\"}","{\"child_id\" : 4, \"name\" : \"Theresa\", \"birthdate\" : \"2017-04-30\"}"} 
```

当使用 sqlalchemy 执行查询时，结果行中的`children`已经被正确地输入为字典列表。

两个查询的`explain analyze`输出(在一个非常小的测试集上)显示聚合版本慢了 50-100 %( 150-200μs 对 250-350μs ),但是我想这很少会是一个真正的问题，因为——至少在我的例子中

*   我的查询的执行时间主要取决于对父行的过滤/排序。

如果您想自己玩这个例子，[在这里获取 SQL 文件](https://moserei.de/files/postgresql-json.sql)。