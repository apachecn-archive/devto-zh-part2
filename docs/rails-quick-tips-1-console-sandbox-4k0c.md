# Rails 快速提示#1:控制台沙箱

> 原文：<https://dev.to/citizen428/rails-quick-tips-1-console-sandbox-4k0c>

这是我将在未来几天/几周内发布的一系列 Ruby on Rails 相关快速技巧中的第一篇。

如果让我说出大多数 Rails 开发人员工具箱中使用最少的工具，我会选择`rails console --sandbox`。以下是[文档](http://guides.rubyonrails.org/command_line.html#rails-console)对此的评论:

> 如果您希望在不改变任何数据的情况下测试一些代码，可以通过调用`rails console --sandbox`来实现。

下面是一个沙盒控制台会话示例:

```
→ rails c --sandbox
Loading development environment in sandbox (Rails 5.2.0)
Any modifications you make will be rolled back on exit
[1] (rails_new) main: 0> User.count
   (17.7ms)  SELECT COUNT(*) FROM "users"
=> 1
[2] (rails_new) main: 0> User.destroy_all
  User Load (0.4ms)  SELECT "users".* FROM "users"
   (1.5ms)  SAVEPOINT active_record_1
  User Destroy (7.4ms)  DELETE FROM "users" WHERE "users"."id" = $1  [["id", 1]]
   (0.7ms)  RELEASE SAVEPOINT active_record_1
=> [#]
[3] (rails_new) main: 0> User.count
   (0.3ms)  SELECT COUNT(*) FROM "users"
=> 0
[4] (rails_new) main: 0>
   (0.8ms)  ROLLBACK

```

正如上面可以看到的，在这个控制台会话中执行的最后一个 SQL 命令是 [`ROLLBACK`](https://www.postgresql.org/docs/9.4/static/sql-rollback.html) ，所以我们让一切保持原来的样子。

今天就到这里，我将很快带来另一个与数据库相关的 Rails 快速技巧。