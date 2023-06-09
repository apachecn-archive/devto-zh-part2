# 如何比较两个数据库，识别差异并生成必要的 SQL 来更新其中一个数据库的结构

> 原文：<https://dev.to/llagerlof/how-to-compare-two-databases-identify-the-diferences-and-generate-the-necessary-sql-to-update-the-structure-of-one-of-them-pp3>

**工具**:db diff(【https://github.com/DBDiff/DBDiff】T2

注意:它可以在 Linux 或 Windows 上工作

## 你需要什么

*   路径上的 **PHP** 、 **GIT** 和**作曲**
*   访问源数据库和目标数据库的凭据

## 快速启动

*   下载并进入 DBDiff 目录

```
$ git clone https://github.com/DBDiff/DBDiff.git
$ cd DBDiff
$ composer update 
```

Enter fullscreen mode Exit fullscreen mode

*   创建一个**。dbdiff 目录中的 dbdiff** 文件，其中包含 2 台服务器的连接设置。向“tablesToIgnore”添加在比较时应该忽略的表和视图的列表。

**。dbdiff** 文件:

```
server1:
    user: root
    password:
    port: 3306
    host: localhost
server2:
    user: AzureDiamond
    password: hunter2
    port: 3306
    host: 192.168.1.1
template: templates/db-migrate.tmpl
type: schema
include: up
nocomments: true
tablesToIgnore:
- view1
- view2
- table5 
```

Enter fullscreen mode Exit fullscreen mode

*   在 templates 目录下创建一个 **db-migrate.tmpl** 文件，内容如下:

```
-- UP

{{ $up }}

-- END UP 
```

Enter fullscreen mode Exit fullscreen mode

*   执行下面的命令来生成数据库差异文件 **migration.sql**

```
$ php dbdiff server1.yourdatabase:server2.yourdatabase 
```

Enter fullscreen mode Exit fullscreen mode

*   在 server2.yourdatabase 上使用您喜欢的数据库客户机运行 **migration.sql** 中的命令来升级您的数据库。我建议在运行命令之前先检查一下。