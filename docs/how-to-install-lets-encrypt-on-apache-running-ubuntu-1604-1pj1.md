# 如何在运行 Ubuntu 16.04 的 Apache 上安装 Let's Encrypt

> 原文：<https://dev.to/erikthiart/how-to-install-lets-encrypt-on-apache-running-ubuntu-1604-1pj1>

Certbot 团队在 Ubuntu 系统上安装了一个 PPA。一旦你把它添加到你的库列表中，你需要做的就是获取下面的包。

```
$ sudo apt-get update
$ sudo apt-get install software-properties-common
$ sudo add-apt-repository ppa:certbot/certbot
$ sudo apt-get update
$ sudo apt-get install python-certbot-apache 
```

Enter fullscreen mode Exit fullscreen mode

Certbot 有一个相当可靠的测试版 Apache 插件，它在许多平台上都得到支持，并且可以自动安装证书。

```
$ sudo certbot --apache 
```

Enter fullscreen mode Exit fullscreen mode

运行这个命令将为您获得一个证书，并让 Certbot 自动编辑您的 Apache 配置以服务于它。如果您感觉更保守，并且想手工修改 Apache 配置，那么可以使用 certonly 子命令:

```
$ sudo certbot --apache certonly 
```

Enter fullscreen mode Exit fullscreen mode

自动更新
您系统上的 Certbot 软件包附带了一个 cron 作业，它会在您的证书过期之前自动更新您的证书。因为让我们加密持续 90 天的证书，所以强烈建议利用这个特性。您可以通过运行以下命令来测试证书的自动续订:

```
$ sudo certbot renew --dry-run 
```

Enter fullscreen mode Exit fullscreen mode