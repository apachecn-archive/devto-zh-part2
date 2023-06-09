# 拒绝访问:重置 MySQL root 用户密码

> 原文：<https://dev.to/oneearedmusic/access-denied-reset-mysql-root-user-password-2hk4>

*这篇文章最初写于 2016 年 9 月 10 日，当时我正处于人生中的一段紧张时期——我无法访问 MySQL root。我正在将我所有的个人博客文章迁移到 dev.to.*

这是各种 StackOverflow 帖子、用户教程和官方文档的集合，它们帮助我弄清楚如何重置我的本地 MySQL root 密码。

多亏了早期的工程任务，我最初的数据库设置真的搞砸了。因为是学期中间，我没有时间去修理它。现在，几个更新后，我不得不面对野兽，让它工作。为了我自己和其他陷入困境的人将来的参考，这是一个简短的帖子，关于我采取的对我的系统有效的步骤。我在修复过程中发现的所有教程要么不完整，要么不太正确*。*

### 统计数据

操作系统:LTS Ubuntu 16 . 04 . 1

MySQL: 5.7.15

### 状态报告

你已经安装，重新安装，并试图修复一个旧的 MySQL 安装。进入

`mysql -u root -p`

后跟您的密码，返回

`Access denied for user 'root'@'localhost'`

错误，即使你确定这是正确的登录。你也已经尝试了关于如何重置根密码的官方文档，但是失败了。也许你甚至试图重新打包 deb 文件

`sudo dpkg-reconfigure mysql-server-5.7`

失败了。不要害怕——有志者事竟成。

* * *

### 第一步

关闭 Mysql。尝试

`sudo service mysql stop`

或者

`sudo /etc/init.d/mysql stop`

或者查找 PID 并杀死它。确保通过以下方式停止:

`service mysql status`

* * *

### 第二步

在无密码的安全模式下启动 MySQL:

`mysqld_safe --skip-grant-tables &`

注意:这是不安全的！我的任何表中都没有数据，这意味着我不担心恶意读取。“跳过”选项使任何人都可以在没有密码的情况下使用完全权限进行连接。如果您对您的表有任何顾虑，您还应该禁用任何远程访问:

`--skip-networking`

* * *

### 第三步

在新的终端中，使用`mysql`客户端连接到 MySQL 服务器。不需要密码。执行以下步骤:

`use mysql;`

`UPDATE user SET authentication_string=PASSWORD("securepassword") where User='root';`

`UPDATE user SET plugin="mysql_native_password";`

T3】

`quit;`

简单解释:第二行是你设置密码的地方。MySQL 5.6 或更老版本与最新的 5.7+的一个区别是列名从`PASSWORD`切换到`AUTHENTICATION_STRING.`更老的教程使用前者。

如果您错过了第三个“设置插件”语句，您将成功更新您的密码，但仍然无法连接到您的服务器。例如，我的系统将插件值设置为“认证套接字”即使有了正确的登录信息，我的服务器也会抛出关于我丢失了套接字的错误，我需要关机，在安全模式下重启，并再次切换这两个值。

最后,“flush privileges”命令重新加载服务器内存中的授权表副本。与更高级别的 GRANT 或 SET 命令不同，使用 UPDATE 修改用户表不会立即将更改加载到表中。

* * *

### 第四步

停止你的安全 mysqld，正常启动 mysql 服务器。您应该可以通过以下方式使用新密码进行连接

`mysql -u root -p`

享受您的新数据库访问！

### 链接

MySQL 参考手册:[如何重置 root 密码](http://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html)

堆栈溢出: [MySQL 用户数据库没有密码列](http://stackoverflow.com/questions/30692812/mysql-user-db-does-not-have-password-columns-installing-mysql-on-osx)

堆栈溢出:[如何停止 mysqld](http://stackoverflow.com/questions/11091414/how-to-stop-mysqld)

堆栈溢出: [MySQL 失败:MySQL“错误 1524(hy 000)”](http://stackoverflow.com/questions/37879448/mysql-fails-on-mysql-error-1524-hy000-plugin-auth-socket-is-not-loaded)

[停止使用刷新权限](http://dbahire.com/stop-using-flush-privileges/)

### 大功告成！