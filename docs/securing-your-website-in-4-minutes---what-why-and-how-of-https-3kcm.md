# 在 4 分钟内保护您的网站——HTTPS 的什么、为什么和如何

> 原文：<https://dev.to/davidisrawi/securing-your-website-in-4-minutes---what-why-and-how-of-https-3kcm>

[![Donzo](img/32c8de4c3eb1a21af7c7f9b17ef91154.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Xm_Ogd3P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u5l7rzt4uog7lyvgsi32.png)

今天我把我网站的协议从 HTTP 改成了 HTTPS——又快又简单。完成后，我不确定我真正完成了什么，所以我做了一些研究，看看在你和网站之间建立一个安全的连接到底意味着什么。

## 这里快速总结一下。

当您向网站提交文本(如登录信息、聊天信息、搜索查询)时，信息会被发送到服务器，服务器可能会将信息返回给您。这种信息交换是使用*超文本传输协议*进行的。问题是这些信息的脆弱性；任何拦截此网络的人都可以看到您的消息，这对您的数据不利。

[![Catch from Wireshark](img/4ad4539b730d1f2ce4140bac5ae9aa12.png "No bueno")](https://res.cloudinary.com/practicaldev/image/fetch/s--1TU4cN7N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f9d35vdx87b0x6ow1gg2.png) 
图片:这是在更改协议之前，从我的计算机发送到我的站点的示例包。使用 Wireshark 被抓。

这个漏洞就是为什么 HTTPS (HTTP + *Secure* )被大力提倡的原因。

该协议对您的消息进行加密，并通过 SSL 证书向接收方发送一个公钥。这个公钥用于端到端加密，或者验证证书签名(感谢评论中的 Vin 以作澄清)。

### 如果我不发送/接收来自我的网站的敏感数据怎么办？

除了确保信息交流，HTTPS 还有更多好处:

*   通过分析您的信息交换来防止入侵者识别您的用户。
*   降低任何人利用你的网站资源为自己谋利的风险。
*   随着渐进式 Web 应用程序越来越受欢迎，服务人员(用于推送通知)需要使用 HTTPS。
*   服务工作者的其他好处包括离线行为和缓存。

## 更改您的网站以使用 HTTPS

[![Site before changing protocol to HTTPS](img/d319b6d94b83c3e512b4c9c348edabd7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--T-ZMfvUO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/aqllhzrum9j2otepsel6.png)

有一个由[http Sesay](https://httpsiseasy.com/)制作的 5 分钟视频解释了如何做到这一点。这是我在使用 Cloudflare 时遵循的一步一步的教程。

*   转到 [Cloudflare](https://www.cloudflare.com/)
*   签约雇用
*   输入您网站的域名。输入，释放，继续，输入
*   该服务将为您提供两台 DNS 域名服务器，以及将其添加到您的网站的说明。
*   点击工具栏上的加密，改变“总是使用 HTTPS”为开

这样做，你就是 donzo，变化可能需要几分钟到 48 小时，但你不需要做任何其他事情。

[![Site after changing protocol to HTTPS](img/6d36166708c373c014d23f3b188238a1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GWTWaSdy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sz1rml131nh66yzyxqlp.png)

做完这些后，我和我弟弟( [@sammyisra](https://dev.to/sammyisa) )聊天，告诉他我用 Cloudfare 做这个，他告诉我他用了 Netlify。很好奇大部分人都用过什么，请在下面留下评论分享你用过什么服务，为什么。

谢谢大家！

### 其他有用资源:

*   [谷歌——为什么 HTTPS 如此重要](https://developers.google.com/web/fundamentals/security/encrypt-in-transit/why-https)
*   [即时 SSL -什么是 HTTPS](https://www.instantssl.com/https-tutorials/what-is-https.html)