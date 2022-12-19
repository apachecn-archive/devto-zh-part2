# 教程–在 Ubutnu 18.04 上安装 MySQL 后，修复了“用户' root'@'localhost '的访问被拒绝”错误

> 原文：<https://dev.to/joshualjohnson/tutorial--after-installing-mysql-on-ubutnu-1804-fix-access-denied-for-user-rootlocalhost-error-2cib>

最近在 Ubuntu 18.04 上安装`mysql-server`时遇到一个问题。我发现，在我用我的标准方法`sudo apt install mysql-server`安装了`mysql-server`并运行了`mysql_secure_installation`之后，我的 root 用户在试图用`mysql -u root -p`访问 MySql 时被拒绝。我总是会得到以下错误:

```
ERROR 1698 (28000): Access denied for user 'root'@'localhost' 
```

Enter fullscreen mode Exit fullscreen mode

## 修复用户“root”@“localhost”Ubuntu 的访问被拒绝

经过几个小时的努力，我想出了正确的解决方案，这就是我要解决的问题:

停止 MySQL 服务器运行:

```
sudo service mysql stop 
```

Enter fullscreen mode Exit fullscreen mode

我们需要为 MySQL 创建一个目录来存储 access socket 文件，这样我们就可以使用`mysqld`命令。如果没有此目录，命令将会失败。

```
sudo mkdir -p /var/run/mysqldsudo chown mysql:mysql /var/run/mysqld 
```

Enter fullscreen mode Exit fullscreen mode

使用以下命令手动启动 MySQL】

```
sudo /usr/sbin/mysqld --skip-grant-tables --skip-networking & 
```

Enter fullscreen mode Exit fullscreen mode

我们刚刚运行的命令将允许我们现在无需密码就可以访问 MySQL。不管什么原因，当 MySQL 被安装时，我们用来安装它的密码没有应用到根用户。现在，您可以使用以下`mysql`命令手动更改您的密码

无密码登录 MySql】

```
mysql -u root 
```

Enter fullscreen mode Exit fullscreen mode

刷新权限，将待定权限应用于安装过程中创建的 root 用户:

```
mysql> FLUSH PRIVILEGES; 
```

Enter fullscreen mode Exit fullscreen mode

使用以下三个命令更新 root 用户的密码:

```
mysql> USE mysql;

mysql> UPDATE user SET authentication\_string=PASSWORD("{PASSWORD\_HERE}") WHERE User='root';

mysql> UPDATE user SET plugin="mysql\_native\_password" WHERE User='root'; 
```

Enter fullscreen mode Exit fullscreen mode

结束您的 MySQL 会话:

```
mysql> quit 
```

Enter fullscreen mode Exit fullscreen mode

现在您已经成功重置了您的密码，您将想要终止`mysqld` :

```
sudo pkill mysqld 
```

Enter fullscreen mode Exit fullscreen mode

重启你的 MySQL 服务器:

```
sudo service mysql start 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

我不确定 MySQL 在安装`mysql-server`时被提示时没有正确设置 root 密码和权限的确切原因。但我知道这对我来说是一种痛苦。如果你遇到这个问题，并发现这个教程很有帮助，请经常回来查看新的[教程](https://ua1.us/news/topic/tutorials/)。

post [教程——在 Ubutnu 18.04 上安装 MySQL 后，修复了“拒绝用户' root'@'localhost '”的访问错误](https://ua1.us/news/tutorials/tutorial-fix-mysql-access-denied-for-user-rootlocalhost-error/)首先出现在 [UA1 实验室](https://ua1.us)。