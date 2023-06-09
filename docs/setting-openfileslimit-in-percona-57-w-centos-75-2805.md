# 在 Percona 5.7 和 CentOS 7.5 中设置“打开文件限制”

> 原文：<https://dev.to/gsinti/setting-openfileslimit-in-percona-57-w-centos-75-2805>

# 这个问题

我们的一个数据库服务器崩溃了，所以我查看了日志，发现了一些典型的警告:

```
[Warning] Changed limits: max_open_files: 5000 (requested 5125)
[Warning] Changed limits: table_open_cache: 1983 (requested 2000) 
```

这些默认值就是不行，所以我确保在`/etc/security/limits.conf`中增加了系统限制并重启了服务器。然而，修改`my.cnf`设置来改变`open_files_limit`以使用新的限制并不可行，MySQL 继续报告`open_files_limit`的默认值。

# 寻找答案

无数个小时后，在数十篇博客文章和 StackOverflow 指南之后，我仍然无法坚持这个变量！每个向导做的事情都稍有不同，没有两个是相同的。有些人觉得太“侵入性”，接触了太多的系统文件和设置，没有一个能解决我的问题。我想要一个简单、直接的解决方案，适用于我的特定版本 Percona 和 CentOS。

终于，我破解了！

感谢 [Sveta 的帖子](https://www.percona.com/blog/2017/10/12/open_files_limit-mystery/)的帮助，我设法想出了一个适合我的优雅的解决方案。

# 修罗

### 1。确保流程有足够的资源可用

```
vi /etc/security/limits.conf 
```

粘贴以下内容:

```
* soft nofile 65535
* hard nofile 65535 
```

<sub>您可以根据自己的需要随意调整，因为您可能想要更多的打开文件，或者为用户`root`或`mysql`指定单独的值，而不是使用`*`通配符。</sub>

### 2。为`mysqld`创建新的服务配置文件

```
systemctl edit mysqld 
```

粘贴以下内容:

```
[Service]
LimitNOFILE=infinity 
```

<sub>使用`infinity`将简单地从系统中继承。您可以指定软限制和硬限制之间的任何值。</sub>

### 3。重新加载 systemd 配置单元

```
systemctl daemon-reload 
```

就是这样！重启 MySQL，查询时应该会出现新的`open_files_limit`值:

```
mysql> SELECT @@open_files_limit;
+--------------------+
| @@open_files_limit |
+--------------------+
|              65535 |
+--------------------+ 
```

如果重新启动机器或更新 MySQL，这些更改也将持续，因为服务配置文件中的设置不会被覆盖(而主服务配置文件会被覆盖)。

其他指南也建议创建一个新的服务配置文件，但是一些系统差异会导致不同的拼写(`systemctl edit mysql`与`systemctl edit mysqld`)，这是我最大的困扰。此外，您不需要修改`my.cnf`来指定那里的`open_files_limit`，因为 MySQL 将从系统继承。我觉得这个解决方案是最简单的，需要对文件做最少的修改。

如果你很难坚持这个设置，希望这对你有用！