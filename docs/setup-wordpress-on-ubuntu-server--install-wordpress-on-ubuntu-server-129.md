# 在 Ubuntu 服务器上安装 Wordpress |在 Ubuntu 服务器上安装 Wordpress

> 原文：<https://dev.to/ishwar/setup-wordpress-on-ubuntu-server--install-wordpress-on-ubuntu-server-129>

# 在 Ubuntu 服务器上安装 Wordpress 很容易:-)

# 安装 PHP7、Mysql、Apache2

1.  安装 Apache Web 服务器

    安装 apache2 apache2-utils

我们需要让 Apache2 web 服务器在系统启动时启动，并按如下方式启动服务:

```
sudo systemctl enable apache2
sudo systemctl start apache2 
```

Enter fullscreen mode Exit fullscreen mode

检查状态:
sudo 服务 apache2 状态

1.  安装 MySQL 数据库服务器

接下来，我们需要通过运行下面的命令来安装 mysql 数据库服务器，系统会提示您设置 MySQL 的 root 用户密码

```
sudo apt-get install mysql-client mysql-server 
```

Enter fullscreen mode Exit fullscreen mode

1.  安装 PHP 和模块

    sudo apt-get install PHP 7.0 PHP 7.0-MySQL libapache 2-mod-PHP 7.0 PHP 7.0-CLI PHP 7.0-CGI PHP 7.0-GD

为了测试 php 是否与 web 服务器协同工作，我们需要在/var/www/html 中创建一个 info.php 文件。

$ sudo vi /var/www/html/info.php

并将下面的代码粘贴到文件中，保存并退出。

phpinfo()；
？>

完成后，打开你的网络浏览器，输入这个地址。

1.  安装 WordPress CMS

下载最新的 WordPress 包，并通过在终端上发出下面的命令来提取它:

$ wget-c[http://wordpress.org/latest.tar.gz](http://wordpress.org/latest.tar.gz)

现在将 WordPress 文件从解压的文件夹移动到 Apache 默认的根目录/var/www/html/:

```
sudo rsync -av wordpress/* /var/www/html/ 
```

Enter fullscreen mode Exit fullscreen mode

给 CHMOD 权限，在网站目录上设置正确的权限，也就是给 web 服务器 WordPress 文件的所有权，如下所示:

```
sudo chown -R www-data:www-data /var/www/html/
sudo chmod -R 755 /var/www/html/ 
```

Enter fullscreen mode Exit fullscreen mode

1.  登录 mysql，创建用户和数据库

执行下面的命令并提供 root 用户密码，然后按 Enter 键打开 mysql shell:

mysql -u root -p

//创建数据库

mysql >创建数据库 wp _ myblogs

//授予访问权限

mysql >授予 wp_myblogs 上的所有权限。*到“您的用户名这里”@“本地主机”由“您的选择密码这里”标识；

mysql >刷新权限；
mysql >退出；
转到/var/www/html/目录，将现有的 wp-config-sample.php 重命名为 wp-config.php:

苏多 mv wp-config-sample.php wp-config.php

然后在 MySQL settings 部分用您的数据库信息更新它

define('数据库名称'，'数据库名称')；
定义(' DB_USER '，' username ')；
定义(' DB_PASSWORD '，' PASSWORD ')；
定义(' DB_HOST '，' localhost ')；
define('DB_CHARSET '，' utf8 ')；
定义(' DB_COLLATE '，' ')；

最后，使用下面的命令重新启动 web 服务器和 mysql 服务:

sudo system CTL restart Apache 2 . service
sudo system CTL restart MySQL . service