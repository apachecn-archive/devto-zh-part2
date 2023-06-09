# 让我们加密 Cloudflare

> 原文：<https://dev.to/jbutz/let-s-encrypt-cloudflare-1bfb>

[让我们加密](https://letsencrypt.org/)和[证书机器人](https://certbot.eff.org/)非常棒，让 SSL 证书的设置变得简单而廉价。但是有时您的应用程序或站点并没有真正按照一种通用的模式建立起来。我经常使用的一种设置是让应用程序在我的服务器上运行，但让 Cloudflare CDN 设置在应用程序的前面。默认的[NGINX 方法][nginx-certbot]在这种情况下不起作用。

DNS 验证方法过去在 Certbot 的网站上不是很突出，所以我花了一段时间才明白，你可以非常容易地用 DNS 进行验证。我将向您展示的这种方法有一个很大的缺点:您需要将 Cloudflare API 密钥以纯文本的形式存储在服务器的硬盘上。在我的情况下，它是在根帐户下，但仍然。它不太理想。

首先创建一个`/root/.secrets`目录，然后创建文件`/root/.secrets/cloudflare.ini`。您还需要确保只有根用户可以访问该文件。

```
mkdir -p /root/.secrets
touch /root/.secrets/cloudflare.ini
chmod 600 /root/.secrets/cloudflare.ini 
```

Enter fullscreen mode Exit fullscreen mode

现在，在文本编辑器中打开文件，添加以下内容，用您的真实内容替换我的示例细节。您的 API 密钥在您的配置文件下，我使用的是我的全局 API 密钥。

```
dns_cloudflare_email = "cloudflare-user@example.com"
dns_cloudflare_api_key = "2aae6c35c94fcfb415dbe95f408b9ce91ee846ed" 
```

Enter fullscreen mode Exit fullscreen mode

接下来，您只需运行 certbot 命令。我已经为我的 web 服务器配置了我的证书文件的位置，所以当我需要添加一个新域或子域时，我可以只使用下面的命令，用任何域替换`example.com`。

```
certbot certonly --dns-cloudflare --dns-cloudflare-credentials /root/.secrets/cloudflare.ini -d example.com 
```

Enter fullscreen mode Exit fullscreen mode

如果你正在使用 NGINX，并且想让你的站点自动设置，你可以使用下面的命令:

```
certbot certonly -i nginx --dns-cloudflare --dns-cloudflare-credentials /root/.secrets/cloudflare.ini -d example.com 
```

Enter fullscreen mode Exit fullscreen mode

这就是所要做的，你只需要稍微调整一下你通常使用的命令。