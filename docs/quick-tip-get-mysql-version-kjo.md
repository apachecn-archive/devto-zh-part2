# 快速提示:获取 MySQL 版本

> 原文：<https://dev.to/lefebvre/quick-tip-get-mysql-version-kjo>

前几天，我需要获得一个 [PostgreSQL](https://dev.to/lefebvre/quick-tip-get-postgresql-version-j9a) 服务器的版本，我想我也应该看看如何用 MySQL 做这件事，因为我也不时地使用那个数据库。

事实证明，您可以通过快速数据库查询轻松获得这些信息，类似于用于 PostgreSQL 的查询:

```
SELECT VERSION(); 
```

Enter fullscreen mode Exit fullscreen mode

希望这能帮助到某个人。