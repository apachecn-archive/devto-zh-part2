# 如何在 Manjaro Linux 中安装 PostgreSQL

> 原文：<https://dev.to/lobo_tuerto/how-to-install-postgresql-in-manjaro-linux--24bj>

# 安装过程

在 **Manjaro Linux** 中设置 **PostgreSQL** 非常容易。

只要遵循这些步骤，您马上就会有一个工作的安装。

安装`postgresql`包:

```
sudo pacman -S postgresql 
```

Enter fullscreen mode Exit fullscreen mode

然后用
为`postgres`用户设置一个密码

```
sudo passwd postgres 
```

Enter fullscreen mode Exit fullscreen mode

切换到`postgres`用户帐户并初始化数据库集群:

```
sudo su postgres -l # or sudo -u postgres -i
initdb --locale $LANG -E UTF8 -D '/var/lib/postgres/data/'
exit 
```

Enter fullscreen mode Exit fullscreen mode

`initdb`的选项如下:

*   `--locale`是在`/etc/locale.conf`中定义的。
*   `-E`是新数据库的默认编码。
*   `-D`是存储数据库集群的默认位置。

现在，启动并启用`postgresql.service` :

```
sudo systemctl enable --now postgresql.service 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！

# 链接

*   [拱门里的 PostgreSQL](https://wiki.archlinux.org/index.php/PostgreSQL)
*   [拱门内的系统](https://wiki.archlinux.org/index.php/Systemd)