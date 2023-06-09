# 只需使用一个命令就可以将 PostgreSQL 数据库复制到另一台服务器上。

> 原文：<https://dev.to/fullstapps/replicate-your-postgresql-database-into-another-server-using-just-one-command-48km>

有时，我们需要将生产 postgreSQL 数据库复制到开发/暂存数据库，以便用现有数据测试新特性。

常见的方法是将数据库转储到文件，然后使用 scp 将文件传输到新的服务器，并从该文件创建数据库。

但是，我们可以在不创建中间文件的情况下对其进行归档，只需使用一个命令、一个管道。

```
pg_dump -C -h remotehost -U remoteuser dbname | psql -h localhost -U localuser dbname 
```

Enter fullscreen mode Exit fullscreen mode

如果您的开发数据库已经包含数据，您应该首先删除它，以避免创建重复数据的错误。

```
psql -h localhost -U localuser dbname -c "drop schema public cascade; create schema public;" 
```

Enter fullscreen mode Exit fullscreen mode

我发现这个命令非常有用，因为它非常简单。

注意:
首先使用备份数据库或备份您当前的数据库来测试它。

> 如果你喜欢我的内容或者对你有帮助，你可以给我买一杯咖啡来激励我写更多的内容