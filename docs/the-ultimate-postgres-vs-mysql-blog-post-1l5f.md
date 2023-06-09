# 终极 Postgres vs MySQL 博客文章

> 原文：<https://dev.to/dmfay/the-ultimate-postgres-vs-mysql-blog-post-1l5f>

我可能应该先说[我喜欢和 Postgres](https://github.com/dmfay/massive-js) 一起工作，并且可以幸福地死去而不再看到`mysql>`提示。这不是一个不带偏见的比较，但无论如何这些都不好玩。

场景:两个应用程序，使用 [Massive.js](https://github.com/dmfay/massive-js) 存储和检索数据。Massive 在设计上与 Postgres 紧密结合。专门化使它能够利用只存在于某些关系数据库中或者不存在于其他关系数据库中的特性，以一种比传统的对象关系映射器更轻便、更“JavaScripty”的方式来简化数据访问。这对于完成工作来说是很棒的，因为基础很简单，对于复杂的东西来说，无论如何都要编写 SQL....您编写 SQL，将它存储在一个中心位置供重用，API 使运行它变得简单。

如果您必须支持另一个 RDBMS，Massive 就没那么有用了。理想情况下，这是你预先知道的事情。总之:事情总会发生，有时你会发现自己不得不回答这样一个问题:“如果我们需要在 MySQL 上用轻量级但紧密耦合的数据层运行这些应用程序，会是什么样子？”

不好，这是显而易见的答案，但不那么明显的是*如何*不好。我知道 Postgres 做了一些 MySQL 没有做的事情，但我也知道 MySQL 有很多我从未尝试过的事情。所以当我开始研究这个的时候，我开始记笔记。这是我找到的所有东西。

## 模式布局

现在，我们基本上都已经摆脱了“无模式”未来的集体幻觉，可以说数据存储最重要的方面是如何在数据库中模拟信息。Postgres 和 MySQL 都是关系数据库，在严格定义的表中对记录进行分组。但是这个主题有很大的变化空间。

### 多个模式

首先:“模式”并不总是指同一件事。对 MySQL 来说，“模式”就是“数据库”的同义词。对于 Postgres,“模式”是数据库中的一个名称空间*,它允许您将表、视图和函数组合在一起，而不必将它们分成不同的数据库。*

MySQL 在这方面的简单性因其提供跨数据库查询而得到改善:

```
SELECT *
FROM db1.table1 t1
JOIN db2.table2 t2 ON t2.t1_id = t1.id; 
```

Enter fullscreen mode Exit fullscreen mode

使用 Postgres，您可以跨模式工作，但是如果您需要在不同的*数据库*中查询信息，这是一项工作...

### 外来数据包装器

外来数据包装器让 Postgres 几乎可以与任何将信息表示为离散记录的东西对话。您可以在 Postgres 数据库中创建一个“外部表”,并像任何其他表一样使用`SELECT`或`JOIN`——只是在幕后，它实际上是读取一个 CSV，与另一个 DBMS 对话，甚至查询一个 REST API。这是一个足够强大的特性，以至于 NoSQL 的忠实拥护者 MongoDB [偷偷在 Postgres 之上用外来的数据包装器](https://www.linkedin.com/pulse/mongodb-32-now-powered-postgresql-john-de-goes/)构建了他们的 BI 连接器。当 [Multicorn](http://multicorn.org/) 让你用 Python 写一个新的 FDW 时，你甚至不需要懂 C！

Oracle 和 SQL Server 都有一些注册外部数据源的功能，但是 Postgres 的产品是我所知道的最具扩展性的。MySQL 除了上面提到的跨数据库查询支持之外，什么都没有。

### 表继承

继承通常被认为是面向对象编程语言而不是数据库的属性，但 Postgres 在技术上是一个 *ORDBMS* 或对象关系数据库管理系统。因此，您可以拥有一个包含列`name`和`population`的表`cities`，以及一个继承了`cities`定义的表`capitals`，但是增加了一个`of_country`列，当然，只与首都城市相关。如果你从`cities`得到`SELECT`，你从`capitals`得到行——它们也是城市！你当然可以把大写字母排除在外。这是一个小众的特性，但是当你有正确的用例时，它真的会大放异彩。

MySQL，作为传统的 RDBMS，不做这个。

### 物化视图

物化视图类似于常规视图，只是指定查询的结果是物理存储的(“物化”)，并且必须显式刷新。这允许数据库开发人员在结果不需要实时时缓存较慢查询的结果。

Oracle 有物化视图，SQL Server 的索引视图也差不多，但是 MySQL 没有物化视图支持。

### 检查约束条件

约束通常确保不存储无效数据。最常见的约束是`NOT NULL`，它防止插入或更新没有不可空列值的记录。当对另一个表中的记录的引用无效时，Foreign key 约束也是如此。Check 约束是最灵活的，它允许验证任何可以放在`WHERE`子句中的谓词——例如，断言价格必须是正数，或者美国邮政编码必须是五位数。

根据 MySQL 文档:[`CHECK`子句被解析，但被所有存储引擎忽略。](https://dev.mysql.com/doc/refman/5.7/en/create-table.html)

### JSONB 和索引

Postgres 和 MySQL 都有一个`JSON`列类型(MySQL 的替代 MariaDB 也有，但它目前只是`LONGTEXT`的别名)和用于构建、处理和查询 JSON 字段的函数。Postgres 实际上走得更远，它提供了一种将输入数据处理成二进制格式的`JSONB`类型。这意味着写起来会慢一点，但是查询起来会快很多。

这也意味着你可以索引二进制数据。GIN 或*广义倒排*索引允许查询检查特定键或键值对的存在，以避免扫描每一条记录进行匹配。如果您在`WHERE`子句中运行挖掘 JSON 字段的查询，这将是巨大的。

### 由函数定义的默认值

`DEFAULT`对于`CREATE TABLE`语句中的列来说是一个有用的规范。在最简单的层面上，如果`INSERT`语句没有给出一个明确的值，这可以用来将一个布尔字段作为`true`或`false`的基线。但是除了设置标量值之外，您还可以做更多的事情:时间戳可以默认为`now()`，UUID 可以默认为各种 UUID 生成函数中的任何一个，任何其他字段可以默认为您想写的任何函数返回的值——没有限制！

除非您使用 MySQL，在这种情况下，您在`DEFAULT`子句中唯一可以引用的函数是`now()`。

## 类型差异

不过，布局只是故事的一部分。同样重要的是类型支持的不同。健壮类型系统的好处是使数据库架构师能够尽可能准确地表示信息。如果一个值很难或者不可能用内置类型来表示，那么开发人员就很难依次处理，如果必须做出妥协来裁剪数据，那么它们会影响整个应用程序。有些类型甚至会影响整个数据库的设计，比如数组和枚举。一般来说，选择越多越好。

### uuid

波斯特格雷属于 UUID 类型。MySQL 没有。如果您想在 MySQL 中存储一个 UUID，如果您想让值像 uuid 一样可读，您可以选择 CHAR 如果您想让值更快但更难手动处理，您可以选择 BINARY。Postgres 还生成更多类型的 UUIDs。

### 布尔

布尔似乎是一个非常基本的类型！然而，MySQL 的 boolean 实际上是 TINYINT(1)的别名。这就是为什么查询结果显示 0 或 1 而不是`true`或`false`。这也是为什么您可以将一个表面上为布尔型的字段的值设置为 2。试试看！

Postgres:有适当的布尔值。

### 瓦丽娜和身长

然而，MySQL 并不是唯一一个以奇怪的方式混淆标准类型的。Postgres 中的 CHAR、VARCHAR 和 TEXT 类型都是相同结构的别名表示——唯一的区别是，如果指定了长度约束，就会强制执行长度约束。文档指出这实际上更慢，并建议将无界文本简单地定义为文本类型，而不是给定任意的最大长度。

这里发生的事情是，Postgres 使用一种叫做 *varlena* 或*可变长度数组*的数据结构来存储信息。varlena 的前四个字节存储值的长度，使得数据库很容易从存储中挑选出整个值。文本只是使用这种结构的类型之一，但它很容易成为最常见的类型。

如果一个 varlena 的长度超过了内联长度，数据库会使用一个名为 TOAST 的系统(“超大属性存储技术”)将它透明地卸载到扩展存储中。除非仔细设计和索引，否则带有涉及 TOASTable 字段的谓词的查询对于大型表来说可能不是那么高效，但是当数据库返回记录时，很容易跟踪 TOAST 指针，因此在大多数情况下开销几乎不明显。

就大多数人而言，所有这些的结果是这样的:使用 Postgres，您只需担心在有长度约束的*原因*的字段上建立长度约束。如果没有明确的要求来限制一个字段可以容纳多少信息，你就不必选择一个任意的数字，并试图使它与你的页面大小相匹配。

### 数组

记录中的非标量值！疯狂！狗和猫住在一起！任何使用过 JSON、XML、YAML 甚至 HTML 的人都明白，信息并不总是平面的。关系架构传统上要求将任何向量分解到新表中，更不用说更复杂的值了。有时这是有用的，但经常会增加复杂性，而没有真正的目的。内联数组使得许多任务——比如标记记录——变得更加容易。

Postgres 有数组，Oracle 也有；MySQL 和 SQL Server 没有。

### 定制类型

如果内置类型不够，您可以随时添加自己的类型。自定义类型允许您将值定义为您想要的值。域是一个相关的概念:对值施加约束的类型(自定义或内置)。例如，您可以创建一个域来将邮政编码表示为一个文本值，该文本值在一个`CHECK`子句中使用正则表达式来确保值由五个数字组成，可以选择后跟一个破折号和另外四个数字。

如果你用的是 Postgres 的话。Oracle 和 SQL Server 都提供了一些自定义类型的功能，但是 MySQL 没有。您甚至不能使用表级别的`CHECK`约束，因为引擎会忽略它们。

### 枚举

枚举得不到足够的爱。如果我看到的代表一组固定潜在值的每个 INT -或者更糟，VARCHAR -字段都有一美元，我可能仍然不能退休，但我至少可以度过一个美好的夜晚。当然，使用枚举也有缺点:添加新值需要 DDL，而且根本不能删除值。但是它们的适当用例仍然相当普遍。

MySQL 和 Postgres 都提供枚举。关键的区别在于 Postgres 的枚举是适当的可重用类型。MySQL 的枚举更像是被忽略的`CHECK`约束，为单个表中的单个列指定一个有效值列表。允许布尔列包含-100 的可能改进？

## 查询数据

这就是数据建模。还有整整另一半要做:实际处理存储的信息。SQL 本身分为两部分，定义数据库结构的“数据定义语言”和“数据操作语言”。后者包括`SELECT`、`INSERT`以及大多数人听到“SQL”这个名字时想到的其他语句。就像建模一样，Postgres 和 MySQL 在查询方面有很大的不同。

### 返回

自动生成主键消除了存储数据的麻烦。但是有一个问题:当您向表中插入一条新记录时，您不知道它的主键值被设置为什么。如果你调用一个特殊的函数，大多数关系数据库会告诉你最后一个自动生成的键是什么；有些，如 SQL Server，甚至允许您筛选出您感兴趣的单个表。

Postgres 超越了`RETURNING`子句。任何写语句- `INSERT`、`UPDATE`、`DELETE` -都可以以一个`RETURNING [column-list]`结尾，它在受影响的记录上充当一个`SELECT`。给你你刚才所做的全部记录集，或者你可以将你感兴趣的限制在某些列。

这意味着你可以这样做:

```
INSERT INTO foos (name)
VALUES ('alpha'), ('beta')
RETURNING *;

 id │ name  
────┼───────
  1 │ alpha
  2 │ beta
(2 rows) 
```

Enter fullscreen mode Exit fullscreen mode

使用 MySQL，在添加新记录后，您不得不调用`LAST_INSERT_ID()`。如果您添加了多个，那么`LAST_INSERT_ID`只会给出最早的新 id，剩下的由您自己解决。当然，这只对整数主键有好处。

MySQL 也没有针对`UPDATE` s 和`DELETE` s 的对应功能。竞争对手 MariaDB 在`DELETE`上支持`RETURNING`，但不支持任何其他类型的声明。

### 常用表表达式

公共表表达式(cte)允许将复杂的查询分解，并从独立的部分组装起来。你可以这样写:

```
WITH page_visits AS (
  SELECT p.id, p.site_id, p.title, COUNT(*) AS visits
  FROM pages AS p
  JOIN page_visitors AS v ON v.page_id = p.id
  GROUP BY p.id, p.site_id, p.title
), max_visits AS (
  SELECT DISTINCT ON (site_id)
    site_id, title, visits
  FROM page_visits
  ORDER BY site_id, visits DESC
)
SELECT s.id, s.name,
  max_visits.title AS most_popular_page,
  SUM(page_visits.visits) AS total_visits
FROM sites AS s
JOIN page_visits ON page_visits.site_id = s.id
JOIN max_visits ON max_visits.site_id = s.id
GROUP BY s.id, s.name, max_visits.title
ORDER BY total_visits DESC; 
```

Enter fullscreen mode Exit fullscreen mode

在第一个查询中，我们汇总了访问次数；在第二个中，我们在第一个的结果上使用 [`DISTINCT ON`](https://www.postgresql.org/docs/10/static/sql-select.html#SQL-DISTINCT) 来过滤掉除了最受欢迎的页面之外的所有页面；最后，我们将两个中间结果连接起来，以提供我们想要的输出。cte 是一种真正可读的提取查询逻辑的方式，它让您可以在一条语句中完成一些用其他方式无法完成的事情。

MySQL 确实有 cte！然而:由于有了`RETURNING`子句，Postgres 可以*在 CTE* 中写记录，并对结果进行操作。这对于应用程序逻辑来说是巨大的 T4。下一个查询在 CTE 中写入一条记录，然后在连接表中添加一个对应的条目——所有这些都在同一个事务中完成。

```
WITH wine AS (
  INSERT INTO wines (name, year)
  VALUES ('Herrenreben', 2015)
  RETURNING id
), reviewer AS (
  SELECT id
  FROM reviewers
  WHERE name = 'Wine Enthusiast'
)
INSERT INTO wine_ratings (wine_id, reviewer_id, score)
SELECT wine.id, reviewer.id, 92
FROM wine
JOIN reviewer ON TRUE; 
```

Enter fullscreen mode Exit fullscreen mode

### 铸造

有时查询需要将一个值视为不同的类型，无论是存储它还是以某种方式对它进行操作。Postgres 甚至允许您用`CREATE CAST`定义类型之间的额外转换。

MySQL 支持将值转换为二进制、char/nchar、date/datetime/time、decimal、JSON 以及有符号和无符号整数。这个列表中没有 tinyints，因为 boolean 实际上是 tinyints，这意味着当您需要将一个值强制为 true 或 false 以存储在“boolean”列中时，您会受到条件约束。

### 横向连接

横向连接从根本上类似于相关子查询，因为它针对当前结果集的每一行执行。然而，相关子查询仅限于为一个`SELECT`列表或`WHERE`子句返回一个值；`FROM`子句中的子查询是独立运行的。横向连接可以引用剩余结果集中的信息:

```
CREATE TABLE docs (id serial, body jsonb);

INSERT INTO docs (body) VALUES ('{"a": "one", "b": "two"}'), ('{"c": "three"}');

SELECT docs.id, keys.*
FROM docs
JOIN LATERAL jsonb_each(docs.body) AS keys ON TRUE;

 id │ key │ value  
────┼─────┼─────────
  1 │ a   │ "one"
  1 │ b   │ "two"
  2 │ c   │ "three"
(3 rows) 
```

Enter fullscreen mode Exit fullscreen mode

它还可以调用类似于`unnest`的表函数，返回多行和多列:

```
CREATE TABLE multiple_arrays(arr1 int[], arr2 int[]);

INSERT INTO multiple_arrays (arr1, arr2)
VALUES
    ('{1,2,3}', '{4,5}'),
    ('{6,7}', '{8,9,10}');

SELECT raw.*
FROM multiple_arrays
JOIN LATERAL unnest(arr1, arr2) AS raw ON TRUE;

 unnest │ unnest 
────────┼────────
      1 │ 4
      2 │ 5
      3 │ (null)
      6 │ 8
      7 │ 9
 (null) │ 10
(6 rows) 
```

Enter fullscreen mode Exit fullscreen mode

Oracle 和 SQL Server 提供了类似的功能，在前者中使用了`LATERAL`关键字，在后者中使用了`CROSS APPLY` / `OUTER APPLY`。MySQL 没有。

### 可变函数自变量

函数！程序，如果你相信这种区别的话！他们很棒！您可以声明可变参数(在其他语言中称为“varargs”或“rest parameters ”),将任意数量的参数放入以最终参数命名的单个集合中。

在波斯特格雷。

### 谓词操作

一些有用的操作允许用 Postgres 表达更多的`WHERE`子句:

*   `IS DISTINCT FROM`和它的对应物`IS NOT DISTINCT FROM`提供了一个空敏感的相等测试。Null 通常是不可比较的，因为它表示一个值的*缺失*，所以谓词`WHERE field <> 1`不会返回`field`为 null 的记录。`WHERE field IS DISTINCT FROM 1`返回所有`field`不为-1 的记录，包括为空的记录。
*   `ILIKE`是不区分大小写的`LIKE`操作。MySQL 确实具有不区分大小写的模式匹配能力，但是它取决于您的排序规则，并且不能在每个查询的基础上进行切换(为了完全公平起见，默认排序规则是不区分大小写的)。
*   `~`、`~*`、`!~`和`!~*`组成了一组 POSIX 正则表达式测试:分别是匹配、不区分大小写匹配、不匹配和不区分大小写匹配。MySQL 确实有`REGEXP`和`NOT REGEXP`；但是，Postgres 的实现有 lookahead 和 look ahead。

## 通用数据库工作

这就是我发现的架构和查询语言特性的差距。不过，我确实遇到了其他一些值得一提的事情:

### 依赖关系

MySQL 不关心数据库对象之间的依赖关系。您可以告诉它删除一个视图或过程所依赖的表，它会直接删除它。直到下一次尝试调用视图或过程时，您才会意识到哪里出错了。Postgres 把你从自己手中拯救出来，除非你真的很确定并且用`CASCADE`把你的依赖者也丢下。

### 触发器和表写

仅仅提到触发因素就可能让一些人不想吃午饭。他们没有那么坏，诚实(嗯，他们可能是，但这不像是他们的错)。无论如何，要点是:有时你想写一个触发器，修改表中的其他行。

嗯，你不能在 MySQL。

## 结束？

这可能已经让我筋疲力尽了，但我很确定这还不是 Postgres 和 MySQL 之间的功能差距的详尽列表。我确实一开始就承认了自己的偏好，但花了六周时间来转换这种比较是相当糟糕的。我认为仍然有选择 MySQL 的理由——但我不确定它们是否是技术性的。