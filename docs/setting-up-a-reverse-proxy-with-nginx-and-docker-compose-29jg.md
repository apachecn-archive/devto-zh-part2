# 用 Nginx 和 docker-compose 设置反向代理

> 原文：<https://dev.to/domysee/setting-up-a-reverse-proxy-with-nginx-and-docker-compose-29jg>

Nginx 是一款很棒的软件，它允许您轻松地将应用程序包装在反向代理中，然后可以处理与服务器相关的方面，如 SSL 和缓存，对其背后的应用程序完全透明。

这是从我的[个人网站](http://www.domysee.com/blogposts/blogpost%2020%20-%20setting%20up%20a%20reverse-proxy%20with%20nginx%20and%20docker-compose/)发来的交叉帖子。

## 简介

web 应用程序的某些方面，如 SSL 加密、请求缓存和服务发现，可以在应用程序本身之外进行管理。像 Nginx 这样的反向代理可以处理许多这样的责任，所以我们作为开发人员不必在我们的软件中考虑它。

此外，有些软件并不意味着可以在互联网上使用，因为它们没有适当的安全措施。很多数据库都是这样的。一般来说，不公开内部服务是一种很好的做法，因为这并不是必须的。

所有这些都可以通过 docker-compose 和 Nginx 来实现。

## 码头工-化合物

docker-compose 是一个简洁的小工具，让你定义一系列应该同时启动的 docker 容器，以及它们应该启动的配置。这包括导出的端口、它们所属的网络、映射到它的卷、环境变量以及可以用`docker run`命令配置的所有其他内容。

在这一节中，我将简要解释如何配置本文中使用的 docker-compose 特性。要了解更多细节，请查看[文档](https://docs.docker.com/compose/compose-file/)。

主入口点是一个`docker-compose.yml`文件。它配置应该一起启动的容器的所有方面。

这里有一个例子`docker-compose.yml` :

```
version: '3'
services:
  nginx: 
    image: nginx:latest
    container_name: production_nginx
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
    ports:
      - 80:80
      - 443:443

  ismydependencysafe:
    image: ismydependencysafe:latest
    container_name: production_ismydependencysafe
    expose:
      - "80" 
```

如您所见，指定了两个图像。
T5 首`nginx`，以名`production_nginx`。它指定了一个替换默认 Nginx 配置文件的卷。还定义了主机端口 80 和 443 到容器端口 80 和 443 的映射。

第二个形象是一个是我自己创造的一个。它暴露端口 80。与`ports`配置的不同之处在于它们没有发布到主机上。这就是为什么它也可以指定端口 80，即使`nginx`已经指定了。

本文中还使用了其他一些配置选项，特别是网络、卷和环境变量。

### 网络

有了网络，就有可能指定哪些容器可以相互通信。它们被指定为新的根配置条目和容器配置。

```
version: '3'
services:
  nginx:
    ...
    networks:
      - my-network-name

  ismydependencysafe:
    ...
    networks:
      - my-network-name

networks:
  my-network-name: 
```

在根对象`networks`中，定义了网络`my-network-name`。通过将每个集装箱添加到`network`列表中，将其分配给该网络。

如果未指定网络，则所有容器都在默认创建的同一网络中。因此，如果只使用一个网络，则根本不需要指定网络。

网络的一个便利特性是同一网络中的容器可以通过名称相互引用。在上面的例子中，url `http://ismydependencysafe`将解析为容器`ismydependencysafe`。

### 卷

卷定义 docker 容器的持久存储。如果应用程序在没有定义卷的地方写入，当容器停止时，数据将会丢失。

有两种类型的卷。一种是将文件或目录映射到容器中的一个文件或目录，另一种只是使文件或目录持久化(命名卷)，而不使它们在文件系统上可访问(当然它们在某个地方*，但这是 docker 实现特有的，不应该干预)。*

 *第一种类型是将特定文件或目录映射到容器中的卷，我们已经在上面的示例中看到过。这又是一个例子，增加了一个卷，也以同样的方式指定了一个目录:

```
version: '3'
services:
  nginx: 
    image: nginx:latest
    container_name: production_nginx
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
      - /etc/letsencrypt/:/etc/letsencrypt/
... 
```

命名卷与网络类似，作为单独的根配置条目直接在容器配置中指定。

```
version: '3'
services:
  nginx:
    ...
    volumes:
      - "certificates:/etc/letsencrypt/"

    ...

volumes:
  certificates:
... 
```

### 环境变量

Docker 还可以为容器中的应用程序指定环境变量。在 compose 配置中，有多种方法可以做到这一点，要么指定一个包含它们的文件，要么直接在`docker-compose.yml`中声明它们。

```
version: '3'
services:
  nginx:
    ...
    env_file:
      - ./common.env
    environment:
      - ENV=development
      - APPLICATION_URL=http://ismydependencysafe
    ... 
```

如你所见，这两种方法也可以同时使用。请注意，`environment`中设置的变量会覆盖从文件中加载的变量。

环境文件的格式必须是`VAR=VAL`，每行一个变量。

```
ENV=production
APPLICATION_URL=http://ismydependencysafe 
```

### CLI

启动和停止容器的命令非常简单。

开始使用`docker-compose up -d`。
T3`-d`指定应该在后台启动。如果没有它，容器将在命令行关闭时停止。

停止使用`docker-compose down`。

这两个命令都在当前目录中查找一个`docker-compose.yml`文件。如果是别的地方，用`-f path/to/docker-compose.yml`指定。

现在 docker-compose 的基础已经很清楚了，让我们继续看 Nginx。

## Nginx

Nginx 是一个具有广泛特性的 web 服务器，包括反向代理，这正是本文中使用它的目的。
T3 它配置了一个`nginx.conf`。默认情况下，它在`/etc/nginx/nginx.conf`中查找它，但是当然也可以指定另一个文件。

作为一个反向代理，它可以透明地处理 web 应用程序的两个非常重要的方面，加密和缓存。但是在详细讨论之前，让我们看看反向代理特性本身是如何配置的:

```
http {
  server {
    server_name your.server.url;

    location /yourService1 {
      proxy_pass http://localhost:80;
      rewrite ^/yourService1(.*)$ $1 break;
    }

    location /yourService2 {
      proxy_pass http://localhost:5000;
      rewrite ^/yourService1(.*)$ $1 break;
    }
  }

  server {
    server_name another.server.url;

    location /yourService1 {
      proxy_pass http://localhost:80;
      rewrite ^/yourService1(.*)$ $1 break;
    }

    location /yourService3 {
      proxy_pass http://localhost:5001;
      rewrite ^/yourService1(.*)$ $1 break;
    }
  }
} 
```

Nginx 配置是在**上下文**中组织的，上下文定义了它们处理的流量类型。`http`上下文(显然)正在处理 http 流量。其他上下文是`mail`和`stream`。

`server`配置指定了一个虚拟服务器，每个虚拟服务器都有自己的规则。`server_name`指令定义了虚拟服务器响应的 URL 或 IP 地址。

`location`配置定义了传入流量的路由位置。根据 url，请求可以被传递到一个或另一个服务。在上面的配置中，路由的起点指定了服务。

`proxy_pass`设置新的 url，使用`rewrite`重写 url 以适应服务。在这种情况下，从 url 中删除了`yourService{x}`。

这是一个概述，后面的部分将解释如何配置缓存和 SSL。

更多细节，请查看[文档](https://docs.nginx.com/nginx/admin-guide/web-server/reverse-proxy/)。

既然我们知道了这些部分，让我们开始把它们组合起来。

## 设置 Nginx 为 Docker 内部的反向代理

对于基本设置，只需要 3 样东西:

1)主机端口到容器端口的映射
2)在`/etc/nginx/nginx.conf`
将配置文件映射到默认的 Nginx 配置文件 3)Nginx 配置

