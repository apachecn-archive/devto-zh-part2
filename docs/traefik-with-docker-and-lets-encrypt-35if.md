# 用 Docker 和 Let's 加密 Traefik

> 原文：<https://dev.to/joenas/traefik-with-docker-and-lets-encrypt-35if>

[![Traefik with Docker and Let's Encrypt](img/e5b7541d4a6b17f1a05de9ffc8b726e6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xygsb0WV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jonnev.se/conteimg/2018/03/Screen-Shot-2018-03-13-at-22.19.45-1.png)

我在 Docker 中运行我的大部分服务，以前我使用`nginx`作为反向和 TLS 终止代理以及 Let's Encrypt。这非常有效，但每次我想尝试新的东西时，我都必须复制粘贴另一个配置并更改一些值。我可能可以在某种程度上实现自动化，也有其他人这样做了，但随着我最近迁移到 VPSes，我想我应该给 [Traefik](https://traefik.io) 一个尝试，如果没有其他东西是为了他们**令人敬畏的**标志的话！

在这里，我将向您展示如何使用 GUI、http 重定向和自动加密证书来设置 Traefik。我们还将把*基本身份验证*添加到 Traefik GUI 中。

## 先决条件

本指南假定您对 Linux 有一定的了解，并且您有一台安装了这些服务的服务器:

*   [码头工人](https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/)
*   [坞站-复合](https://docs.docker.com/compose/install/)
*   用于托管您的应用程序的**域**

对于价廉物美的服务器，请查看数字海洋或 T2 的网站。另外，当我建立一个新的 VPS 时，我总是推荐[这个指南](https://plusbryan.com/my-first-5-minutes-on-a-server-or-essential-security-for-linux-servers)。

*如果你想使用数字海洋，请随意使用我的[推荐链接](https://m.do.co/c/37d221e6802a)为你的服务器获得 10 美元*😊

## 设置

这是在官方指南的基础上增加了一些内容。我将向您展示如何添加 web dashboard 和 API——受基本身份验证保护——主要是因为这很有趣。如果你对它没有用处或者认为它不安全，你可以跳过这一部分。

首先，为面向 web 的容器创建一个连接网络。

```
docker network create web 
```

Enter fullscreen mode Exit fullscreen mode

然后我们创建一个目录和必要的文件，如果需要的话可以创建 sudo。

```
sudo su
mkdir -p /opt/traefik
touch /opt/traefik/docker-compose.yml
touch /opt/traefik/acme.json && chmod 600 /opt/traefik/acme.json
touch /opt/traefik/traefik.toml 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们添加我们的`docker-compose`...

```
nano /opt/traefik/docker-compose.yml 
```

Enter fullscreen mode Exit fullscreen mode

```
version: '2'

services:
  proxy:
    image: traefik:v1.7.12-alpine
    command: --configFile=/traefik.toml
    restart: unless-stopped
    networks:
      - web
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - /opt/traefik/traefik.toml:/traefik.toml
      - /opt/traefik/acme.json:/acme.json
    # REMOVE this section if you don't want the dashboard/API
    labels:
      - "traefik.enable=true"
      - "traefik.frontend.rule=Host:example.com"
      - "traefik.port=8080"

networks:
  web:
    external: true 
```

Enter fullscreen mode Exit fullscreen mode

☝️ **如果你保留 API 的话记得替换`traefik.frontend.rule`** 中的`example.com`。

...以及 Traefik 的配置...

```
nano /opt/traefik/traefik.toml 
```

Enter fullscreen mode Exit fullscreen mode

```
# Change this if needed
logLevel = "ERROR"
defaultEntryPoints = ["https","http"]

[entryPoints]
  [entryPoints.http]
    address = ":80"
    [entryPoints.http.redirect]
    entryPoint = "https"
  [entryPoints.https]
    address = ":443"
  [entryPoints.https.tls]

# REMOVE this section if you don't want the dashboard/API
[api]
entryPoint = "api"
dashboard = true

[retry]

[docker]
endpoint = "unix:///var/run/docker.sock"
domain = "mydomain"
watch = true
# I prefer to expose my containers explicitly
exposedbydefault = false

[acme]
email = "myemail"
storage = "acme.json"
entryPoint = "https"
OnHostRule = true
[acme.httpChallenge]
entryPoint = "http" 
```

Enter fullscreen mode Exit fullscreen mode

☝️ **分别在`[docker]`和`[acme]`下添加你的域名和邮箱。**

...我们应该可以出发了！

```
cd /opt/traefik/
docker-compose up -d 
```

Enter fullscreen mode Exit fullscreen mode

检查日志(`docker-compose logs`)并前往您配置的域，您应该会看到类似这样的内容(截图是几个版本之前拍摄的，已经重新设计)。

[![Screen-Shot-2018-03-13-at-22.19.45](img/2b8daf7330e4307be5d2c2e4c3bedf48.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6uR448PK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jonnev.se/conteimg/2018/03/Screen-Shot-2018-03-13-at-22.19.45.png)

### 基本认证

因为我们已经公开了 Traefik 的 API，所以我们希望进行一些身份验证。支持基本身份验证，所以让我们添加它。对您想要的用户名运行这个程序，例如`admin`，并输入您的密码。

```
sudo apt install apache2-utils
htpasswd -n username 
```

Enter fullscreen mode Exit fullscreen mode

这是我为管理员/管理员准备的。

```
admin:$apr1$IBj9Hfsd$kf7vXLpY4/9XD365jcp/n1 
```

Enter fullscreen mode Exit fullscreen mode

现在它需要进入`traefik.toml`中，但是为了工作，任何`$`符号都必须用另一个`$`转义。
将此添加到`[entrypoints]`部分...

```
 [entryPoints.api]
    address = ":8080"
    [entryPoints.api.auth]
     [entryPoints.api.auth.basic]
       users = [
         "admin:$$apr1$$IBj9Hfsd$$kf7vXLpY4/9XD365jcp/n1"
       ] 
```

Enter fullscreen mode Exit fullscreen mode

现在停止并重建您的服务...

```
docker-compose stop
docker-compose up -d 
```

Enter fullscreen mode Exit fullscreen mode

...而且你要有基本的 auth！

[![Screen-Shot-2018-03-13-at-22.30.58](img/28c7a65b28a803050e23fc94ff5b287f.png)T2】](/conteimg/2018/03/Screen-Shot-2018-03-13-at-22.30.58.png)

## 添加一个容器

当然，要使用它，我们需要一个容器！为什么不是有`ghost`的博客？

创建一个目录和一个`docker-compose.yml`，记住要更改域名并将主机名添加到您的 DNS 中！👍

```
mkdir -p /opt/ghost
nano /opt/ghost/docker-compose.yml 
```

Enter fullscreen mode Exit fullscreen mode

```
version: '2'
services:
  server:
    image: ghost:alpine
    container_name: ghost
    restart: unless-stopped
    networks:
      - web
    labels:
      # This one is important since we default to not expose
      - "traefik.enable=true"
      - "traefik.frontend.rule=Host:blog.example.com"
      - "traefik.port=2368"
      - "traefik.docker.network=web"
    volumes:
      - /opt/ghost/blog:/var/lib/ghost/content
    environment:
      - NODE_ENV=production
      - url=https://blog.example.com

networks:
  web:
    external: true 
```

Enter fullscreen mode Exit fullscreen mode

运行普通的`docker-compose up -d`和 *voilà* 博客，使用 SSL/TLS 和所有功能，纯粹是魔术😀

### 更新

2019-07-15:更新到最新版本(`v1.7.12`)，也改为只使用`traefik.toml`而不与`docker-compose.yml`中的`command`混合