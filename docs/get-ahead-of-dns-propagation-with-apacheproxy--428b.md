# 使用 Apache 代理领先于 DNS 传播

> 原文：<https://dev.to/mac_hour/get-ahead-of-dns-propagation-with-apacheproxy--428b>

* * *

将网站从一台服务器迁移到另一台服务器通常是一个简单的过程。这是如果 DNS 传播没有咬你的屁股。

问题:我为许多每天发布新内容的客户托管了几个网站。我需要将它们从服务器 **A** 迁移到服务器 **B** 。

这里没什么特别的，我通常会经历以下步骤:

*   警告客户我正在执行迁移，并要求他们在预定义的时间内不要发布内容
*   将文件和数据库从 **A** 复制到 **B**
*   通过在我的`/etc/hosts`文件中强制使用新的 IP 来测试新服务器上的网站
*   在域的区域文件中切换服务器 IP 地址希望 DNS 传播尽可能快地执行

但是即使你的区域 TTL 很低，一些 ISP 仍然会解析到旧的 IP 地址。这甚至发生在域的区域更新 48 小时之后。一些用户仍将从 A 获得内容，对此你无法控制。如果你的客户登陆 B 并发布新内容，这些用户将无法看到它。

如果您的客户 ISP 仍然在解析对旧服务器的请求，情况会变得更加棘手。新内容可能会发布在旧服务器上，从 A 到 b 的同步会变得更加混乱。

当然，您可以更改 A 上的脚本，使它们使用 b 上托管的新数据库。但是，如果您的客户随新帖子一起发布图片，该怎么办呢？新的二进制文件会在 **A** 上，新的数据库内容会在 **B** 上，你会有很多麻烦:

[![trouble](img/3d7242687f65683158960d545667d413.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--evtqXxDG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mf5521rq25qdtyzdo8yb.png)

* * *

不要担心，这可以妥善处理:阿帕奇代理救援！

这里的想法是，我们将把网站从 **A** 变成一个简单的代理，将所有传入的请求转发给 **B** 。这比听起来容易。以下是它的三个简单步骤:

*   确保您已经在 **A** : `sudo a2enmod proxy_http`上的 Apache 服务器上启用了`mod_proxy_http`
*   确保 **A** 为你的域名解析新的 IP 地址，通过强制它在服务器的`/etc/hosts`文件中:`server.B.ip.address example.com`
*   更改上的虚拟主机配置，以添加绿线(不带+):

```
<VirtualHost *:80>
    Define SITE_BASE ${SERVER_BASE}/customer/example.com
    ServerName  example.com

+    ProxyRequests Off
+    ProxyPass / http://example.com/
+    ProxyPassReverse / http://example.com/ 
    LogLevel warn
    ErrorLog  ${SITE_BASE}/logs/apache.error.log
    CustomLog ${SITE_BASE}/logs/apache.access.log vhost_combined
</VirtualHost> 
```

Enter fullscreen mode Exit fullscreen mode

测试您的 Apache 配置并重启它。

通过查看两台服务器上的 Apache 访问日志，可以看到代理转发了所有传入的请求🎉🎉

[![forwarded request](img/26af39ea83656636c825428b6332e2ed.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vD3EdTJw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wsd9sg326e60in9a4wwv.png)

*对 enbref.tn 的请求被解析到 A 并转发到 B*

如果您的站点被配置为通过 SSL 提供内容，您将需要在您的代理配置中包含以下附加行:`SSLProxyEngine on`

我现在可以看到请求数越来越少，直到它们完全消失，我的客户可以放心地发布他们的新内容。

* * *

PS:这个解决方案有一个小小的折衷:B 上的请求 IP 将是 a 的公共地址。这在我的情况下不是问题。

这是解决紧急问题的一种快捷方式。可能存在其他更有效的方法，我很想听听这些方法！