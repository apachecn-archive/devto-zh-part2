# 同形异义体，攻击！

> 原文：<https://dev.to/logan/homographs-attack--5a1p>

自 20 世纪 90 年代以来，我们就知道同形异义词攻击——所以你可能会奇怪，为什么我要在 2018 年写这些攻击。别担心，我会处理的。在本帖中，我们将探索同形异义词攻击的历史，以及为什么像许多源于路径依赖的互联网问题一样，它们似乎永远不会消失。

## 我的兴趣起源

几个月前，当我在 Kickstarter 的 [Hackerone](https://hackerone.com) 项目中处理门票时，我第一次对同形异义词攻击产生了兴趣。HackerOne 是一个“bug bounty program”，或者说，是黑客和安全研究人员在我们的网站中找到漏洞以换取金钱的邀请。

当我看票的时候，有一张引起了我的注意。这不是一个特别高风险的漏洞，但我不理解票中的许多单词，所以我当然感兴趣。黑客担心 Kickstarter 的个人资料页面。(我们经常会收到关于我们个人资料和项目页面的报告。)

[![Example of Kickstarter's Profile Page](img/a2285b63064cb0faa3790d661ae8d271.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--URpsHPUS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgur.com/Yr3KizX.png)

个人资料页面通常会给网站带来漏洞。无论何时，当你在你的网站上“招待”某人时，你都要考虑他们会如何滥用你给他们的合法性。我们的黑客特别关注一个允许我们的用户在他们的个人资料中添加用户网址或“网站”的领域。

[![Example of Kickstarter's Profile Page Websites](img/90fc07f04c8c6e00743d1f9f2ea219cc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--D43etkIM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgur.com/6EpiaXz.png)

他们认为这部分可以用于同形异义词攻击。对此我想，到底什么是同性恋攻击？这个问题让我陷入了一个国际互联网监管的兔子洞，一把把 RFC，以及一场关于互联网全球性质的长达几十年的辩论。

## 互联网名称与数字地址分配机构(ICANN)

我们必须从 ICANN 说起，它是负责这个故事的主要国际互联网机构。ICANN 制定了关于什么可以是域名，什么不可以是域名的所有规则(以及执行 DNS 根区域注册管理机构的技术维护和维护互联网的名称空间)。

比如说你去 Namecheap 注册“loganisthemostawesome.com”。Namecheap 使用[“可扩展供应协议”](https://en.wikipedia.org/wiki/Extensible_Provisioning_Protocol)通过 Verisign 验证您的姓名。Verisign 是管理“注册管理机构”的组织。com“gTLD。Versign 为你的注册尝试检查 ICANN 的规则和规定，把结果告诉 Namecheap，Namecheap 告诉我是否可以注册“loganisthemostawesome.com”。剧透:我可以！

这太棒了。但是我主要说英语，我在网上的所有生意都使用 ASCII 码。那些不能用与 ASCII 兼容的脚本来表达的语言会发生什么？

### 国际化域名的第 1 版

ICANN 试图回答这个问题，他们在 90 年代末提出并实施了 IDN T1 作为域名的标准协议。他们想要一个更加全球化的互联网，所以他们向各种 unicode 表示的脚本开放了域名。

什么是剧本？脚本是单个系统的字母/符号的集合。例如，拉丁语是一种支持多种语言的文字，而像汉字这样的文字是支持日语的文字之一。脚本可以支持很多语言，语言可以由多个脚本组成。ICANN 保存着与任何给定文字相关联的所有 unicode 字符的表格。

现在这样更好！通过 IDN，ICANN 赋予了我们跨多种文字表达互联网群体的能力。然而，有一个重要的要求。ICANN 的域名系统执行查找服务，将用户友好的名称翻译成网络地址以查找互联网资源，但实际上仅限于使用 ASCII 字符。

### 双关语

因此 ICANN 转向了 Punycode。Punycode 只是微不足道的 unicode。Bootstring 是一种算法，它将以本地语言脚本(unicode)编写的名称转换为与域名系统(punycode)兼容的 ASCII 文本表示。

例如，以这个虚构的域名为例(因为我们仍然不能在通用顶级域名中使用表情符号😭):

> 你好👋朋友💖🗣.com

如果您将它放在浏览器中，对域名系统的真正查找将不得不使用 punycode 等价物:

> `xn--hifriends-mq85h1xad5j.com`

所以，问题解决了。我们有办法在 unicode 文字中使用域名，这些文字代表了互联网的全球覆盖面，并且可以开始分发 IDN。太好了！什么会出错？

## 同形异义词

事情并不总是像看上去的那样。这就是同形异义词和同形异义词的由来。

同形异义词是指看起来或似乎相同，但有不同含义的多个事物。我们在英语中有很多这样的词，例如“打火机”可以指引火物或比较级形容词。

当涉及到 IDN 时，问题是文字之间也存在同形异义，许多拉丁字母在其他文字中有副本，如希腊语或西里尔语。

来自 homoglyphs.net 的 lookalikes 示例:

[![Homoglyphs.net Image of Example Homoglyphs](img/c630e4b4dfe9e84b51eed19b9a5bf172.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vT1YxCvG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgur.com/lpDpgMO.png)

让我们看一个域名的例子。

> washingtonpost.com

相对

> wаshingtonpost.com

你能看出区别吗？好吧，让我们把这两个都翻译成纯粹的 ASCII:

> washingtonpost.com

相对

> `xn--wshingtonpost-w1k.com`

哦，这些肯定不一样。然而，为了使 punycode 对用户友好，用户代理将使它们在浏览器中显示相同。但实际上，fake-WaPo 中的第一个“a”实际上是一个西里尔字母。当翻译成 punycode 时，我们可以看到 ASCII 剩余字符“wshingtonpost”和一个表示西里尔字母 a 的键“w1k”。

这给 ICANN 带来了一个大问题。当用户代理将《华盛顿邮报》和《华盛顿邮报》解释为同形异义词，使它们看起来完全相同时，你可以清楚地看到它们是如何被用于网络钓鱼攻击的。那么 ICANN 该怎么做呢？

## 国际化域名版本 2 & 3

到 2005 年，ICANN 已经找到了解决方案。他们告诉 gTLD 的注册服务商，他们必须限制混合脚本。每一个注册的域名都必须有一个“标签”来标明域名用来支持其语言的单一纯文字。今天，如果你试图在`xn--wshingtonpost-w1k.com`注册我们的山寨版《华盛顿邮报》,你会得到一个错误。注意:但是，对于需要混合脚本的语言，如日语，有一些例外。

问题解决了，对吧？嗯，虽然混合文字是不允许的，但根据 ICANN 的指导方针，纯文字仍然非常好。因此，我们仍然有一个问题。西里尔字母或希腊字母中看起来像拉丁字符的纯文字怎么办？会有多少呢？

## 概念证明

[![Gif of POC](img/693d9421303c75193ca7b440bb885722.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EthjMyVI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://imgur.com/W5Q1D2N.gif)

嗯，当我和我的朋友 [@frewsxcv](https://github.com/frewsxcv) 谈论同形异义词攻击时，他有了一个好主意，可以编写一个脚本来查找易受攻击的 URL。所以我做了一个[同形攻击探测器](https://github.com/loganmeetsworld/homographs-talk/tree/master/ha-finder)说明:

*   排名前 100 万的网站
*   对于每个域，检查每个域中的字母是否与拉丁或十进制混淆
*   检查该域名的 punycode url 是否通过 WHOIS 查询进行了注册
*   返回我们可以注册的所有可用域

很多网址看起来有点像西里尔文(也有很多排名前 100 万的网站是色情网站)，但我们发现了一些有趣的网站，你可以注册。

举个例子，这是我个人最喜欢的。在 *Firefox* 和 *Chrome* 中，访问:

> T0 https://raur。com/T1

这是它们在这些浏览器中的样子。

火狐浏览器:

[![Firefox IDN display algo](img/800d65b9ff3b2c71473038c462461617.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wxgkOd15--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgur.com/6Nvi8tq.png)

铬合金:

[![Chrome IDN display algo](img/592d0df47671f3fa3b988236ed5e44eb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JwtKvCOZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgur.com/lHf0IaI.png)

相当酷！在 Firefox 中，地址栏看起来完全像官方的 PayPal！然而，在 Chrome 中，它解析为 punycode。这是为什么呢？🤔

## 用户代理&他们的国际化域名显示算法

是因为 Chrome 和 Mozilla 使用了不同的国际化域名显示算法。Chrome 的算法比 Mozilla 的要严格和复杂得多，并且包括特殊的逻辑来防止同形异义词攻击。Chrome 会检查域名是否在 gTLD 上，并且所有的字母都是容易混淆的西里尔字母，然后它会在浏览器中显示 punycode 而不是 unicode 字符。Chrome 最近才改变这一点，因为[郑旭东的 2017 年报告](https://www.xudongz.com/blog/2017/idn-phishing/)将`www.xn--80ak6aa92e.com`用作概念验证。

另一方面，Firefox 仍然以其预期的脚本显示完整的 URL，即使它与拉丁字符相混淆。我想指出的是，Firefox 允许你在浏览器中更改设置为 *always* show punycode，但是如果你经常使用非 ASCII 域的网站，这可能很难实现。

## 那么，接下来呢？

那么，如果我们认为有人可能利用我们的网站用同形异义词来钓鱼，那么作为应用程序开发人员和维护人员，我们的责任是什么呢？我可以看到几条前进的道路:

1.  倡导 Mozilla 和其他用户代理确保改变他们的算法以保护用户。
2.  主张 ICANN 改变其关于用拉丁易混淆字符注册域名的规则。
3.  实现我们自己的显示算法。这就是我们最终在 Kickstarter 上做的事情。我们使用了谷歌的开源算法，如果页面上显示的 url 可能是另一个 url 的同形异义词，就会显示警告。
4.  最后，我们可以像注册 PayPal 一样注册这些域名，这样它们就不会被恶意使用。可能的话，如果我们是一个有易受影响域的组织的一部分，我们应该只注册它。

总结一下，这是一个很难的问题！这就是它存在了二十年的原因。从根本上说，我发现这次袭击所暴露出的问题非常有趣。我个人认为 ICANN 允许在各种文字中使用 IDN 是正确的。互联网应该对所有人更加开放。

然而，我喜欢 Chrome 支持其显示算法的声明，它很好地总结了游戏的权衡:

> 我们希望防止混淆，同时确保不同语言的用户在 Chrome 上有很好的体验。在太多的网址上显示双关语或可见的安全警告会损害世界各地人们的网站可用性。

互联网充满了围绕可访问性和安全性的权衡。作为这个美好地方的使用者和维护者，我发现像这样的对话是共同建设我们的世界的最好的部分之一。

现在，我们只需要得到一些表情符号的支持。

感谢阅读！🌍💖🎉🙌🌏

## 资源

### 背景

*   [维基百科对同形异义词的攻击](https://en.wikipedia.org/wiki/IDN_homograph_attack)
*   [关于 IDN 的维基百科](https://en.wikipedia.org/wiki/Internationalized_domain_name)
*   [用同形异义词混淆的文本中的剽窃检测](http://eprints.whiterose.ac.uk/112665/1/paper_247v2.pdf)
*   [一种集体智能的方法来检测 IDN 网络钓鱼，作者:沙扬-雄曾、卢爱琴、程恒 Ku 和耿光刚](https://kaigi.org/jsai/webprogram/2013/pdf/960.pdf)
*   [通过给 Unicode 字符串着色来暴露同形异义词混淆意图，作者刘、Anthony Y. Fu 和邓小铁](https://slides.tips/exposing-homograph-obfuscation-intentions-by-coloring-unicode-strings.html)
*   [郑旭东利用 Unicode 域名进行网络钓鱼](https://www.xudongz.com/blog/2017/idn-phishing/)
*   [叶夫根尼·加布利洛维奇和亚历克斯·贡特马赫的同形异义攻击](http://evgeniy.gabrilovich.com/publications/papers/homograph_full.pdf)注:原文！
*   Tobias Holgers，David E. Watson 和 Steven D. Gribble 的《穿越混乱:同形异义词攻击的测量研究》
*   [Peter Hannay 和 Christopher Bolan 对国际化域名同形异义词攻击缓解的评估](http://ro.ecu.edu.au/cgi/viewcontent.cgi?article=1010&context=ism)
*   多语种网站:约翰尼·阿尔·贺楼和斯科特·蒂利的国际化域名同形异义攻击
*   [IDN 同形异义攻击潜在影响分析 by @jsidrach](https://github.com/jsidrach/idn-homograph-attack)

### 浏览器策略

*   [Chrome 的 IDN 显示算法](https://www.chromium.org/developers/design-documents/idn-in-google-chrome)
*   [Mozilla 的 IDN 显示算法](https://wiki.mozilla.org/IDN_Display_Algorithm)
*   [UTC 混合脚本检测安全机制](https://www.unicode.org/reports/tr39/#Mixed_Script_Detection)
*   [Chrome 的 IDN 恶搞检查器](https://cs.chromium.org/chromium/src/components/url_formatter/idn_spoof_checker.cc)
*   [Bugzilla 在 IDN 上打开 Bug](https://bugzilla.mozilla.org/show_bug.cgi?id=1332714#c79)

### 工具

*   [混合文字的同形词攻击生成器](https://github.com/UndeadSec/EvilURL)注意:已经不能注册混合文字域名。
*   [纯西里尔文字的同形异义词攻击探测器](https://github.com/frewsxcv/homograph-attack-finder/)
*   [同形异义词攻击探测器+查找纯西里尔文字的 WHOIS】](https://github.com/loganmeetsworld/homographs-talk/tree/master/ha-finder)
*   [同形字典](http://homoglyphs.net/)
*   [Puncode 转换器](https://www.punycoder.com)

### ICANN CFP 和指南

*   [2005 年 2.0 版指南](https://www.icann.org/resources/unthemed-pages/idn-guidelines-2005-11-14-en)
*   [ICANN 2005 年 IDN 指南 2.0 版 RFC 公告](https://www.icann.org/news/announcement-2005-02-23-en)
*   [IDNA2008 版本 2.2 草案](https://www.icann.org/en/system/files/files/idn-guidelines-26apr07-en.pdf)
*   [2011 IDN 3.0 版指南](https://www.icann.org/resources/pages/idn-guidelines-2011-09-02-en)

### ICANN、Verisign 和域名注册流程

*   TLD 的维基百科。每个 TLD 都有自己的注册中心来管理它并定义它的 IDN 规则。
*   [维基百科的域名注册处](https://en.wikipedia.org/wiki/Domain_name_registry)，像 Verisign
*   [维基百科中的域名注册商](https://en.wikipedia.org/wiki/Domain_name_registrar)，如 Name price、Godaddy 或 Gandi.net
*   [ICANN 认证和 Verisign 认证](https://www.verisign.com/en_US/channel-resources/become-a-registrar/verisign-domain-registrar/index.xhtml)用于分发。com 域
*   [可扩展供应协议](https://en.wikipedia.org/wiki/Extensible_Provisioning_Protocol)的维基百科，当注册中心的用户请求. com 域名时使用。注册中心使用 EPP 协议与 verisign 通信来注册域。
*   Verisign 的 IDN 政策。Verisign 要求您在注册时指定一个与域名相关的三个字母的语言标签。此标签决定了您可以在域中使用哪些字符脚本。大概 https://аррӏе.com/(西里尔文)的语言标签是“RUS”或“UKR”。
*   PIR，的经理。组织顶级域名，IDN 规则

### 与域相关的杂项安全

*   [扩展验证被@iangcarroll 打破](https://stripe.ian.sh/)

### 同形异义主要站点复制——猫的例子

*   http://аоӏ.com/
*   https://。com/
*   https://arr。com/
*   T0 http://www。PPP。com/T1