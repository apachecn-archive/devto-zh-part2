# 快速提示:获取 PostgreSQL 版本

> 原文：<https://dev.to/lefebvre/quick-tip-get-postgresql-version-j9a>

前几天我碰到了这个，觉得它可能值得一个快速提示。我需要弄清楚服务器上运行的是哪个版本的 PostgreSQL。事实证明，您可以通过快速数据库查询轻松获得这些信息:

```
SELECT version(); 
```

Enter fullscreen mode Exit fullscreen mode

希望这能帮助到某个人。