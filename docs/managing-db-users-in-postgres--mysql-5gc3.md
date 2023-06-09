# 在 postgres & mysql 中管理数据库用户

> 原文：<https://dev.to/intricatecloud/managing-db-users-in-postgres--mysql-5gc3>

当你不得不在 mysql 和 postgres 数据库中添加/删除/更新/和/20/other/东西时，当你试图做看似简单的事情时，你会迷失方向。我一直保存着一个运行文档，在那里我保存了 SQL 的片段，以备不时之需，如果你碰巧同时使用 postgres 和 mysql 并需要管理用户访问，我希望它们能有所帮助。

下面，我们得到了 10 个用于管理用户的常用 SQL 片段。

1.  添加用户
2.  重设密码
3.  检查用户权限
4.  撤销特权
5.  删除用户
6.  密码/临时凭证过期
7.  检查到期时间
8.  授予只读访问权限
9.  检查用户是否存在
10.  一些可能有帮助的调试信息

* * *

如果您想在开发数据库中使用这些脚本，可以使用 docker 启动它们并连接到它:

在 postgres:

```
# start postgres in docker
docker run --rm --name pgsql -e POSTGRES_PASSWORD=password -p 5555:5432 -d postgres

# connect to postgres
psql -h 127.0.0.1 -p 5555 -U postgres -d postgres 
```

Enter fullscreen mode Exit fullscreen mode

在 mysql:

```
# start mysql in docker
docker run --rm --name mysql -p3307:3306 -e MYSQL_ROOT_PASSWORD=password -d mysql
# connect to mysql
mysql -h 127.0.0.1 -P 3307 -u root -p 
```

Enter fullscreen mode Exit fullscreen mode

* * *

# 1。添加用户

在 postgres:

```
CREATE USER '${user}' WITH PASSWORD '${password}';
GRANT ALL ON DATABASE '${db}' TO '${user}'; -- grants read/write to everything in this database instance
-- OR
GRANT CONNECT ON DATABASE '${db}' to '${user}'; -- only allows the user to connect, but nothing more. 
```

Enter fullscreen mode Exit fullscreen mode

