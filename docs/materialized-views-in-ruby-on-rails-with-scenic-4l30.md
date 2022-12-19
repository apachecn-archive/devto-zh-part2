# Ruby on Rails 中的实化视图

> 原文：<https://dev.to/mreyman1990/materialized-views-in-ruby-on-rails-with-scenic-4l30>

物化视图并没有在 Ruby on Rails 应用程序中广泛使用。然而，我们最近尝试使用它，结果非常令人满意。

在本文中，我们将展示一个使用 Ruby 2.4.1、Rails 5.1.4、PostgreSQL 10 和 scenic gem 的简单应用程序。

**什么是数据库视图？**

来自 PostgreSQL 文档:

假设您的应用程序对天气记录和城市位置的组合列表特别感兴趣，但是您不想在每次需要时都键入查询。您可以在查询上创建一个视图，该视图为查询提供一个名称，您可以像普通表一样引用该名称:

`CREATE VIEW myview AS
SELECT city, temp_lo, temp_hi, prcp, date, location
FROM weather, cities
WHERE city = name;`

`SELECT * FROM myview;
view rawcreate_view.sql`

自由使用视图是优秀 SQL 数据库设计的一个关键方面。视图允许您封装表结构的细节，这些细节可能会随着应用程序的发展而改变，在一致的接口后面。

视图几乎可以用在任何可以使用真实表格的地方。在其他视图上构建视图并不少见。

这是一个非常方便的方法，不用编写复杂的查询。这对性能没有帮助——复杂的查询仍然每次都要执行。

**物化视图**

幸运的是，视图可以被物化。同样，让我们来看看 PostgreSQL 文档:

PostgreSQL 中的物化视图像视图一样使用规则系统，但是以类似表格的形式保存结果。
之间的主要区别

`CREATE MATERIALIZED VIEW mymatview AS SELECT * FROM mytab;`

还有:

`CREATE TABLE mymatview AS SELECT * FROM mytab;`

物化视图随后不能被直接更新，并且用于创建物化视图的查询的存储方式与视图查询的存储方式完全相同，因此可以使用
为物化视图生成新数据

`REFRESH MATERIALIZED VIEW mymatview;`

简单地说，视图查询的结果存储在数据库中——就像任何其他表一样。唯一的区别是我们不能直接更新视图，但是可以使用源表中的记录来刷新视图。

这样，我们可以在物化视图中使用以更简单的方式组织的数据，而不是执行昂贵的查询。

[点击此处阅读完整的案例分析和示例。](https://ideamotive.co/blog/materialized-views-ruby-rails-scenic/)