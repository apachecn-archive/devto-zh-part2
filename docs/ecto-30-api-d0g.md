# Ecto 3.0 :计划改进查询 API 上一篇]

> 原文：<https://dev.to/gumi/ecto-30-api-d0g>

在 Ecto 3.0 发行之前，plataformatec 公司开发团队的博客文章《[a sneak peek at ecto 3.0:query improvements ( part1)](http://blog.plataformatec.com.br/2018/10/a-sneak-peek-at-ecto-3-0-query-improvements-part-1/)》已公开。 本稿根据 plataformatec 公司的许可，基于这篇文章，介绍 Ecto 3.0 最大的改善项目`Ecto.Query`API。 blog 报道预计下次会有 part 2，所以本文作为前篇。

# 通过命名绑定改善绑定的结构

Ecto 通过`join`，总是可以连接多个模式和表。

```
query =
  from p in Post,
    join: c in Comment,
    where: p.id == c.post_id,
    select: c 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们对查询进行修改，以便只返回已发布的评论。 上面的查询可以按以下方式配置:

```
from [_, c] in query, where: c.public 
```

Enter fullscreen mode Exit fullscreen mode

从上面的示例代码中可以看到，您可以提取查询(`p`和`c`)中的绑定，然后应用过滤器。 此示例中的绑定依赖于`in`左侧列表中的顺序，并带有位置。 暂时命名为`p`和`c`，与整个查询无关。 也就是说，即使改写如下，查询的含义也是相同的。

```
from [_, comment] in query, where: comment.public 
```

Enter fullscreen mode Exit fullscreen mode

位置绑定的问题在于，查询的配置可能会变得非常困难。 尝试构建复杂搜索的功能，通过合并多个表并重新排序来跟踪位置绑定，容易产生复杂和漏洞。

修改 Ecto 3.0，使其分别为`from`和`join`命名。 上述查询被改写如下。

```
query =
  from p in Post,
    join: c in Comment,
    as: :comments,
    where: p.id == c.post_id,
    select: c 
```

Enter fullscreen mode Exit fullscreen mode

请注意，在`join`之后增加了`ad`选项。 过滤现有的`:comments`时，无论在查询中的顺序如何，都可以写如下。

```
from [comments: c] in query, where: c.public 
```

Enter fullscreen mode Exit fullscreen mode

用关键字列表替换位置绑定。 键是绑定名称，值是要为其分配绑定的变量。 这里，变量`c`是暂定的，所以什么名字都可以。 重要的是绑定到现有的`:comments`。

Ecto 3.0 采用了`:as`选项作为明确的名称结构，而不是变量名。 因为变量名有时会不小心重复。 特别是，如果开发人员将查询的变量名省略为一个字符，这种可能性就会增加。 此外，如果多次尝试绑定到同一名称，则会发生错误。

另一个要列举的是，`:as`选项也会给予`from`。

```
query =
  from p in Post,
    as: :posts,
    join: c in Comment,
    as: :comments,
    where: p.id == c.post_id,
    select: c 
```

Enter fullscreen mode Exit fullscreen mode

通过命名绑定，Ecto 可以更加灵活地构建复杂的搜索表单和搜索 API 等动态查询。

# 数据库前缀和索引提示

在为了添加命名绑定而制作的 Ecto 的基础上，又增加了两个功能。 `from`/ `join`前缀和索引提示。

Ecto v2.0 采用了前缀这一想法。 前缀(前缀)的含义因数据库引擎而异。 在 Postgres 中，前缀是 Postgres 架构。 Postgres 数据库有多个模式，缺省模式称为“公共”。 由于 MySQL 不支持模式，因此前缀功能只是不同数据库的含义。

Ecto v2.0 引入前缀的目的是方便地从不同的前缀中选择、插入、更新或删除数据。 目标是支持多租户 APP 应用程序。 但是，在 Ecto v2.0 中，一次只能处理一个前缀。 例如，不能编写一个查询来在两个不同的前缀之间联接数据。

在 Ecto v3.0 中，此限制不存在。 在`from`/ `join`中，与`:as`选项一样，被赋予`prefix`选项。 例如，考虑一个系统，其中所有帖子都是公开的。 但是，假设注释是使用系统的客户端所特有的。 这意味着系统中每个客户端都有多个前缀，每个前缀都有各自的注释表。 然后，可以编写跨越这些前缀的以下查询。

```
from p in Post,
  prefix: "public",
  join: c in Comment,
  prefix: "client1",
  where: p.id == c.post_id,
  select: c 
```

Enter fullscreen mode Exit fullscreen mode

此外，在 Ecto 3.0 中，通过同样的 API，也可以使用[MySQL](https://dev.mysql.com/doc/refman/8.0/en/index-hints.html) 和[MSSQL](https://docs.microsoft.com/en-us/sql/t-sql/queries/hints-transact-sql-table?view=sql-server-2017) 数据库中的索引提示。

```
from p in Post,
  join: c in Comment,
  hints: ["USE INDEX FOO", "USE INDEX BAR"],
  where: p.id == c.post_id,
  select: c 
```

Enter fullscreen mode Exit fullscreen mode

可能不怎么用提示。 关于这些功能，请务必阅读数据库免责事项。

前缀和提示选项使开发人员能够更灵活地组织和优化查询，充分利用`Ecto.Query`的力量。 也可以避免回退到 SQL。

# 其他变更

`Ecto.Query`可以在`where`和`having`中使用元组。 例如，可以写`where: {p.foo, p.bar} > {^foo, ^bar}`这样的查询。 [可以用于基于光标的寻呼处理](https://www.sitepoint.com/paginating-real-time-data-cursor-based-pagination/)等吧。

还支持`+`、`-`、`*`、`/`等算术运算符。 但是，这些运算符只是委托给原来使用的数据库引擎。 因此，请务必检查数据库，以确定哪些类型可以作为被运算符。

最后，可以调用以表或整个源为参数的数据库函数。 `fragment("some_function(?)", p)`的用法。

在下一篇[后篇]中，将进一步介绍对 Ecto 3.0 的`Ecto.Query`进行的改善和功能。

### Journaling of Lian Lian

*   [Ecto 3.0 :关于打破互换性的变更预定](https://dev.to/gumi/ecto-30--523l)
*   [Ecto 3.0 :计划改善查询 API 后篇]](https://dev.to/gumi/ecto-30-api-3odk)
*   [Ecto 3.0 :性能和向新版本的转移等](https://dev.to/gumi/ecto-30--3ema)