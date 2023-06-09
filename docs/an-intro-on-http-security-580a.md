# HTTP 安全性介绍

> 原文：<https://dev.to/dotnetcoreblog/an-intro-on-http-security-580a>

当你读到这封信的时候，我将已经在利兹大学做了一次演讲

*组织聚会的人棒极了，如果你在这个地区，你一定要参加*

涵盖了 HTTP 安全性的一些方面，以及如何使您的网站不那么容易被黑客攻击。

*我选择了“不被黑的方法”的点击式副标题*

在那次聚会中，幻灯片组只能看到阳光，这让我感到有点遗憾。所以我想我应该和全世界分享它们。此外，参加的人可以坐下来看我成为一个傻瓜，而不是疯狂地记笔记

*即使那些笔记放在我是个彻头彻尾的沃利的事实上*

我用揭示来写幻灯片

*参见 [GitHub repo](https://github.com/GaProgMan/Talks/tree/master/HTTP%20Security%20Intro/) 中的自述文件，了解如何启动并运行它*

并且可以在任何有浏览器和网络连接的计算机上运行。

### 这谈不上什么

[![What this talk is not](img/5a08f6b815d91a6bf26f784366067c6c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fI3Rij1P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7875ap0mdda07h53m1yd.jpg)

这个演讲的设计并不太专业。对于完全不懂技术的人来说，有一些简单的方法(有些是技术不可知的，有些是 WordPress 特有的)。我们将从简单的胜利开始。

### 安全=追赶(有时)

[![Security is catchup sometimes](img/abdc151f350589671c9790c8a5790df6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--su9Qql31--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dpp24busk8h3h5m0efge.jpg)

有时安全是一场猫捉老鼠的游戏，因为更多的黑客攻击和漏洞被发现、报告和修补。站在网络安全的最前沿是一份全职工作。正如特洛伊·亨特(T1)或 T2·斯科特·赫尔姆(T3)一样

在我做这个演讲的几天前，[一个流行的 jQuery 插件中的严重漏洞](https://nvd.nist.gov/vuln/detail/CVE-2018-9206)被报道。它已经“在野外”

*这是“我们，有点，知道这件事”的安全用语*

三年了。

这个网站上还有我的另一篇文章的评论:

[![dotnetcoreblog profile image](img/8756136f44a0bc1562d9bd8568defb6b.png) ](/dotnetcoreblog) [ Jamie ](/dotnetcoreblog) [ ![twitter](img/82aa32a73d0a2eadd783a8a531ea2cc3.png) ](https://twitter.com/dotNetCoreShow) [![github](img/029e4450541d5c4819d89a83d5315060.png)](https://github.com/GaProgMan) [<time datetime="2018-09-13T13:43:00Z">Sep 13 '18</time>](/dotnetcoreblog/comment/5eop)

想象你想偷一辆车。你一条街一辆车的检查。你寻找任何可见的入口，但是你也寻找方向盘上的任何物理锁，等等。你还需要知道哪些型号更容易热连线。

现在想象一下，你不得不把车停在一条发生过很多盗窃案的街道上。为了确保你的车不会被偷，你要确保你已经把贵重物品放在锁着你车的手套箱或行李箱里；在方向盘上安装了物理锁；与伊莫比尔订婚；武装你的警报；等等。

在安全方面，你需要寻找别人侵入你的应用程序的方法。你想找到尽可能多的，并把东西放在适当的位置，以阻止其他人利用它们。

我要说的是，每个 web 开发人员至少应该知道 T2 的 OWASP 十大安全风险 T3。你可能很容易失去一两天的时间，在 OWASP 网站上深入研究(就像任何人用电视比喻一样)，但仍然只是触及皮毛。

### 插件

[![plugins](img/d98dbd08e5dbdfb200dfdd702a1606c8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2HCi2IOa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fta4pbreiwl5h7mzpmo5.jpg)

我们开发人员知道，总会有一个这样的库。无论是 npm 包、NuGet 包，还是只是堆栈溢出的一些代码

你不会在不理解代码首先做什么的情况下从堆栈溢出中复制粘贴 T2，对吗？

因此，很容易陷入运行网站所需的数百行代码的陷阱——其中大部分代码要么过于臃肿，要么难以理解。

如果你需要包含其他人的代码(通过插件或其他方式)，确保它短小精悍，切中要点。正如查尔斯·埃默森-温彻斯特三世所说，它应该:

> 一次做一件事。做得很好。然后我继续前进

### 单一责任原则

[![Single Responsibility is the best ibility](img/7f643119b97a0736268f72c93ee9c4f9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cjbK1svn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/602xq3fskke0t6ymnpgg.jpg)

这与最小特权原则密切相关。基本上，如果您需要让某人或某物访问您的应用程序

*或博客，或网站，或黑色行动网站*

给他们**最少**可能的访问权限；只够完成工作，不能再多了。

在博客的世界里:

*   作者不应该有管理员权限
*   编辑也是
*   类似于计算机和安全管理最佳实践

### 多因素验证

[![](img/84616382229cffac75bad2e8b3c1a8d5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zRSm2cxd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k7ad9ak5gu5604j77yz7.jpg)

多因素身份验证是指您需要多种东西才能登录。传统的单因素身份验证是用户名和密码的组合——这是你知道的一件事。

其他因素可能包括您拥有的东西(即身份验证应用程序)、您的身份(即指纹)等。您启用的这些工具越多，恶意用户想要侵入就需要越多的东西。但这将使正常用户更难登录。

我结合使用 [Authy](https://authy.com/) 和 [YubiKeys](https://www.yubico.com/) 来为我所有的账户提供第二个因子。这些设备使用基于时间的算法来创建需要在登录时输入的一次性代码。

因为是基于时间的，所以*几乎*不可能暴力破解

*差不多；但是比没有好几百倍*

### HTTP 如何工作

[![How HTTP Works](img/3ee127f3fc3c91c27dfc3e90b76cfd6c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aACBlQUO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yhdlpotg9xjeezi33md6.jpg)

作为一个非常简单的例子，假设我想加载 BBC 的主页。

*   我告诉我的浏览器去 https://bbc.co.uk/
*   我的浏览器向我的互联网服务提供商发送 HTTP 请求
*   我的 ISP 帮助找出 BBC 的服务器在哪里
*   然后，它通过一系列路由器转发我的浏览器对网站的请求

*我指的是一系列管子，显然是*

*   它最终到达运行 BBC 网站的服务器
*   服务器为主页生成 HTML，将其放入 HTTP 响应中，并发送给我
*   HTML 采取了与到达目的地相反的路线，以便返回给我

在任何时候，有人都可以拦截请求并查看内容。您可以对响应进行同样的操作。除非是用 HTTPS 加密的。

如果没有加密，那么我和 BBC 服务器之间的任何人都可以更改请求或响应的细节。这有时被称为[中间人攻击](https://en.wikipedia.org/wiki/Man-in-the-middle_attack/)。

加密信息并不容易，因为我和 BBC 服务器之间的人可以得到加密信息的密钥。但是他们必须从一开始就关注这些请求，这并不容易做到。

参见 Russ 的评论:

[![kritner profile image](img/4bfae62782dfaf9a060cd303dd8a5e42.png) ](/kritner) [ Russ Hammett ](/kritner) [ ![twitter](img/82aa32a73d0a2eadd783a8a531ea2cc3.png) ](https://twitter.com/RLHammett) [![github](img/029e4450541d5c4819d89a83d5315060.png)](https://github.com/Kritner) [<time datetime="2018-10-22T23:01:16Z">Oct 22 '18</time>](/kritner/comment/6aco)

> 加密消息并不容易，因为我和 BBC 服务器之间的人可以获得用于加密消息的密钥。但是他们必须从一开始就关注这些请求，这并不容易做到。

这是真的吗？在我的印象中，TLS 之类的东西让双方使用他们自己的私有/公共密钥对，像 diffie hellman 之类的东西来获得一个共享的秘密，然后用 KDF 来导出一个永远不会通过网络传输的密钥。

我说的话有一点错误，因为我把两件事混为一谈了。虽然不可能在 TLS 信息在网络上传播时拦截和解密它们，但如果有足够的计算能力和时间，事后解密是可能的。

### 让我们加密

[![Let's Encrypt](img/8481481878608e06135885bbfd4f8e15.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---iCseMYF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qhdxqhv4au65tt8v8yi8.png)

不管你对大牌认证机构的感觉如何，他们最近犯了一些大错误。Symantech 制造了许多严重的犯规，这意味着他们已经退出了 CS 行业。

Let's Encrypt 是一项提供 TLS 的免费服务

自 90 年代以来，没有人使用 SSL，但这个缩写词一直沿用至今

任何人的证书。你需要做一些工作来让他们自动更新，但你的主机提供商可能会帮助你。我知道那个[梦幻主持人](https://dreamhost.com)

*我使用的主机提供商之一*

可以帮助您设置自动续订证书。

### 子资源完整性

[![SRI in action](img/41676829884299196845e27e440e393b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nh67uWXV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yrbo3q37gv67inubk9sk.jpg)

每当你访问一个网页，就会有大量的 HTML 文件被发送到你的浏览器。那个 HTML

*除非网站全是纯文本*

将包括一些外部资源的链接(通常像图像、样式表和 JavaScript)。在浏览器显示页面之前，它需要获取这些外部资源

*“外部”在这里是指您在*加载网站的浏览器外部

如果外部资源是 JavaScript，那么您需要知道是否有人设法利用了它，无论是在传输过程中(在到达您的浏览器的途中)还是在它的源头(您下载它的地方)。这就是子资源完整性的用武之地。

SRI 本质上是文件内容的散列。当您在 HTML 页面中包含一个脚本(或样式表)时，您可以告诉浏览器它应该期望该脚本有一个特定的指纹(在本例中是 SHA256 散列)。如果下载的脚本的散列与 SHA 256 散列不匹配，那么浏览器将不会尝试运行它。

把“散列”某物想象成获取它的内容，并通过数学公式进行运算。任何符合这个公式的事情都会产生不同于其他事情的答案

### 内容安全策略

[![Content Security Policy](img/0543498733498d08fec543402fae98d6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FHd4Tlci--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mk7jpjkovuu8l58iuy4q.jpg)

有一个老程序员的笑话是这样的:

> 计算机科学中最难的三件事是给事物命名，并以一个错误结束

但那是在 CSP 发明之前写的。

本质上，CSP 是一个允许浏览器加载内容(HTML、脚本、图像、样式表等)的白名单。)从加载您的网站时开始。如果您的网站(或它加载的内容)试图从不在此列表中的网站加载内容，您的浏览器将停止加载该网站。

再读一遍最后一句话:

> 如果您的网站(或它加载的内容)试图从不在此列表中的网站加载内容，您的浏览器将停止加载该网站。

完全正确。CSP 可以破坏你的网站。如果你做得不对

*而且你不可能第一次就做对*

然后你会停止你的网站(或网络应用程序)的工作。

这是服务器端的事情，不适合胆小的人。它还需要你的网络服务器和反向代理如何工作的知识。每个网络服务器处理这个问题的方式不同，所以你需要自己查找如何做这件事(不幸的是)，或者找一个专家

*“boffin”是一个古老的英国俚语，意思是:“拥有被认为是复杂或神秘的知识或技能的人”*

来帮助你。

一组 CSP 规则的示例有:

```
content-security-policy:
    upgrade-insecure-requests;
    default-src 'self';
    connect-src 'self' https://cdn.jsdelivr.net https://api.unsplash.com;
    script-src 'self' https://cdnjs.cloudflare.com https://code.jquery.com;
    img-src 'self' https://pbs.twimg.com https://imagegen.podchaser.com https://*.unsplash.com https://casper.ghost.org https://*.gravatar.com;
    style-src 'self'  https://cdnjs.cloudflare.com/ https://maxcdn.bootstrapcdn.com; 
    frame-src 'self' https://html5-player.libsyn.com/;
    font-src 'self' https://maxcdn.bootstrapcdn.com;
    object-src 'none' 
```

这告诉浏览器它只能从指定的位置加载以下内容:

*   剧本
    *   网站的起源(自我)
    *   [https://cdn.jsdelivr.net](https://cdn.jsdelivr.net)
    *   [https://api.unsplash.com](https://api.unsplash.com)
*   图像:
    *   网站的起源(自我)
    *   https://pbs.twimg.com(推特)
    *   https://imagegen.podchaser.com
    *   https://* . Unsplash . com(Unsplash)
    *   [https://casper.ghost.org](https://casper.ghost.org)(幽灵)
    *   https://* . Gravatar . com(Gravatar)
*   等等。

它还说浏览器应该`upgrade-insecure-requests`这意味着它应该尝试加载 HTTPS 上的所有内容

### 安全报头

[![Secure Headers](img/b609df32f71e6764ef901675994fc0c5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--J-78pbkN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wkxyo1gxav0zqfxpfabc.jpg)

这些是在网络服务器或反向代理级别(如 CSP)添加的东西，旨在使您的网站和网络应用程序对最终用户更加安全。

我们已经对 CSP 有所了解。

HTST 告诉浏览器总是使用 HTTPS 来访问我们的网站，永远不要尝试通过 HTTP。

内容类型是一种阻止恶意用户告诉浏览器它加载的图像是一段 JavaScript(例如)的方法，通过改变它的 [MIME 类型](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types)

*MIME 类型是识别文件的方式，无需依赖文件扩展名*

XSS 保护有助于阻止[跨站点脚本攻击](https://en.wikipedia.org/wiki/Cross-site_scripting)。这个有点复杂，但是有一个例子可以想象你有一个评论框，用户可以在里面留下评论。恶意用户可以在评论框中放入一些 JavaScript，当评论被加载时，脚本就会在浏览器上运行。这是我们没有编写或不想在我们的网站上运行的 JavaScript。

比这要复杂一点，但这是基本要点。

### 链接

*   [滑梯](https://github.com/gaprogman/Http-Security-Intro)
*   [我，在推特上](https://twitter.com/dotnetcoreblog)
*   [CVE-2018-9206](https://nvd.nist.gov/vuln/detail/CVE-2018-9206)
*   [扩展验证证书已死](https://www.troyhunt.com/extended-validation-certificates-are-dead/)Troy Hunt
*   [OWASP 安全头项目](https://www.owasp.org/index.php/OWASP_Secure_Headers_Project#tab=Headers)