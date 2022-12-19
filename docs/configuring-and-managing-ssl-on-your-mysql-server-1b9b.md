# 在 MySQL 服务器上配置和管理 SSL

> 原文：<https://dev.to/scalegrid/configuring-and-managing-ssl-on-your-mysql-server-1b9b>

[![Configuring and Managing SSL On Your MySQL Server - ScaleGrid Blog](../Images/ebba46602ca59b0666c1a891766b91d7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hmM6Xo5f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://mongodb.scalegrid.io/hubfs/Configuring-and-Managing-SSL-on-Your-MySQL-Server-ScaleGrid-Blog-150551-edited.png)

在这篇博文中，我们回顾了在 MySQL hosting 中配置和管理 SSL 的一些重要方面。这些包括默认配置、禁用 SSL 以及在 MySQL 服务器上启用和实施 SSL。我们的观察基于 MySQL 5.7.21 的社区版本。

阅读原始教程:[在你的 MySQL 服务器上配置和管理 SSL](https://scalegrid.io/blog/configuring-and-managing-ssl-on-your-mysql-server/)

## MySQL 中的默认 SSL 配置

默认情况下，MySQL server 总是安装并启用 SSL 配置。但是，并不强制客户端使用 SSL 进行连接。客户端可以选择使用或不使用 SSL 连接，因为服务器允许这两种类型的连接。让我们看看如何验证 MySQL 服务器的这种默认行为。

当默认情况下在 MySQL 服务器上安装并启用 SSL 时，我们通常会看到以下内容:

1.  *的存在。MySQL 数据目录中的 pem 文件。这些是用于 SSL 的各种客户端和服务器证书和密钥，如这里的[所述](https://dev.mysql.com/doc/refman/5.7/en/creating-ssl-rsa-files-using-mysql.html#creating-ssl-rsa-files-using-mysql-automatic)。
2.  在服务器启动期间，mysqld 错误日志文件中会有一条注释，例如:
    *   *【注意】在数据目录中找到 ca.pem、server-cert.pem 和 server-key.pem。尝试使用它们来启用 SSL 支持。*
3.  “have_ssl”变量的值将为是:

*mysql >显示类似‘have _ SSL’的变量；*

*+--+*

*|变量名称|值|*

*+--+*

*| have_ssl | YES |*

*+--+*

对于 MySQL 客户端，默认情况下，它总是尝试与服务器进行加密网络连接，如果失败，它会退回到未加密模式。

因此，通过使用以下命令连接到 MySQL 服务器:

*mysql -h <主机名> -u <用户名> -p*

我们可以使用 status 命令检查当前客户端连接是否加密:

*mysql >状态*

*-*

*mysql 版本 14.14 发行版 5.7.21，适用于使用 EditLine 包装器的 Linux(x86 _ 64)*

*连接 id: 75*

*当前数据库:*

*当前用户:root@127.0.0.1*

*SSL:使用的密码是 DHE-RSA-AES256-SHA*

*当前寻呼机:stdout*

*使用 outfile: ''*

*使用分隔符:；*

*服务器版本:5.7.21-log MySQL 社区服务器(GPL)*

*协议版本:10*

*连接:127.0.0.1 通过 TCP/IP*

*…………..*

上面突出显示的 SSL 字段表明连接是加密的。但是，我们可以使用以下命令让 MySQL 客户端在没有 SSL 的情况下进行连接:

*mysql -h <主机名> -u <用户名>-p**-SSL-mode =禁用*

*mysql >状态*

*-*

*连接 id: 93*

*当前数据库:*

*当前用户:sgroot@127.0.0.1*

*SSL:未使用*

*当前寻呼机:stdout*

*使用 outfile: ''*

*使用分隔符:；*

*服务器版本:5.7.21-log MySQL 社区服务器(GPL)*

*协议版本:10*

*连接:127.0.0.1 通过 TCP/IP*

*………………………………*

我们可以看到，即使服务器上启用了 SSL，我们也能够在没有 SSL 的情况下连接到服务器。

## 在 MySQL 中禁用 SSL

如果您的要求是完全关闭 MySQL 服务器上的 SSL，而不是默认选项“启用，但可选模式”，我们可以执行以下操作:

1.  删除*。PEM MySQL 数据目录中的证书和密钥文件。
2.  关闭 SSL 选项启动 MySQL。这可以通过添加一个行条目来完成:

my.cnf 文件中的 *ssl=0* 。

我们可以观察到:

1.  mysqld 日志中不会有任何注释，例如:
    *   *【注意】在数据目录中找到 ca.pem、server-cert.pem 和 server-key.pem。尝试使用它们来启用 SSL 支持。*
2.  “have_ssl”变量的值将被禁用:

*mysql >显示类似‘have _ SSL’的变量；*

*+--+*

*|变量名称|值|*

*+--+*

*| have_ssl | DISABLED |*

*+--+*

## 在 MySQL 中实施 SSL

我们看到，虽然 SSL 在 MySQL 服务器上是默认启用的，但它并不是强制的，我们仍然能够在没有 SSL 的情况下进行连接。

现在，通过设置 [require_secure_transport](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_require_secure_transport) 系统变量，我们将能够强制服务器只接受 SSL 连接。这可以通过使用以下命令尝试连接到 MySQL 服务器来验证:

*mysql -h <主机名>-u SG root-p-SSL-mode =禁用*

而且，我们可以看到服务器会拒绝连接，并显示以下错误消息:

错误 3159 (HY000):当- require_secure_transport=ON 时，禁止使用不安全传输的连接。

## 复制通道的 SSL 考虑事项

默认情况下，在 MySQL 复制设置中，从服务器不加密地连接到主服务器。

因此，为了以安全的方式连接到主服务器，从服务器必须使用*MASTER _ SSL = 1；*作为“*将主机更改为“*命令的一部分，该命令指定了连接到主机的参数。请注意，如果您的主机配置为使用 [require_secure_transport](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_require_secure_transport) 强制 SSL 连接，此选项也是强制的。