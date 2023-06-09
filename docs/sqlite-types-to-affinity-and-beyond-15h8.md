# SQLite 类型:亲和与超越

> 原文：<https://dev.to/hoelzro/sqlite-types-to-affinity-and-beyond-15h8>

*最初发布于[https://hoelz.ro/blog/sqlite-types-to-affinity-and-beyond](https://hoelz.ro/blog/sqlite-types-to-affinity-and-beyond)T3】*

如果你还没有查看过 [SQLite](http://sqlite.org/) ，它绝对值得一看。当您进行开发时，它非常适合零配置设置，并且它是运行测试套件的绝佳数据库，尤其是因为您可以完全在易失性内存之外运行 SQLite 数据库。它甚至是中小型数据集的一个很好的选择——我有很多项目将他们的数据存储在 SQLite 中以保持简单，甚至是 SQLite 本身使用的版本控制系统 [Fossil](http://fossil-scm.org) 也将其数据存储在 SQLite 中！

SQLite 与 MySQL 或 PostgreSQL 等其他 SQL 实现的不同之处之一(除了它是无服务器的这一事实)是它的类型系统的工作方式。SQLite 的类型系统类似于 Perl 的类型系统，因为一个列可以保存任何类型的值。所以即使有下面的表定义:

```
CREATE TABLE number_values (
  value INTEGER NOT NULL
); 
```

Enter fullscreen mode Exit fullscreen mode

我可以运行`INSERT INTO number_values VALUES ('foo')`，SQLite 会很高兴地将‘foo’存储到 value 列中。当您进行比较时，SQLite 在如何对待类型方面非常聪明；如果运行以下语句:

```
SELECT COUNT(1) FROM number_values WHERE value = '5' 
```

Enter fullscreen mode Exit fullscreen mode

SQLite 会意识到你在一个整数列中寻找一个字符串，并自动将' 5 '转换成 5。这使用了一组规则，SQLite 文档称之为 *[类型关联](http://www.sqlite.org/datatype3.html)* 。

这一切都很好，但是我在从事一个兼职项目时，在类型强制系统中发现了一个有趣的怪癖。SQLite 允许你添加自定义函数，比如:

```
$dbh->sqlite_create_function('is_even', 1, sub {
  my ( $n ) = @_;

  return $n % 2 == 0 ? 1 : 0;
}); 
```

Enter fullscreen mode Exit fullscreen mode

定义了`is_even`函数后，我可以在 SQL 语句中使用它:

```
SELECT COUNT(1) FROM number_values WHERE is_even(value) = 1 
```

Enter fullscreen mode Exit fullscreen mode

...SQLite 会告诉我在“number_values”表中有多少个偶数。考虑到类型关联，您认为下面的表达式会产生什么结果？

```
SELECT COUNT(1) FROM number_values WHERE is_even(value) = '1' 
```

Enter fullscreen mode Exit fullscreen mode

您可能期望与前面的 SQL 语句产生的数字相同，但不幸的是，这是不正确的。正确答案是“0”。出于某种原因，在将函数的结果与一个值进行比较时，不会发生隐式强制。因此，如果您决定使用 SQLite 并添加自己的函数，请记住这一点！