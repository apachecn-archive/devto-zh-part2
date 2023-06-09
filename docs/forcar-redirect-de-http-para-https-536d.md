# 强制从 HTTP 重定向到 HTTPS

> 原文：<https://dev.to/andersonbraz/forcar-redirect-de-http-para-https-536d>

本文介绍如何解决使用 Apache 作为服务支持的 web 托管服务器上出现的问题。

## 导言

今天，所有媒体都告诉人们:如果网站在浏览器/浏览器地址栏中的绿色图标上没有锁，则表示该网站不受信任。然后，您可以向您的主机提供程序了解，要获得此锁，您需要获得 SSL 密钥服务。

没错！您购买了该服务，但发现如果用户只使用“http://”键入站点的 url，即使使用了订约的 SSL 服务，当用户进入您的页面时，他们也不会看到此类绿色锁定。

## 科莫解析器？

建立强制或重导所有 HTTP 要求至 HTTPS 的条件。

要包含在域根目录的. htaccess 文件中的参数:

```
RewriteEngine On
RewriteCond %{HTTPS} off
RewriteRule (.*) https://%{SERVER_NAME}/$1 [R,L] 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

一个如此简单的调整细节，可以解决这里看起来是个问题。开心点！

**此解决方案仅适用于 APACHE 中的 web 服务器

## 克雷蒂托

马库斯·斯皮斯克在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片