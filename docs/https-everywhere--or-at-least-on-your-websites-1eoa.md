# 到处都是 HTTPS！(或者至少在你的网站上)

> 原文：<https://dev.to/valentinpearce/https-everywhere--or-at-least-on-your-websites-1eoa>

*原发于 2017-10-24[我的自主 WIP 博客](https://alzai.xyz/post/3)*

你可能已经注意到这个网站是加密的！

这是因为随着最近 WPA2 漏洞 KRACK 的发布，我决定设置我四个月前试图设置的东西*(见[我之前的帖子](post/2) )*

上次，这个过程很困难，因为我的操作系统(Raspbian)缺少一些依赖项，我不得不摆弄存储库列表来安装为 Debian 编写的软件。我不知道自己在做什么，决定最好不要继续。

另一方面，这次我发现了 [certbot](https://certbot.eff.org/) ！这个“让我们加密客户端”让它变得简单多了。这个过程是这样的:

*   下载证书机器人
*   *RTFM*
*   运行 certbot
*   失败是因为您没有将端口 443 重定向到您的服务器
*   花半个小时试图记住你的路由器密码(并失败)
*   向您的网络管理员(又名爸爸)询问密码
*   重定向端口 443
*   运行 certbot
*   喝杯茶，吃点饼干庆祝一下！

就这么简单！所以真的，如果你有一个自托管应用程序的网络服务器，不要犹豫，加密一切！

干杯！

我起来了