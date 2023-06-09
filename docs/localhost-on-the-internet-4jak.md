# 互联网上的本地主机

> 原文：<https://dev.to/root3d/localhost-on-the-internet-4jak>

# 你在互联网上的本地主机，也就是不受限制地启用 SSL。

## 简介

有大量的服务可以让你在互联网上公开你的本地主机。我们需要它来测试第三方 API 或展示当前开发服务器的外壳。例如，最受欢迎的有:

1.  [ngrok](https://ngrok.com/)
2.  [局部隧道](https://localtunnel.github.io/www/)
3.  [pagekite](https://pagekite.net/)

那么，为什么要重新发明轮子呢？因为你必须追根溯源，理解简单的复杂性。没什么大不了的。

## 先决条件

那么我们都需要什么呢？

1.  一台服务器(我将使用$5 [做](https://www.digitalocean.com/)服务器)
2.  一个领域
3.  好奇心和耐心

### 这种设置有什么好处？你可能会问。

*   总自定义域和尽可能多的领域你想要的。
*   使用没有限制
*   超过其他服务的付费计划

> 我们开始吧，好吗？

这只是一个 4 步设置

1.  在服务器上使用 nginx 设置监听端口，将其与子域名相关联。
2.  在 DNS 中添加子域(我们将使用令人敬畏的数字海洋 DNS)
3.  添加我们最喜欢的[提供的 SSL 证书让我们加密](https://letsencrypt.org/)
4.  使用惊人的 SSH 命令转发流量。

## 步骤 1 -使用 nginx 配置服务器，为子域提供流量服务

请先在服务器上安装`nginx`。
假设你使用的是 Ubutnu，你需要在`/etc/nginx`的`sites-available`文件夹中添加一个 nginx 的配置文件。nginx 配置文件如下所示。

```
map $http_upgrade $connection_upgrade {
    default upgrade;
    ''      close;
}

server {
    server_name 42.igauravsehrawat.com;

    location / {
        proxy_pass http://localhost:4242;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $http_host;
        proxy_set_header X-NginX-Proxy true;

        # Enables WS support
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection $connection_upgrade;
    }
}

server {
    server_name www.42.igauravsehrawat.com;

    location / {
        proxy_pass http://localhost:4242;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $http_host;
        proxy_set_header X-NginX-Proxy true;

        # Enables WS support
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection $connection_upgrade;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们来看看配置。

1.  有`server_name`指令告诉哪个域服务于请求，`listen`指令告诉哪个端口监听域。
2.  这里的`location`指令很重要，这里我们只是用端口`4242`代理来自`localhost`的请求。此端口很重要，因为我们将把本地主机流量定向到它。然后是请求上的设置头。
3.  指令的其余部分是通过升级连接来启用此设置上的 websocket 支持。仅此而已。

注意:您需要根据您需要的子域更改`server_name`，而不是`42.igauravsehrawat.com`。

## 步骤 2 -在 DNS 上配置子域

使用[数字海洋 DNS 服务](https://www.digitalocean.com/docs/networking/dns/how-to/manage-records/)，我们将添加一个我们选择的子域，如步骤 1 中所做的，它是 42.igauravsehrawat.com

为了添加子域，我们将创建 3 个记录(A 记录、AAAA 记录、CNAME 记录)
A 和 AAAA 记录将重定向到我们的数字海洋服务器，如下所示。
[![A & AAAA records](img/d7db494267a05b5dfd7d9abd77a0b2ae.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xmI64WWu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://preview.ibb.co/iavxYo/Screen_Shot_2018_07_28_at_21_02_25.png)

CNAME 只是我们 A/AAAA 记录的别名。三者都会出现如下图
[![All three](img/dbaeee1bcc9e0568ba278ef8f23aeda0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8OSPA732--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://preview.ibb.co/ey6hzT/Screen_Shot_2018_07_28_at_21_03_03.png)

这就是全部内容，您应该能够通过互联网访问您的子域，向您显示`502 Bad Gateway`页面，这是意料之中的，因为没有任何内容被转发到端口`4242`。

## [T1。步骤 3 -启用 SSL](#-step-3-enabling-ssl)

启用 SSL 是小菜一碟，比这更容易。从[到](https://certbot.eff.org/lets-encrypt/ubuntuxenial-nginx)遵循`certbot`安装设置

然后为 nginx
`sudo certbot --nginx`运行魔术命令

你会看到类似下面的
[![certbot-run](img/daf69cb0c9889ec3e9c05cc60c15eeb4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--daglTDk7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://preview.ibb.co/jgKVm8/Screen_Shot_2018_07_28_at_22_17_52.png)

运行两次，一次用于子域，一次用于 CNAME(别名)。

现在，当您转到您的子域[www.42.igauravsehrawat.com](http://www.42.igauravsehrawat.com)
[![expected-bad-gateway](img/bf23874fe1949b6429d6377ecd4d4b1d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vHMpaAOJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://preview.ibb.co/jxi9Do/Screen_Shot_2018_07_28_at_22_29_50.png)
时，您应该会看到这种情况，因为在服务器的 localhost 的端口`4242`上没有运行任何东西。

汪汪，我们离快乐的舞蹈只有一步之遥。

## 第四步- SSH 魔法

只需一个命令就能统治所有人

`ssh -N -R localhost:4242:localhost:3000 root@42.igauravsehrawat.com`

-N 告诉“不要执行远程命令。这对于转发端口非常有用。
-R 告知“到远程(服务器)主机上给定 TCP 端口或 Unix 套接字的连接将被转发到本地端。”

第一个 localhost 是服务器的，第二个是本地机器的，然后是服务器的地址。如果你设置了 SSH 密钥(推荐)，它将是无缝的，没有密码，什么都没有。

就是这样。

快乐的舞蹈如约而至。

[![happy-dance](img/3cb914791c9a4229b286520ca3d4743a.png)T2】](https://i.giphy.com/media/10UtqJNULHPfxe/giphy.gif)

> 等等，我们还没测试呢。

## 测试

为了测试，我将[使用`create-react-app`创建一个 react](https://github.com/facebook/create-react-app) 应用程序，并使用`npm start`在端口 3000 上运行它(默认)。
然后运行神奇的 SSH 命令
`ssh -N -R localhost:4242:localhost:3000 root@42.igauravsehrawat.com`

看看它的实际效果

[![localhost-on-internet](img/145ab02aca8a555b9dc35be11599e8f0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jzzbhPAQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/tMX81nZ.gif)

## 结论

凭借一台服务器的能力，拥有自己的设置并不困难。你只需要有自己的自定义，SSL 启用子域，没有任何限制。