*[https://www . PostgreSQL . org/docs/8.0/static/SQL-create user . html](https://www.postgresql.org/docs/8.0/static/sql-createuser.html)

在 mysql:

```
CREATE USER '${user}' IDENTIFIED BY '${password}';
GRANT ALL PRIVILEGES ON ${db}.* TO '${user}'; 
```

Enter fullscreen mode Exit fullscreen mode

*[https://dev.mysql.com/doc/refman/8.0/en/adding-users.html](https://dev.mysql.com/doc/refman/8.0/en/adding-users.html)

# 2。重设密码

在 postgres:

```
ALTER ROLE '${user}' WITH PASSWORD '${pw}'; 
```

Enter fullscreen mode Exit fullscreen mode

*[https://www . PostgreSQL . org/docs/8.0/static/SQL-alter user . html](https://www.postgresql.org/docs/8.0/static/sql-alteruser.html)

在 mysql:

```
ALTER USER '${user}' IDENTIFIED BY '${pw}' 
```

Enter fullscreen mode Exit fullscreen mode

*这里有另外两种方法:[https://www.geeksforgeeks.org/mysql-change-user-password/](https://www.geeksforgeeks.org/mysql-change-user-password/)

# 3。检查用户权限

在 postgres 中:

这将列出所有数据库，并向您显示有权访问它的所有角色。

```
postgres=# \l
                                 List of databases
   Name    |  Owner   | Encoding |  Collate   |   Ctype    |   Access privileges
----------------+----------+----------+------------+------------+-----------------------
 postgres  | postgres | UTF8     | en_US.utf8 | en_US.utf8 |
 template0 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
           |          |          |            |            | postgres=CTc/postgres
 template1 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
           |          |          |            |            | postgres=CTc/postgres 
```

Enter fullscreen mode Exit fullscreen mode

这将显示所有角色名称及其包含的其他角色。

```
postgres=# \du
                                   List of roles
 Role name |                         Attributes                         | Member of
----------------+------------------------------------------------------------+-----------
 postgres  | Superuser, Create role, Create DB, Replication, Bypass RLS | {} 
```

Enter fullscreen mode Exit fullscreen mode

这将显示用户可以访问的所有表格(感谢提示， [@dmfay](https://dev.to/dmfay) )

```
postgres=# \z
                                 Access privileges
 Schema |  Name  | Type  |     Access privileges     | Column privileges | Policies
-------------+--------+-------+---------------------------+-------------------+----------
 public | foobar | table | postgres=arwdDxt/postgres+|                   |
        |        |       | dperez=r/postgres         |                   |
 public | test   | table | postgres=arwdDxt/postgres+|                   |
        |        |       | dperez=r/postgres         |                   |
(2 rows)
 ... 
```

Enter fullscreen mode Exit fullscreen mode

请看[关于助学金的 postgres 文件来解读这个描述](https://www.postgresql.org/docs/7.4/static/sql-grant.html)

在 mysql:

```
mysql> SHOW GRANTS FOR '${user}';
+---------------------------------------------------------------------+
| Grants for root@localhost                                           |
+---------------------------------------------------------------------+
| GRANT ALL PRIVILEGES ON *.* TO '${user}'@'localhost' WITH GRANT OPTION |
+---------------------------------------------------------------------+ 
```

Enter fullscreen mode Exit fullscreen mode

# 4。撤销特权

在 postgres:

```
REVOKE ALL PRIVILEGES ON DATABASE '${db}' from '${user}'; 
```

Enter fullscreen mode Exit fullscreen mode

*[https://www.postgresql.org/docs/9.1/static/sql-revoke.html](https://www.postgresql.org/docs/9.1/static/sql-revoke.html)

在 mysql:

```
REVOKE ALL PRIVILEGES ON '${db}.*' FROM '${user}'; 
```

Enter fullscreen mode Exit fullscreen mode

*[https://dev.mysql.com/doc/refman/8.0/en/revoke.html](https://dev.mysql.com/doc/refman/8.0/en/revoke.html)

# 5。删除用户

在 postgres:

```
DROP ROLE IF EXISTS '${user}'; 
```

Enter fullscreen mode Exit fullscreen mode

您可能会看到这样的错误消息:
`ERROR: role "testuser" cannot be dropped because some objects depend on it DETAIL: $somethingUseful`
这意味着您的用户依赖于其他对象——比如特权和其他表。在这种情况下，您需要运行:

```
REVOKE ALL PRIVILEGES ON DATABASE '${db}' from '${user}';
DROP ROLE IF EXISTS '${user}'; 
```

Enter fullscreen mode Exit fullscreen mode

如果失败，则尝试

```
-- another_user could == 'root' (or an admin account)
REASSIGN OWNED BY '${user}' TO '${another_user}'; 
DROP ROLE IF EXISTS '${user}'; 
```

Enter fullscreen mode Exit fullscreen mode

*[https://stack overflow . com/questions/3023583/PostgreSQL-how-to-quick-drop-a user-with-existing-privileges](https://stackoverflow.com/questions/3023583/postgresql-how-to-quickly-drop-a-user-with-existing-privileges)

这里具有讽刺意味的是，您通常会使用`DROP ROLE IF EXISTS`,这样如果角色不存在，脚本就不会出错。但是，当角色不存在时，您会得到一个成功的响应。当角色确实存在时，它可能会有特权并要求您运行`REVOKE ALL PRIVILEGES`——不幸的是，您不能运行`REVOKE ALL ... IF EXISTS`。这使得整个`DROP ROLE IF EXISTS`便利声明变得毫无用处。

在 mysql:

```
DROP USER IF EXISTS '${user}'; 
```

Enter fullscreen mode Exit fullscreen mode

mysql，谢谢你这么直截了当。

# 6。使密码/临时凭证过期

在 postgres:

```
ALTER ROLE '${user}' WITH PASSWORD '${password}' VALID UNTIL '${expiration_timestamp}'; 
```

Enter fullscreen mode Exit fullscreen mode

这里，`expiration_timestamp`的格式为`Nov 3 12:00:00 2018 +1`,表示密码失效的时间。
*[https://www . PostgreSQL . org/docs/9.2/static/SQL-alter role . html](https://www.postgresql.org/docs/9.2/static/sql-alterrole.html)

在 mysql:

```
ALTER USER '${user}' PASSWORD EXPIRE INTERVAL 1 DAY; 
```

Enter fullscreen mode Exit fullscreen mode

语句好像只允许`DAY`作为区间单位。所以`INTERVAL 90 DAY`是正确的(注意:day 中没有复数 s)。

# 7。如何检查密码的到期时间

一旦设置了密码到期时间，查看该时间将会非常有用。

在 postgres:

```
SELECT valuntil AS valid_until FROM pg_user WHERE usename = '${user}'; 
```

Enter fullscreen mode Exit fullscreen mode

在 mysql:

```
SELECT DATE_ADD(password_last_changed, interval password_lifetime day) AS valid_until FROM mysql.user WHERE user = '${user}'; 
```

Enter fullscreen mode Exit fullscreen mode

# 8。授予只读访问权限

在 postgres:

```
GRANT USAGE ON SCHEMA public TO '${user}';
GRANT SELECT ON ALL TABLES IN SCHEMA public TO '${user}'; 
```

Enter fullscreen mode Exit fullscreen mode

* [关于`grant all privileges on database`实际做什么的相关线程](https://serverfault.com/questions/198002/postgresql-what-does-grant-all-privileges-on-database-do)
稍后您可能会发现，如果您添加了一个表，这个只读用户没有权限读取该表。在这种情况下，运行以下命令以确保您在默认情况下获得了对表的特权:

```
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT SELECT ON TABLES TO '${user}'; 
```

Enter fullscreen mode Exit fullscreen mode

*更多信息请点击此处-[https://stack overflow . com/questions/760210/how-do-you-create-a-read-only-user-in-PostgreSQL](https://stackoverflow.com/questions/760210/how-do-you-create-a-read-only-user-in-postgresql)

在 mysql:

```
GRANT SELECT ON '${db}'.* TO '${user}'; 
```

Enter fullscreen mode Exit fullscreen mode

# 9。检查用户是否存在

在 postgres:

```
SELECT 1 FROM pg_roles WHERE rolname='${user}'; 
```

Enter fullscreen mode Exit fullscreen mode

在 mysql:

```
SELECT EXISTS(SELECT 1 FROM mysql.user WHERE user = '${user}'); 
```

Enter fullscreen mode Exit fullscreen mode

# 10。获取一些关于当前用户的调试信息

在 postgres:

```
postgres=# \conninfo
You are connected to database "postgres" as user "postgres" on host "127.0.0.1" at port "5555".
postgres=# SELECT CURRENT_USER;
 current_user
-------------------
 postgres
(1 row)

postgres=# SELECT CURRENT_USER(); -- you can't use it as a function
ERROR:  syntax error at or near "("
LINE 1: SELECT CURRENT_USER(); 
```

Enter fullscreen mode Exit fullscreen mode

在 mysql:

```
mysql> select CURRENT_USER();
mysql> select CURRENT_USER; -- both this and the above function return the same result
+----------------+
| current_user() |
+----------------+
| dperez@%       |
+----------------+
mysql> status
-------------------
mysql  Ver 14.14 Distrib 5.7.22-22, for osx10.11 (x86_64) using  EditLine wrapper

Connection id:    683
Current database: dpereztest
Current user:   dperez@10.162.9.100
SSL:      Cipher in use is DHE-RSA-AES128-SHA
Current pager:    less
Using outfile:    ''
Using delimiter:  ;
Server version:   8.0.11 Source distribution
Protocol version: 10
Connection:   127.0.0.1 via TCP/IP
Server characterset:  utf8mb4
Db     characterset:  utf8mb4
Client characterset:  utf8
Conn.  characterset:  utf8
TCP port:   5555
Uptime:     2 days 7 hours 55 min 53 sec

Threads: 3  Questions: 107602  Slow queries: 0  Opens: 285  Flush tables: 2  Open tables: 259  Queries per second avg: 0.534
------------------- 
```

Enter fullscreen mode Exit fullscreen mode

我希望这有助于下次您需要管理 postgres 或 mysql 数据库上的用户时作为有用的指南。您还可以做很多事情来定制您的用户及其访问级别，但是本指南应该作为一个起点，提供文档链接来帮助您做您想做的事情。

* * *

如果您跨多个团队管理用户和凭据，您可能需要检查:

*   在大型团队中管理密码时需要注意的事项。
*   [最近一次在 AWS 参数存储中存储机密的经历](https://www.intricatecloud.io/2018/10/a-few-lessons-learned-using-aws-parameter-store/)

有兴趣看更多这样的文章吗？在 dev.to 上关注我或[订阅我的列表](http://eepurl.com/dHhWQb)以便在我有新帖子时得到通知。