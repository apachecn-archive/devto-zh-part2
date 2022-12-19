# HAProxy 入门:从代码源安装

> 原文：<https://dev.to/tmidi/getting-started-with-haproxy-install-from-code-source-5c4g>

## [T1】简介](#intro)

这篇文章摘自我的个人博客，我将演示如何从源代码安装 HAProxy。根据[维基百科](https://en.wikipedia.org/wiki/HAProxy)，自 2000 年以来，HAProxy 一直由[威利·塔罗](http://1wt.eu/#wami)编写和维护。HAProxy 是免费的开源软件(FOSS)，它为基于 TCP(传输控制协议)和 HTTP(超文本传输协议)的应用程序提供高可用性负载平衡器和代理服务器，这些应用程序可以在多个服务器之间传播请求。它是用 C 语言编写的，因此以快速高效而闻名。

如果你好奇，这个 [HAProxy 页面](http://www.haproxy.org/#feat)提供了更多关于它的特性的细节。

## 安装

在本指南中，我们将安装 1.8 的最新稳定版本(截至 2018 年 5 月 9 日)。目前在 RHEL 下可用的 HAProxy 是 Debian/Ubuntu 上的`1.5.18`和`1.8.8`。在进行下一步之前，确保您已经安装了`gcc`、`pcre-static`和`pcre-devel`:

```
# CentOS RHEL:
sudo yum -y install make gcc perl pcre-devel zlib-devel

# Debian/Ubuntu:
sudo apt install make gcc perl pcre-devel zlib-devel 
```

Enter fullscreen mode Exit fullscreen mode

启用 IP 转发并绑定到非本地 IP 地址:

```
# echo "net.ipv4.ip_forward = 1" >> /etc/sysctl.conf
# echo "net.ipv4.ip_nonlocal_bind = 1" >> /etc/sysctl.conf
# sysctl -p
net.ipv4.ip_forward = 1
net.ipv4.ip_nonlocal_bind = 1 
```

Enter fullscreen mode Exit fullscreen mode

下载源代码:

```
wget https://www.haproxy.org/download/1.8/src/haproxy-1.8.8.tar.gz 
```

Enter fullscreen mode Exit fullscreen mode

提取文件并更改目录:

```
tar xvzf haproxy-1.8.8.tar.gz && cd haproxy-1.8.8 
```

Enter fullscreen mode Exit fullscreen mode

编译程序:

```
make TARGET=linux2628 USE_PCRE=1 USE_OPENSSL=1 USE_ZLIB=1 
```

Enter fullscreen mode Exit fullscreen mode

这是目标列表:

*   用于 Linux 2.2 的 linux22
*   适用于 Linux 2.4 及更高版本的 linux24(默认)
*   适用于 Linux 2.4 的 linux24e，支持工作 epoll (> 0.21)
*   适用于 Linux 2.6 及以上版本的 linux26
*   适用于 Linux 2.6.28、3.x 和更高版本的 linux2628(启用拼接和 tproxy)
*   适用于 solaris 8 或 10 的 Solaris(其他未经测试)
*   freebsd 为 FreeBSD 5 到 10(其他未经测试)
*   用于 netbsd 的 NetBSD
*   用于 Mac OS/X 的 osx
*   openbsd 5.7 及以上版本的 OpenBSD
*   用于 AIX 5.1 的 aix51
*   用于 AIX 5.2 的 aix52
*   cygwin 为了 Cygwin
*   为俳句而俳句
*   通用于任何其他操作系统或版本。
*   自定义手动调整每个设置

您还可以使用以下选项编译程序:
USE_PCRE=1 使用 libpcre，无论您的系统上有什么形式(共享或静态)。
USE_ZLIP=1 使用 zlib 压缩库。
使用 _OPENSSL=1 使用 GNU makefile 添加对 SSL 的本地支持。

安装 HAProxy:

```
sudo make install 
```

Enter fullscreen mode Exit fullscreen mode

至此，我们应该已经安装了所选的 HAProxy 版本。现在我们需要设置 HAProxy。

## 设置:

添加 HAProxy 用户:

```
id -u haproxy &> /dev/null || useradd -s /usr/sbin/nologin -r haproxy 
```

Enter fullscreen mode Exit fullscreen mode

将位于提取的 HAProxy 目录中的 HAProxy 二进制文件复制到`/usr/sbin/`:

```
cp haproxy /usr/sbin/ 
```

Enter fullscreen mode Exit fullscreen mode

创建手册页:

```
wget -qO - https://raw.githubusercontent.com/horms/haproxy/master/doc/configuration.txt | gzip -c > /usr/share/doc/haproxy/configuration.txt.gz 
```

Enter fullscreen mode Exit fullscreen mode

创建 Systemd 服务脚本来管理 HAProxy，您可以自行决定使用以下内容:

```
sudo wget https://gist.githubusercontent.com/tmidi/1699a358533ae876513e2887fec6fbe2/raw/6c07ce39adc56c731d2bbeb88b90d8bbc636f3ea/haproxy.service -O /lib/systemd/system/haproxy.service 
```

Enter fullscreen mode Exit fullscreen mode

或者用以下内容创建`/lib/systemd/system/haproxy.service`:

```
[Unit]
Description=HAProxy Load Balancer
Documentation=man:haproxy(1)
Documentation=file:/usr/share/doc/haproxy/configuration.txt.gz
# allows us to do millisecond level restarts without triggering alert in Systemd
StartLimitInterval=0
StartLimitBurst=0
After=network.target syslog.service
Wants=syslog.service

[Service]
Environment="CONFIG=/etc/haproxy/haproxy.cfg" "PIDFILE=/run/haproxy.pid"
# EXTRAOPTS and RELOADOPS come from this default file
EnvironmentFile=-/etc/default/haproxy
ExecStartPre=/usr/sbin/haproxy -f $CONFIG -c -q
ExecStart=/usr/sbin/haproxy -W -f $CONFIG -p $PIDFILE $EXTRAOPTS
ExecReload=/usr/sbin/haproxy -f $CONFIG -c -q $EXTRAOPTS $RELOADOPTS
ExecReload=/bin/kill -USR2 $MAINPID
KillMode=mixed
Restart=always
Type=forking

[Install]
WantedBy=multi-user.target 
```

Enter fullscreen mode Exit fullscreen mode

启用并启动`haproxy.service`:

```
systemctl enablle haproxy.service

systemctl start haproxy.service 
```

Enter fullscreen mode Exit fullscreen mode

## 
 [T3】
结论](#conclusion) 

这就结束了 HAProxy 安装步骤。然而，本指南仅涵盖安装步骤，在未来的帖子中，我将演示如何配置 HAProxy，以便在使用粘性会话和 SSL 直通时平衡三个后端 web 服务器的负载。

如果你觉得这个指南有帮助，请与你的朋友和同事分享。我在 [Twitter](https://twitter.com/taleeb_midi) 和 [LinkedIn](https://www.linkedin.com/in/taleebmidi/) 上，如果你有任何问题或建议，让我们联系或让我知道。