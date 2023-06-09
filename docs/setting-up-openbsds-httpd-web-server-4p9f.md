# OpenBSD httpd 6.3: Web 服务器

> 原文：<https://dev.to/nabbisen/setting-up-openbsds-httpd-web-server-4p9f>

## [T1】简介](#intro)

OpenBSD 有自己的 web 服务器，名为“ [httpd](https://man.openbsd.org/httpd.8) ”。

* * *

### 背景

在 OpenBSD 上可以安装 [Nginx](https://www.nginx.com/resources/wiki/start/topics/tutorials/openbsd/) 和 [Apache](http://httpd.apache.org/) (称为“apache-httpd”)。

然而，由于历史背景的原因，他们并没有得到官方的支持。
Nginx 在 2015 年 [5.6 版本](https://man.openbsd.org/OpenBSD-5.6/man8/nginx.8)结束时从官方手册中消失。
OpenBSD httpd 随后加入。

也可以手动安装 [Caddy](https://caddyserver.com/download) 。

* * *

坦率地说，OpenBSD httpd 的 conf 例子和教程似乎比他们少。因此，有些人可能认为这更困难。

我喜欢 OpenBSD httpd，因为它简单、最小化[并带有明确许可的](https://cvsweb.openbsd.org/src/share/misc/license.template?rev=HEAD)，健壮且安全，因此，在我看来，它很漂亮。
还有，随着[中继](https://man.openbsd.org/relayd.8)它变得更加强大。

<center>✿ ✿ ✿</center>

### 环境

*   操作系统:OpenBSD 6.3 amd64

## 程序

### 1。准备配置文件

激活 httpd 服务需要 [`httpd.conf`](https://man.openbsd.org/httpd.conf.5) 。
默认路径是`/etc/httpd.conf`。

#### 1-1。制作`/etc/httpd.conf`

```
#  # Using fish shell:
#  if not test -e /etc/httpd.conf; touch /etc/httpd.conf; end 
```

Enter fullscreen mode Exit fullscreen mode

当然，简单的用`touch /etc/httpd.conf`或者`vi /etc/httpd.conf`都可以。

#### 1-2。编辑`/etc/httpd.conf`

```
#[ MACROS ] ext_ip = "127.0.0.1"
# ext_ip = "*"      # open to the outside network
# ext_ip = "egress" # open to only the primary IP address of the network interface 
# [ GLOBAL CONFIGURATION ]
# none 
# [ SERVERS ] server "default" {
    listen on $ext_ip port 80
    root "/htdocs/my.domain"
}

# [ TYPES ] types {
    include "/usr/share/misc/mime.types"
} 
```

Enter fullscreen mode Exit fullscreen mode

*(注意)* `root`“服务器”部分的属性是指`/var/www`下的目录。官方文件在[全球配置](https://man.openbsd.org/httpd.conf.5#GLOBAL_CONFIGURATION)部分提到:

> chroot 目录
> 设置 chroot(2)目录。如果未指定，则默认为/var/www，即 www 用户的主目录。

可选地添加其他服务器定义，如下:

```
server "www.https-example.domain" { 
    alias "https-example.domain" 
    listen on $ext_ip port 80 
    listen on $ext_ip tls port 443
    tls {
        key         "/etc/ssl/private/www.https-example.domain.key"
        certificate "/etc/ssl/www.https-example.domain.crt"
    }
    root "/htdocs/www.https-example.domain" 
}

server "www.fastcgi-example.domain" {
    alias "fastcgi-example.domain"
    listen on $ext_ip port 80
    fastcgi socket ":{% port-number %}"
} 
```

Enter fullscreen mode Exit fullscreen mode

公文是[这里](https://man.openbsd.org/httpd.conf.5)。

#### 1-3。让 index.html 接受测试

```
#  mkdir -p /var/www/htdocs/my.domain
#  chown {% user %}:{% group %} /var/www/htdocs/my.domain # if necessary
$  echo "Hello, world. from OpenBSD httpd" > /var/www/htdocs/my.domain/index.html 
```

Enter fullscreen mode Exit fullscreen mode

### 2。激活 httpd 服务

启用 httpd:

```
#  rcctl enable httpd 
```

Enter fullscreen mode Exit fullscreen mode

**注:这次`/etc/rc.conf.local`是这样创建的:*

```
#  cat /etc/rc.conf.local
httpd_flags= 
```

Enter fullscreen mode Exit fullscreen mode

并启动它:

```
#  rcctl start httpd
httpd(ok) 
```

Enter fullscreen mode Exit fullscreen mode

**注:默认设置:`httpd_flags=NO`、`# rcctl -f start httpd`下可以强制启动 httpd。*

### 3。测试服务器是否在监听

```
$  curl localhost:80
Hello, world. from OpenBSD httpd 
```

Enter fullscreen mode Exit fullscreen mode

<center>✿ ✿ ✿</center>

## 其他

非常感谢您的阅读。
为🕊服务愉快