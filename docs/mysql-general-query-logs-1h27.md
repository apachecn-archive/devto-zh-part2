# MySQL 常规查询日志

> 原文：<https://dev.to/vivekshashank/mysql-general-query-logs-1h27>

这是第一次发表[在这里](https://thecuriousfrequency.wordpress.com/2015/11/25/mysql-general-query-logs/)。

MySQL 可以在通用查询日志中记录每个活动。当您想要查看正在执行哪些查询或者何时执行提交或回滚时，这在应用程序分析中很有帮助。在使用 ORM 作为日志显示原始 SQL 的应用程序中，它也很有帮助。

您可以通过在 MySQL 控制台中运行以下查询来启用 MySQL 通用查询日志:

```
SET GLOBAL general_log = 'ON'; 
```

Enter fullscreen mode Exit fullscreen mode

注意:你不需要重启 MySQL 服务器。

您可以使用以下命令检查日志文件的位置。

```
show variables like 'general_log_file'; 
```

Enter fullscreen mode Exit fullscreen mode

它会显示一个位置，比如:- /var/lib/mysql/filename.log

现在你可以分析你的日志来找到一些有用的信息。如果生成的日志很大，您可以考虑使用 percona 提供的 pt-query-digest([https://www . per ConA . com/doc/per ConA-toolkit/LATEST/pt-query-digest . html](https://www.percona.com/doc/percona-toolkit/LATEST/pt-query-digest.html))等工具进行分析。

您可以使用
禁用它

```
SET GLOBAL general_log = 'OFF'; 
```

Enter fullscreen mode Exit fullscreen mode

要了解更多信息和选项，你可以在这里阅读官方文档。
[https://dev.mysql.com/doc/refman/5.6/en/query-log.html](https://dev.mysql.com/doc/refman/5.6/en/query-log.html)