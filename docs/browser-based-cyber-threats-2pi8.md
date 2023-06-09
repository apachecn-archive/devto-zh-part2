# 基于浏览器的网络威胁

> 原文：<https://dev.to/exadra37/browser-based-cyber-threats-2pi8>

读完这篇文章后，我想我应该把它放在这里，以提高我们开发人员的意识，因为根据我的经验，我知道他们中的许多人并没有意识到当他们浏览网站时，他们可能会被动地浏览网站内容，即使网站看起来完全正常，行为正常。

##### 引用文章:

> 作为基于浏览器的攻击如何工作的一个示例，考虑这样一个场景:Windows 用户访问一个看似良性但现在是恶意的网站，该网站可能是他或她以前访问过的网站，或者是一封诱人的电子邮件的结果。一旦建立连接，用户的浏览器就开始与网站进行交互。假设系统使用 JavaScript，根据 Web Technology Surveys 等研究公司的调查，94%的网站都使用 JavaScript，90%以上的浏览器都启用了 JavaScript，浏览器将立即从恶意网站下载并开始执行 JavaScript 文件。

可以使用几种使用 javascript 的技术，比如来自著名网络罪犯 *MageCart* 的[英国航空](https://www.wired.com/story/british-airways-hack-details/)或 [TicketMaster](https://www.riskiq.com/blog/labs/magecart-ticketmaster-breach/) 黑客攻击。

但是 javascript 并不是浏览器攻击的唯一载体，Flash 或 PDF 也可以用来利用您的浏览器漏洞。

因此，所有这类浏览器攻击都将被用来在你输入数据时窃取数据，比如登录你的银行账户的凭证，或者用恶意软件或勒索软件永久感染你的计算机。

所以我们必须怀疑什么时候一个页面花费了太多的时间来加载，或者在你的标签页中保持 spinner 活动，然后点击 F12 来查看正在做什么。

我使用[9.9.9.9](https://www.quad9.net/)和[史蒂文布莱克主机文件](https://github.com/StevenBlack/hosts)来解析 DNS，以保护我免受被认为是危险的站点的攻击(我将在稍后的设置中做一篇文章)。另一种替代方案也存在，但它们需要在我的网络上运行活跃的软件，我对允许这样做没有信心。我也使用总是开启追踪保护的火狐浏览器。

哦，我有没有告诉你，现在我访问的网页的加载速度更快了，而且没有跟踪和广告了？？？