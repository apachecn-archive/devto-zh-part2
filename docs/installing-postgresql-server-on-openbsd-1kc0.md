# OpenBSD 6.3 上的 PostgreSQL 10.3:安装

> 原文：<https://dev.to/nabbisen/installing-postgresql-server-on-openbsd-1kc0>

这篇文章展示了如何在 [OpenBSD](https://www.openbsd.org/) 上安装 [PostgreSQL](https://www.postgresql.org/) 并设置它。

<center>✿ ✿ ✿</center>

## 环境

*   操作系统:OpenBSD 6.3
*   数据库:PostgreSQL 10.3

<center>✿ ✿ ✿</center>

## 程序

* *注意*:在代码区，前导`#`表示由超级用户执行；相当于使用`doas`命令(作为 root)，而前面的`$`是指一般用户。

### 安装包

```
#  pkg_add postgresql-server 
```

Enter fullscreen mode Exit fullscreen mode

### 初始化数据库

```
#  # change user
#  su _postgresql
  $  # Both `--auth=md5` and `--pwprompt` are for the sake of security. 
$  initdb -D /var/postgresql/data/ -U postgres --auth=md5 --pwprompt --encoding=UTF-8 --locale=ja_JP.UTF-8
$  # or without locale: `initdb -D /var/postgresql/data/ -U postgres --auth=md5 --pwprompt --no-locale`
 The files belonging to this database system will be owned by user "_postgresql".
This user must also own the server process.

The database cluster will be initialized with locale "ja_JP.UTF-8".
initdb: could not find suitable text search configuration for locale "ja_JP.UTF-8"
The default text search configuration will be set to "simple".

Data page checksums are disabled.
postgresql
Enter new superuser password: 
Enter it again: 

creating directory /var/postgresql/data ... ok
creating subdirectories ... ok
selecting default max_connections ... 30
selecting default shared_buffers ... 128MB
selecting dynamic shared memory implementation ... posix
creating configuration files ... ok
running bootstrap script ... ok
performing post-bootstrap initialization ... ok
syncing data to disk ... ok

Success. You can now start the database server using:

    pg_ctl -D /var/postgresql/data/ -l logfile start $  exit 
```

Enter fullscreen mode Exit fullscreen mode

### 启动 PostgreSQL 服务器

```
#  rcctl enable postgresql
#  rcctl start postgresql
postgresql(ok) 
```

Enter fullscreen mode Exit fullscreen mode

### (完了！)现在 DDL 和 DML 都有了

“`psql`是基于终端的 PostgreSQL 前端”(来自 [doc](https://www.postgresql.org/docs/10/static/app-psql.html) ):

```
$  psql -U postgres 
```

Enter fullscreen mode Exit fullscreen mode

DDL 示例:

```
CREATE DATABASE {% database-name %};
CREATE USER {% user-name %} WITH PASSWORD '{% user-password %}';
GRANT ALL PRIVILEGES ON DATABASE {% database-name %} TO {% user-name %};

-- exit
\q 
```

Enter fullscreen mode Exit fullscreen mode

<center>✿ ✿ ✿</center>

非常感谢您的阅读。如果这篇文章在某种程度上对某人有所帮助，我会很高兴:)