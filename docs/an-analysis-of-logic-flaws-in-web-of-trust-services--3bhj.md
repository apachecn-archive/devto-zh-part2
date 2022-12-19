# 信任网服务的逻辑缺陷分析

> 原文：<https://dev.to/edoverflow/an-analysis-of-logic-flaws-in-web-of-trust-services--3bhj>

# 摘要

诸如 Keybase、Onename 和 Blockstack 等网络信任服务(WOT)承诺在网络上验证个人身份。由于 web 上的许多应用程序不一致，这通常会导致意外行为，从而导致可信 web 服务中的安全漏洞。这篇文章分析了我在研究 WOT 服务安全性时偶然发现的三种攻击媒介。

# WOT 服务背后的技术

WOT 服务允许用户创建令牌并将其放在他们的个人页面上(例如 GitHub profile)。然后，该服务将使用一个 scraper 查找令牌，如果令牌有效，则显示用户确实拥有该外部页面。这种方法背后的想法是让用户可以显示 web 上哪些页面属于他们，然后将他们的 WOT 帐户绑定到所有这些外部服务。最重要的是，由于验证令牌需要公开访问，其他用户可以通过访问包含令牌的页面来验证证据是否合法。

[![](../Images/cd371175b2f3e0883b9a23dde2b0043b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xWE5_dkh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vtx1z51ecgqzj69rs77g.png)

<center>*User [TomNomNom](https://keybase.io/tomnomnom) cryptographically verifying their online identity on Keybase.*</center>

# 攻击载体一——时间线上的内容分布

在网络上，许多基于个人资料的应用程序允许通过分享他人在你个人时间线上的条目来重新分发内容——在 Twitter 上，用户可以转发内容，在 GitHub 上，人们可以通过 T2 分享项目。由于一些 WOT 服务使用公共入口或帖子来验证用户的身份，我问自己是否有可能通过让他们共享张贴在攻击者时间轴上的令牌来声明其他人帐户的所有权。一个特别的服务引起了我的注意，GitHub，其中诸如 Keybase 之类的 WOT 应用程序要求用户将验证令牌放入 GitHub gist 文件中。我注意到 GitHub 的一个有趣的行为是，当用户派生一个要点时，要点不仅显示在用户的时间线上，它还用分享者的用户名替换了原作者的用户名。

[![](../Images/c3129850538f657c43334ce5c211f61c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hmJJMx9U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/18099289/36165627-1901716e-10e8-11e8-9def-a3349954851a.png)

<center>*A forked GitHub gist — the original author is EdOverflow and the gist was forked by bayotop.*</center>

一个易受攻击的服务是 Keybase，如果攻击者可以说服受害者分叉他们的 GitHub gist，攻击者就可以声称拥有受害者的 GitHub 用户名。最重要的是，Keybase 允许修改验证代码片段，允许攻击者在 HTML 注释中隐藏令牌。

使用这种技术攻击 Keybase 的一个示例可能如下所示:

1.  **攻击者**向**密钥库**为**受害者** GitHub 用户名请求验证令牌；
2.  **Keybase** 提示**攻击者**将验证令牌放入 *keybase.md* gist 中；
3.  **攻击者**创建一个 *keybase.md* gist，将验证令牌隐藏在 HTML 注释中；
4.  **受害者**分叉**攻击者** GitHub 要点；
5.  **攻击者**指示 **Keybase** 验证/ **受害者** /keybase.md

因此，攻击者的 Keybase 帐户声明他们拥有受害者的帐户。更糟糕的是，Keybase 有一个[浏览器扩展](https://keybase.io/docs/extension)，允许用户浏览某些应用程序(例如 GitHub、Twitter、黑客新闻等。)并通过个人资料页面在 Keybase 上给用户发消息——该扩展在用户的个人资料上添加了一个小消息窗口。消息被发送到已经验证了帐户所有权的 Keybase 上的帐户。这意味着该扩展会欺骗用户以为他们正在向预定的收件人发送消息，但所有消息都会进入攻击者的收件箱，因为他们控制了受害者的用户名。

[![](../Images/41c58383bc294060b797d414ee0cb809.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--s5_rUOjn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/18099289/36165628-1921e5ac-10e8-11e8-9fc5-e3ac95e98d1e.png)

<center>*Hijacked GitHub username (@jackds1986) viewed in the Keybase browser extension. All messages are sent to a user called "totallynotjackds" on Keybase.*</center>

[Blockstack](https://blockstack.org/) 和 [fireblock.io](https://fireblock.io/) 也容易受到这种攻击媒介的攻击。在大多数情况下，修复只是使用 GitHub 的 gist API 验证 GitHub gist 是否是一个 fork。

# 攻击载体二——命名空间攻击

一些 WoT 服务要求将验证令牌放在特定的文件名中。例如，如前一节所述，Keybase 需要将 GitHub 验证令牌放在名为 *keybase.md* 的 GitHub gists 中。在 web 资产上，Keybase 要求将文件命名为 *keybase.txt* ，并放置在顶级目录或`.well-known`路径下。在 [RFC5785](https://tools.ietf.org/html/rfc5785) 中的`.well-known`提议背后的原因是为了防止文件名冲突和阻塞根目录。就 WOT 服务而言，前者尤其有趣，因为如果攻击者能够控制网站上的文件名，他们就有可能声称拥有该域名。一个这样的例子发生在 liberapay.com 和基贝斯身上。开源捐赠平台 Liberapay 并没有限制用户名中包含点号；因此，用户可以创建包含文件扩展名的用户名。当我为 security.txt 项目([https://liberapay.com/security.txt](https://liberapay.com/security.txt))建立一个个人资料页面时，这一点变得很明显。我创建了一个名为 *keybase.txt* 的用户，并在概要文件的描述中嵌入了 keybase 验证片段。这让我可以宣称 liberapay.com 的所有权。Keybase 没有验证 keybase.txt 文件的内容类型，甚至没有确保令牌没有嵌入到页面中。

[![](../Images/96bbf4c2c2aa6202ef522f77267540d9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--l7lVpekm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/18099289/35120564-d26274aa-fc98-11e7-884f-b972fdc44efe.png)

<center>*Claiming ownership of liberapay.com by creating a user called "keybase.txt" and embedding the verification snippet into the profile's description.*</center>

# 攻击向量三—重定向

在声称拥有 liberapay* *之后。com* *我注意到 liberapay* *。org* *重定向至 liberapay* *。com*T6】。下一次攻击包括使用为 liberapay *生成的验证令牌。org* *嵌入在 liberapay* *上。com* *声称拥有 liberapay* *。组织* *。Keybase 的 scraper 会盲目地跟随重定向，而不会验证最终端点以确保它与目标主机匹配。Keybase 会请求 liberapay* *。org* */keybase.txt，重定向到 liberapay* *。com* */keybase.txt，其中包含有效的 keybase.txt 文件。

[![](../Images/8a7774c94cd4fea5802501e356562837.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Kascy57M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/18099289/36167048-659b30b0-10ec-11e8-8dbb-14b97337020a.png)

<center>*Claiming ownership of liberapay.org via liberapay.com's keybase.txt file.*</center>

我能想到的一个特别可信的攻击场景是使用这种技术来声明品牌 URL 缩写。

# 结论

受到这些攻击媒介影响的所有服务都得到了通知，并迅速解决了大多数报告的问题。Keybase 仍然容易受到攻击向量 2 和 3 的攻击——据我所知，他们不打算解决这些问题。所有受影响方的响应时间给我留下了深刻的印象，我期待着将来与他们再次合作。这项研究的结果是，我已经沉迷于寻找逻辑缺陷。