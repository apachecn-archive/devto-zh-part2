# 让我们加密太棒了

> 原文：<https://dev.to/funkysi1701/lets-encrypt-is-awesome-5d23>

Let's Encrypt 是一种免费的方式来获得 SSL 证书到您的网站上，直到最近，我从来没有尝试过。这很简单，我认为这很棒。

IIS 是微软随 Windows 10 和 Windows Server 附带的网络服务器软件。我在笔记本上安装了它，它显示默认的 IIS 页面。

[![](../Images/95ba7073aff15763d96eddbcc3d5d8cd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0GyL-bov--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storageaccountblog9f5d.blob.core.windows.net/blazor/wp-content/uploads/2018/04/iis.jpg%3Fresize%3D768%252C464%26ssl%3D1)

在你的笔记本电脑上托管网站，使用专用的网络服务器，或者在托管公司托管，这都不是一个好主意，但是技术是一样的，这给了我一些东西来写！

为了将域名指向我的计算机上的 IIS 所提供的内容，我执行了以下操作:

*   做一个谷歌搜索“我的 IP 是什么”，这将返回您的公共 IP。大多数住宅 ISP 使用动态 IPs，所以它可能会随着时间的推移而改变，(这也是不要在你的笔记本电脑上托管网站的另一个原因！)
*   使用您刚刚获得的 IP 地址在域上添加一个 A 记录
*   您的公共 IP 很可能指向您的路由器而不是您的笔记本电脑，因此启用端口 80 和端口 443 到您笔记本电脑内部 IP 的端口转发(类似 192.168.0.11 等)

有趣的事情来了，让我们加密吧！

首先你需要一个 Let's Encrypt 客户端，有很多这样的客户端，大部分是针对 linux 的，但是稍微搜索一下就发现了一个 windows 的。去[https://github.com/PKISharp/win-acme/releases](https://github.com/PKISharp/win-acme/releases)下载 zip 文件并解压。

运行 zip 文件中的可执行文件，并按照屏幕提示进行操作。

[![](../Images/ceec708cde9cb03439727d0e9753ed05.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Fh8r8BpS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storageaccountblog9f5d.blob.core.windows.net/blazor/wp-content/uploads/2018/04/letsencrypt.jpg%3Fresize%3D768%252C480%26ssl%3D1)

按 N 创建新证书。

然后按 1 绑定到在您的 IIS 设置中找到的单个网站

[![](../Images/9975c776a3ac289bea47831d327dac0e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tLu_EXA6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storageaccountblog9f5d.blob.core.windows.net/blazor/wp-content/uploads/2018/04/letsencrypt2.jpg%3Fresize%3D768%252C686%26ssl%3D1)

现在，让我们神奇地加密知道你在 IIS 中设置了什么。

现在你需要做的就是输入一个电子邮件地址，以防续约失败，并同意让我们加密条款，你都设置好了。

[![](../Images/6849b388f70861cd91147a023b2e0388.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--y_Z5vsPZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storageaccountblog9f5d.blob.core.windows.net/blazor/wp-content/uploads/2018/04/letsencrypt3.jpg%3Fresize%3D768%252C920%26ssl%3D1)

让你的网站使用 SSL 证书是多么的棒和简单。如果你在服务器上配置了 IIS，试一试，你就可以对你所有的东西进行 SSL 了。