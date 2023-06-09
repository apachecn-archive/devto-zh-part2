# 如何在运行 Ubuntu 16.04 的 Nginx 上安装 Let's Encrypt

> 原文：<https://dev.to/erikthiart/how-to-install-lets-encrypt-on-nginx-running-ubuntu-1604-3jl1>

安装

```
$ sudo apt-get update
$ sudo apt-get install software-properties-common
$ sudo add-apt-repository ppa:certbot/certbot
$ sudo apt-get update
$ sudo apt-get install python-certbot-nginx 
```

Enter fullscreen mode Exit fullscreen mode

入门
Certbot 有 Nginx 插件，很多平台都支持，还有证书安装。

```
$ sudo certbot --nginx 
```

Enter fullscreen mode Exit fullscreen mode

如果您感觉更保守，并且想要手工修改 Nginx 配置，那么您可以使用 certonly 子命令:

```
$ sudo certbot --nginx certonly 
```

Enter fullscreen mode Exit fullscreen mode

自动更新
您系统上的 Certbot 软件包附带了一个 cron 作业，它会在您的证书过期之前自动更新您的证书。因为让我们加密持续 90 天的证书，所以强烈建议利用这个特性。您可以通过运行以下命令来测试证书的自动续订:

```
$ sudo certbot renew --dry-run 
```

Enter fullscreen mode Exit fullscreen mode