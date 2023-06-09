# 不要被 PostgreSQL 区分大小写所困扰

> 原文：<https://dev.to/lefebvre/dont-get-bit-by-postgresql-case-sensitivity--457i>

我自己以前也遇到过 PostgreSQL 区分大小写的问题，有很多人问我这个问题，所以我想在这里提出来可能是件好事。

有时您会听说 PostgreSQL 不区分大小写，但事实并非如此。默认情况下，它实际上是将表名和列名转换成小写。所以看一下这个 SQL:

```
SELECT FullName FROM Person 
```

Enter fullscreen mode Exit fullscreen mode

PostgreSQL 实际上将其转换为:

```
SELECT fullname FROM person 
```

Enter fullscreen mode Exit fullscreen mode

如果您碰巧喜欢混合使用大小写来编写查询，那就太好了。

但是，如果您实际上已经使用区分大小写的名称创建了表和列，就会遇到问题，这种情况会在名称两边使用引号时发生。例如，考虑这些 SQL CREATE 语句:

```
CREATE TABLE person (fullname VARCHAR(100), address VARCHAR(100))
CREATE TABLE Person (FullName VARCHAR(100), Address VARCHAR(100))
CREATE TABLE "Person" ("FullName" VARCHAR(100), "Address" VARCHAR(100)) 
```

Enter fullscreen mode Exit fullscreen mode

在前两个例子中，您得到一个名为“person”的表，其中有两列，分别是“fullname”和“address”。这在第二个例子中可能不明显，因为名称不是小写的，但是请记住 PostgreSQL 会将您的 SQL 转换为小写。

在最后一个示例中，名称用引号括起来，因此它们的大小写保持不变。这意味着您将得到一个名为“Person”的表，其中有两列，分别是“FullName”和“Address”。现在，如果您尝试使用名为“Person”的表运行查询，会发生什么情况呢？嗯，像这样使用 SQL:

```
SELECT FullName FROM Person 
```

Enter fullscreen mode Exit fullscreen mode

您将得到一个语法错误:

错误:关系“人员不存在
行 1:从人员中选择全名

这是因为 PostgreSQL 正在将“person”转换为“person”，但是没有名为“Person”的表。其实叫“人”。

[![](img/3249c87cc9125d6e069532a91d8e4dd7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eDPP8Kpx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.xojo.com/wp-content/uploads/2016/09/2016-09-23_09-38-33.png)

为了避免这种错误，您必须编写带有引号的 SQL，如下所示:

```
SELECT "FullName" FROM "Person" 
```

Enter fullscreen mode Exit fullscreen mode

显然，这可能会开始变得有点痛苦，所以这个故事的寓意是，在创建表或编写 SQL 查询时不要使用引号，这样所有内容都会以小写形式创建，并且事情会像您可能期望的那样工作。您将特别希望关注用于创建 SQL 的任何工具。如果您使用一个工具的 UI 来创建一个表，并且有混合大小写的习惯，那么该工具可能会使用引号为您生成 SQL(或者甚至是表本身),正如您所看到的，这可能会在以后给你带来麻烦。

这篇文章最初出现在 Xojo 的博客上。