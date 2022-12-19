# 设置流量

> 原文：<https://dev.to/sirech/setting-up-traefik-3pao>

最近，我为一个应用建立了一个新的基于云的基础设施。作为它的一部分，我们有一些关于负载平衡、SSL 等等的需求。作为其中的一部分，我有机会评估了 Traefik。这是一个相当酷的工具，作为一个反向代理(以及更多)。

现在，这听起来就像使用 [nginx](https://www.nginx.com/) 一样，对吗？这里没有什么新东西。不过， *Traefik* 是用 [Docker](https://www.docker.com/) 和云来打造的，而且相当轻量，值得探索。

## 拼凑

我在 Github 中建立了一个[存储库，并做了一个简单实用的演示。如果您正在使用](https://github.com/sirech/traefik-test) [docker-compose](https://docs.docker.com/compose/) 将一堆容器绑定在一起，它基本上已经准备好了！

通过这种最简单的设置，您可以:

*   不同容器之间的负载平衡
*   SSL 终端
*   自动发现
*   按指定路线发送
*   如果你需要的话，甚至还有 MTL

它只需相当少的配置工作就能产生巨大的影响。

## 与 nginx 对比

如果您检查[配置文件](https://github.com/sirech/traefik-test/blob/master/traefik/traefik.toml)，您会看到服务应用程序的基本设置。作为比较，这里有一个配置文件，用于通过 https:
在 *nginx* 中服务一个静态应用

```
server {
    listen 80;
    server_name ${HOST};
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;

    server_name ${HOST} www.${HOST};

    root /srv/main;

    ssl_certificate /cert/live/${HOST}/fullchain.pem;
    ssl_certificate_key /cert/live/${HOST}/privkey.pem;
    ssl_trusted_certificate /cert/live/${HOST}/chain.pem;

    location / {
        rewrite ^([^.\?]*[^/])$ $1/ permanent;
        try_files $uri $uri/ $uri/index.html =404;

        gzip_static on;
        expires off;
        add_header Cache-Control "no-store";
    }

    location ^~ /static/ {
        gzip_static on;
        expires max;
        add_header Cache-Control public;
    }
} 
```

现在需要注意的是 *nginx* 是一个真正的网络服务器，而`traefik`不是。所以这个例子实际上是为资产服务的。即使这样，你也可以争辩说设置是非常相似的，那么为什么不保持现有的呢？`traefik`有两个功能是我非常喜欢的。

### 仪表盘

给你一个非常好的仪表板，你可以看到你的服务之间的实际联系。以下是截图:

[![Traefik's Dashboard](../Images/42a69c2f65fefdcad7b1f2a9ccb65c78.png "Traefik's Dashboard")T2】](///static/14e6f619c5778085e8d2df0dfc66073a/2adcb/traefik.png)

这是一个小细节，但我真的喜欢对我的路线状态有一个直观的概述。当您还在设置服务时，这在开始时非常有用。当我遇到问题时，我总是发现 *nginx* 特别难以调试。

但是还有一个更大的好处…

### 发现

如果您检查了配置，您可能会注意到那里没有配置任何服务。这在我看来是`traefik`的杀手级特点。它可以连接到您指定的提供者，在本例中是 [docker](https://docs.traefik.io/configuration/backends/docker/) ，并自动动态地获取所有正在运行的容器。告别痛苦地创建新配置并将其添加到您的 *nginx* 配置中，以及之后所需的重启。

你唯一需要做的就是在每次启动新服务时添加一些配置，让`traefik`知道你希望它如何实现。这真的不容易:

```
labels:
  - "traefik.backend=echo"
  - "traefik.frontend.entryPoints=https"
  - "traefik.frontend.rule=Host:echo.testing.com;Path:/standard" 
```

通过这个简单的代码片段，我们让`traefik`知道了我们的新服务，并设置了一个入口点和到达它的路由。就是这样。你不需要做任何其他事情。简单真的让人耳目一新。在大多数情况下，我觉得你不需要比这更多。

## 待定的想法

实际上，要检查的东西更多。`traefik`有很多路由选择的可能性，尽管没有其他反向代理那么全面。我们一直在考虑做一些奇特的头部检查，以路由到共享相同 url 的不同测试系统，当我有更具体的东西时，我可能会更详细地谈论它。

另一个我很想尝试的特性是通过 [LetsEncrypt](https://letsencrypt.org/) 的[自动证书配置](https://docs.traefik.io/configuration/acme/)。LetsEncrypt 是一个救生员，为你的域名设置 https 非常方便。我已经通过一些调用 [certbot docker 容器](https://hub.docker.com/r/certbot/certbot/)的自定义脚本将它用于我的网站。然而`traefik`更进了一步，把任务完全从你手中拿走。我一有空就想跳上去。

## 从哪里开始？

我只能鼓励你先阅读入门指南，让一切运转起来。然后我会阅读关于[的基础知识](https://docs.traefik.io/basics/)，理解术语以及所有东西是如何结合在一起的。在那之后，你可以真正开始玩和尝试。我发布的[库](https://github.com/sirech/traefik-test)可以帮助你启动这个过程。