在 docker-compose 文件中，端口映射可以用`ports` config 条目来完成，正如我们在上面看到的。

```
 ...
    ports:
      - 80:80
      - 443:443
    ... 
```

Nginx 配置的映射是用一个卷完成的，我们之前也看到过:

```
 ...
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
    ... 
```

Nginx 配置被认为与`docker-compse.yml` ( `./nginx.conf`)在同一个目录中，但是它当然可以在任何地方。

### 缓存配置

将缓存添加到设置中非常容易，只需更改 Nginx 配置。

在`http`上下文中，添加一个`proxy_cache_path`指令，定义缓存内容的本地文件系统路径以及内存区域的名称和大小。

请记住，路径是在容器内的*，而不是在主机的文件系统上。* 

```
http {
    ...
    proxy_cache_path /data/nginx/cache keys_zone=one:10m;
} 
```

在响应应该被缓存的`server`或`location`上下文中，添加一个指定内存区域的`proxy_cache`指令。

```
 ...
  server {
    proxy_cache one;
  ... 
```

这足以用默认的缓存配置来定义缓存。还有许多其他指令更详细地指定缓存哪些响应。关于这些的更多细节，请看一下[文档](https://docs.nginx.com/nginx/admin-guide/content-cache/content-caching/#specifying-which-requests-to-cache)。

## 使用 SSL 保护 HTTP 流量

到现在为止，服务器设置已经完成。docker-compose 启动所有容器，Nginx 容器充当服务的反向代理。正如[这个](https://doesmysiteneedhttps.com)网站如此漂亮地解释的那样，只剩下一件事要做了，加密。

要安装从 Let's Encrypt 获取证书的客户端 certbot，请遵循[安装说明](https://certbot.eff.org)。

### 用 certbot 生成 SSL 证书

certbot 有多种方法可以获得 SSL 证书。有广泛的网络服务器插件，如 Apache 和 Nginx，一个使用独立的网络服务器来验证域，当然还有一种手动方式。

我们将使用`standalone`插件。它为证书质询启动一个单独的 web 服务器，这意味着端口 80 或 443 必须可用。为此，Nginx 服务器必须关闭，因为它绑定到两个端口，并且 certbot 服务器需要能够接受至少其中一个端口上的入站连接。

要创建证书，请执行

```
certbot --standalone -d your.server.url 
```

并按照说明进行操作。您还可以通过添加更多的`-d`参数，例如`-d your.server1.url` `-d your.server2.url`，一次为多个 URL 创建一个证书。

### 自动更新证书

让我们加密 CA 颁发短期证书，它们的有效期只有 90 天。这使得更新过程的自动化变得非常重要。值得庆幸的是，certbot 通过命令 [`certbot renew`](https://certbot.eff.org/docs/using.html#renewing-certificates) 让这一切变得简单。它检查所有已安装的证书，并续订将在 30 天内到期的证书。

它将使用与最初获得证书时相同的插件进行续订。在我们的例子中，这就是`standalone`插件。

质询过程是相同的，因此对于续订，端口 80 或 443 也必须是空闲的。certbot 提供了 pre 和 post 挂钩，我们使用它们在更新期间停止和启动 web 服务器，以释放端口。

钩子只在证书需要更新的时候执行，所以不会有不必要的服务停机。

因为我们使用的是`docker-compose`，所以整个命令看起来像这样:

```
certbot renew --pre-hook "docker-compose -f path/to/docker-compose.yml down" --post-hook "docker-compose -f path/to/docker-compose.yml up -d" 
```

要完成自动化，只需将前面的命令添加为 cronjob。

用`crontab -e`打开 cron 文件。

在那里用
添加新的一行

```
@daily certbot renew --pre-hook "docker-compose -f path/to/docker-compose.yml down" --post-hook "docker-compose -f path/to/docker-compose.yml up -d" 
```

就是这样。现在，renew 命令每天都会执行，您不必担心证书的到期日期。

### 使用 Nginx Docker 容器中的证书

到目前为止，证书已经被请求并存储在服务器上，但是我们还没有使用它们。为了实现这一目标，我们必须

1)使证书对 Nginx 容器可用，并
2)更改配置以使用它们

要使证书对 Nginx 容器可用，只需将整个`letsencrypt`目录指定为其上的一个卷。

```
 ...
  nginx: 
    image: nginx:latest
    container_name: production_nginx
    volumes:
      - /etc/letsencrypt/:/etc/letsencrypt/
  ... 
```

修改配置并使其安全需要更多的工作。
默认情况下，虚拟服务器监听端口 80，但使用 SSL 时，它还应该监听端口 443。这必须由 2 个`listen`指令指定。

另外，证书必须定义。这是通过`ssl_certificate`和`ssl_certificate_key`指令完成的。

```
 ...
  server {
    ...
    listen 80;
    listen 443 ssl;
    ssl_certificate /etc/letsencrypt/live/your.server.url/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/your.server.url/privkey.pem;
  }
  ... 
```

这些小变化足以为 SSL 配置 nginx。虽然它使用 Nginx 的默认 SSL 设置，这是可以的，但还可以改进。

### 提高 Nginx 配置的安全性

在本节的开始，我应该提到，如果您使用最新版本的 nginx，它的默认 SSL 设置是安全的。不需要定义协议、密码和其他参数。

也就是说，有几个 SSL 指令可以让我们进一步提高安全性。只要记住，通过设置这些，你自己有责任保持它们是最新的。Nginx 对默认配置设置的更改不会影响你，因为你正在覆盖它们。

首先，设置

```
ssl_protocols TLSv1.1 TLSv1.2; 
```

这将禁用所有被认为不安全的 SSL 协议和 tlsv 1.0([tlsv 1.0](https://www.netsparker.com/web-vulnerability-scanner/vulnerabilities/insecure-transportation-security-protocol-supported-tls-10/)、 [SSLv3](https://www.netsparker.com/web-vulnerability-scanner/vulnerabilities/insecure-transportation-security-protocol-supported-sslv3/) 、 [SSLv2](https://www.netsparker.com/web-vulnerability-scanner/vulnerabilities/insecure-transportation-security-protocol-supported-sslv2/) )。在撰写本文时(2018 年 7 月)，TLSv1.1 和 TLSv1.2 被认为是安全的，但没有人能保证它们在未来不会被破解。

接下来，设置

```
ssl_prefer_server_ciphers on;
ssl_ciphers ECDH+AESGCM:ECDH+AES256:ECDH+AES128:DHE+AES128:!ADH:!AECDH:!MD5; 
```

密码定义了加密是如何完成的。那些值是从本文的[中复制的，因为我不是这方面的专家。](https://bjornjohansen.no/optimizing-https-nginx)

这些是最重要的设置。要进一步提高安全性，请遵循以下文章:

*   [在 Nginx 上优化 HTTPS](https://bjornjohansen.no/optimizing-https-nginx)
*   [如何在 Ubuntu 18.04 上设置 Let ' s Encrypt for Nginx](https://gist.github.com/cecilemuller/a26737699a7e70a7093d4dc115915de8#stronger-settings-for-a)

你可以通过 SSL 实验室提供的一个很棒的网站[来检查你的 SSL 配置的安全性。](https://www.ssllabs.com/ssltest/analyze.html)

## 总结起来

在本文中，我们介绍了如何设置 docker-compose，如何使用它的网络和卷特性，如何设置环境变量，如何使用 Nginx 作为反向代理，包括缓存和 SSL 安全。主持一个项目所需的一切。

请记住，这不是一个非常专业的设置，任何重要的服务都需要一个更复杂的设置，但对于小项目或辅助项目来说，这完全没问题。

## 修正

下面是生成的`nginx.conf`和`docker-compose.yml`文件。它们包括应用程序的占位符名称、URL 和路径。

**码头-化合物. yml**

```
version: '3'
services:
  nginx: 
    image: nginx:latest
    container_name: production_nginx
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
      - ./nginx/error.log:/etc/nginx/error_log.log
      - ./nginx/cache/:/etc/nginx/cache
      - /etc/letsencrypt/:/etc/letsencrypt/
    ports:
      - 80:80
      - 443:443

  your_app_1:
    image: your_app_1_image:latest
    container_name: your_app_1
    expose:
      - "80"

  your_app_2:
    image: your_app_2_image:latest
    container_name: your_app_2
    expose:
      - "80"

  your_app_3:
    image: your_app_3_image:latest
    container_name: your_app_3
    expose:
      - "80" 
```

**engine . conf**

```
events {

}

http {
  error_log /etc/nginx/error_log.log warn;
  client_max_body_size 20m;

  proxy_cache_path /etc/nginx/cache keys_zone=one:500m max_size=1000m;

  server {
    server_name server1.your.domain;

    location /your_app_1 {
      proxy_pass http://your_app_1:80;
      rewrite ^/your_app_1(.*)$ $1 break;
    }

    location /your_app_2 {
      proxy_pass http://your_app_2:80;
      rewrite ^/your_app_2(.*)$ $1 break;
    }
  }

  server {
    server_name server2.your.domain;
    proxy_cache one;
    proxy_cache_key $request_method$request_uri;
    proxy_cache_min_uses 1;
    proxy_cache_methods GET;
    proxy_cache_valid 200 1y;

    location / {
      proxy_pass http://your_app_3:80;
      rewrite ^/your_app_3(.*)$ $1 break;
    }

    listen 80;
    listen 443 ssl;
    ssl_certificate /etc/letsencrypt/live/server2.your.domain/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/server2.your.domain/privkey.pem;
    include /etc/letsencrypt/options-ssl-nginx.conf;
  }
} 
```

* * *

在 Twitter 上关注我，了解更多我的想法、文章、项目和工作。*