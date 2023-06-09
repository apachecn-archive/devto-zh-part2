# caddy web 服务器入门

> 原文：<https://dev.to/legobox/another-kickass-server-called-caddy-4o1f>

> 这篇文章最初发表在我的博客上，地址是 [legobox](https://medium.com/legobox/another-kickass-server-called-caddy-2bc24e8d8028)

# 背景

去年我偶然发现了一个叫做 caddy 的服务器，我有点喜欢它，因为它的名字听起来和我最喜欢的说唱歌手 cardi b 的名字很相似，听起来很奇怪，对吗？开个玩笑，这不是我喜欢 caddy web 服务器的原因，相反，它是一个比 nginx 安装和配置简单得多的服务器，更重要的是，它为您的域集成了 ssl 证书，其中包括通配符，在本文中，我们将鸟瞰服务器，并了解它为什么是一个伟大的工具。

# 先决条件

在深入研究之前，真的没有太多需要了解的，但是如果你有以下几点，那就更好了。

*   至少对服务器和代理有很好的理解。
*   对 SSL -安全外壳层和 HTTPs 的一般了解

有了这些，你就可以走了。

# 出身。

Long story short caddy 是由 Light Code Labs 创建的，并且是开源的，它以类似于 nginx 的方式使用服务器块，只是在这种情况下，所有的配置都可以在一个名为 Caddyfile 的文件中完成，一旦它被指定为域名的一部分，它就会实现 https(强调 s)。

它非常轻巧，而且有很多非常好的相关文档。既然我已经能够说服你(希望如此)，让我们看看如何安装和设置 caddy。

# 设置球童

首先，你可以通过访问网站([https://caddyserver.com/download](https://caddyserver.com/download))下载 caddy，安装非常简单，很容易上手，大部分都在他们的文档中有详细说明，如果你正在寻找一个 docker 容器，你可能会有兴趣看看这个 docker 图片。

如果您下载了 zip 文件，请按照以下说明进行操作。

首先，将 caddy 二进制文件放在系统范围的二进制文件目录中，并赋予它适当的所有权和权限:

```
 sudo cp /path/to/caddy /usr/local/bin
    sudo chown root:root /usr/local/bin/caddy
    sudo chmod 755 /usr/local/bin/caddy 
```

Enter fullscreen mode Exit fullscreen mode

给予 caddy 二进制文件作为非根用户绑定到特权端口(例如 80、443)的能力:

```
 sudo setcap 'cap_net_bind_service=+ep' /usr/local/bin/caddy 
```

Enter fullscreen mode Exit fullscreen mode

设置需要的用户、组和目录:

```
 sudo groupadd -g 33 www-data
    sudo useradd \
      -g www-data --no-user-group \
      --home-dir /var/www --no-create-home \
      --shell /usr/sbin/nologin \
      --system --uid 33 www-data

    sudo mkdir /etc/caddy
    sudo chown -R root:www-data /etc/caddy
    sudo mkdir /etc/ssl/caddy
    sudo chown -R root:www-data /etc/ssl/caddy
    sudo chmod 0770 /etc/ssl/caddy 
```

Enter fullscreen mode Exit fullscreen mode

将您的 caddy 配置放在适当的目录中，并赋予它适当的所有权和权限:

```
 sudo cp /path/to/Caddyfile /etc/caddy/
    sudo chown www-data:www-data /etc/caddy/Caddyfile
    sudo chmod 444 /etc/caddy/Caddyfile 
```

Enter fullscreen mode Exit fullscreen mode

为服务器创建主目录，并赋予它适当的所有权
和权限:

```
 sudo mkdir /var/www
    sudo chown www-data:www-data /var/www
    sudo chmod 555 /var/www 
```

Enter fullscreen mode Exit fullscreen mode

让我们假设你的网站内容在一个名为“example.com”的目录中。
把你的网站放好，让球童为你服务:

```
 sudo cp -R example.com /var/www/
    sudo chown -R www-data:www-data /var/www/example.com
    sudo chmod -R 555 /var/www/example.com 
```

Enter fullscreen mode Exit fullscreen mode

您需要显式地配置 caddy 来从这个位置为站点提供服务，如果您还没有这样做的话，可以通过将
添加到 Caddyfile 中:

```
 example.com {
        root /var/www/example.com
        ...
    } 
```

Enter fullscreen mode Exit fullscreen mode

安装 systemd 服务单元配置文件，重新加载 systemd 守护进程，
并启动 caddy:

```
 wget https://raw.githubusercontent.com/mholt/caddy/master/dist/init/linux-systemd/caddy.service
    sudo cp caddy.service /etc/systemd/system/
    sudo chown root:root /etc/systemd/system/caddy.service
    sudo chmod 644 /etc/systemd/system/caddy.service
    sudo systemctl daemon-reload
    sudo systemctl start caddy.service 
```

Enter fullscreen mode Exit fullscreen mode

如果你愿意，让球童服务在开机时自动启动:

```
 sudo systemctl enable caddy.service 
```

Enter fullscreen mode Exit fullscreen mode

如果 caddy 似乎无法正常启动，您可以查看日志数据来帮助找出问题所在:

```
 journalctl --boot -u caddy.service 
```

Enter fullscreen mode Exit fullscreen mode

使用 Caddyfile 中的`log stdout`和`errors stderr`来充分利用 systemd 日志。

如果你的 GNU/Linux 发行版没有将*日志*和*系统*一起使用，那么检查`/var/log`中的任何日志文件。

如果你想跟踪 caddy 的最新日志，你可以这样做:

```
 journalctl -f -u caddy.service 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用以下命令让`www-data`用户访问其他证书和私钥文件:

```
 setfacl -m user:www-data:r-- /etc/ssl/private/my.key 
```

Enter fullscreen mode Exit fullscreen mode

# 球童文件

在设置的时候，你可能已经瞥见了 caddyfile，现在让我们来看看它，我们如何设置一个服务器块，就这么简单。

```
 https://mydopesite.com {
      root /path/to/site_files
    } 
```

Enter fullscreen mode Exit fullscreen mode

瞧，就这么简单。让我们把它分解一下，这样我们就能更好地理解它(因为它太难了)

## 站点地址

HTTP 服务器使用站点地址作为标签。地址以`scheme` `://` `host` `:` `port` `/` `path`的形式指定，其中除了一个以外都是可选的。

主机部分通常是本地主机或域名。默认端口是 2015(除非该站点符合自动 HTTPS 的条件，在这种情况下，它会为您更改为 443)。scheme 部分是指定端口的另一种方式。有效的方案是“http”或“https ”,分别代表端口 80 和 443。如果同时指定了方案和端口，则端口优先。例如(此表假设自动 HTTPS 适用于符合条件的情况):

```
 :2015                    # Host: (any), Port: 2015
    localhost                # Host: localhost; Port: 2015
    localhost:8080           # Host: localhost; Port: 8080
    example.com              # Host: example.com; Ports: 80->443
    http://example.com       # Host: example.com; Port: 80
    https://example.com      # Host: example.com; Ports: 80->443
    http://example.com:1234  # Host: example.com; Port: 1234 
```

Enter fullscreen mode Exit fullscreen mode

主机名中可以使用通配符`*`。通配符必须代替整个域标签:`*.example.com`有效，但`foo*.example.com`无效。

## 路径匹配。

一些指令接受指定要匹配的基路径的参数。基本路径是一个前缀。如果 URL 以基本路径开头，它将是匹配的。例如，`/foo`的基本路径将把请求匹配到`/foo`、`/foo.html`、`/foobar`和`/foo/bar.html`。

## 指令

大多数指令调用中间件层。中间件是应用程序中的一个小层，它处理 HTTP 请求，并且很好地完成一件事情。中间件在启动时被链接在一起(如果你愿意，可以说是预编译的)。只有从 Caddyfile 调用的中间件处理程序才会被链接进来，所以小 caddy file 非常快速有效。

参数的语法因指令而异。有些需要参数，有些不需要。请查阅每个指令的文档以获取它们的签名。

## 占位符

在某些情况下，指令将接受占位符(可替换值)。这些词用花括号`{ }`括起来，在请求时由 HTTP 服务器解释。比如`{query}`或者`{>Referer}`。把它们想象成变量。这些占位符与您可以在 Caddyfiles 中使用的环境变量没有关系，除了为了熟悉起见我们借用了语法。

如果你想更多地了解这方面的知识，花些时间阅读这些文档会很棒。(点击此处进入)

## caddy 中的代理和负载均衡

在 caddy 中完成代理操作就像设置代理指令一样简单。这个中间件添加了一个可以在日志格式中使用的占位符:{upstream} -请求被代理到的上游主机的名称。

```
 https://mydomain.here{
      // usual structure is (proxy from to)
      proxy /api localhost:9005
    } 
```

Enter fullscreen mode Exit fullscreen mode

上面的代理将所有/api 路由重定向到 localhost:9005(机器的 localhost)。
您可以使用代理中的策略选项，根据与 nginx 算法相同的概念来设置代理算法。

```
 proxy / web1.local:80 web2.local:90 web3.local:100 {
            policy round_robin
    } 
```

Enter fullscreen mode Exit fullscreen mode

以下是与负载平衡相关的策略

**策略**
有几种负载平衡策略可用:

*   随机(默认)-随机选择一个后端
*   least_conn -选择活动连接最少的后端
*   round_robin -以循环方式选择后端
*   首先——按照 Caddyfile 中定义的顺序选择第一个可用的后端
*   ip _ hash——通过散列请求 IP 来选择后端，根据后端的总数均匀分布在散列空间中
*   uri _ hash——通过散列请求 uri 来选择后端，根据后端的总数均匀分布在散列空间中
*   header -通过散列给定头的值来选择，该值由策略名称后的[value]指定，根据后端的总数均匀分布在散列空间中

# 结论

现在很明显，我确实喜欢使用 caddy，这是一个简单的工具，可以很好地完成工作，web 服务器有很多选择，但是这个服务器带来的好处是非常令人兴奋和值得的。

如果你想了解更多，请在 https://caddyserver.com 查看这个项目。