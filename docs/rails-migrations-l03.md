# Rails 迁移

> 原文：<https://dev.to/jeancarlosn/rails-migrations-l03>

Rails 框架非常有趣，因为在程序员和数据库之间放置了一层，你的名字是 ActiveRecord，
非常好的 ruby 库，用于操作所有数据库的数据、表结构和数据库操作对象，例如:MySql、Postgresql、
SQLite、Oracle、Sybase、SQL-Server 等。

对我来说，最有趣的是迁移，因为我们可以控制数据库的版本，这对大型项目来说非常重要。

在控制台上，我们可以:

简单示例:

内嵌`rails generate migration Person`。

这个例子很简单，但是很好，通过这个例子我们可以立即创建在数据库上创建表的脚本:

内嵌`rails generate migration Person name:string address:string`。

ActiveRecord 是一种 ORM ->Object-Rol 建模，具有更高的安全性。