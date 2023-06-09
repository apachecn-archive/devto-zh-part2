# OWASP -谁？

> 原文：<https://dev.to/dotnetcoreblog/owasp---who-jck>

我最近写了一篇短文，向所有开发者推荐五个播客，不管他们的技术如何；您可以通过以下链接阅读:

[![dotnetcoreblog image](img/532a02d36322a9e6641cc8377a9f5450.png)](/dotnetcoreblog) [## 我的播客订阅

### 杰米 9 月 7 日 184 分钟阅读

#podcast #productivity #audio](/dotnetcoreblog/my-podcast-subscriptions-1b6m)

在这篇文章的评论中，我的好朋友金成说:

[![dance2die profile image](img/0ee510fbd0d5195e8e16812b53490e7e.png) ](/dance2die) [ Sung M. Kim ](/dance2die) • [<time datetime="2018-09-08T02:07:03Z"> Sep 8 '18 </time>](https://dev.to/dance2die/comment/5bbo) 

哇，杰米，谢谢你的列表和每个播客的概要💃。

首先，Git 上有专门的播客，很神奇。我完全不知道肖恩·怀尔德穆斯的播客。

你为你的播客投入了如此多的时间和精力，并与你的 OWASP 负责人一起帮助其他人关注 OWASP 的安全性，这真是令人惊讶。核心库。

看起来你和马丁都对安全很感兴趣。他为 InfoSec 准备了一整节播客

[![mrtnrdl image](img/7a562adc2ea06fd220ae78e463b8797f.png)](/mrtnrdl) [## 我的播客-订阅

### 马丁·里德尔

#podcast#productivity](/mrtnrdl/my-podcast-subscriptions-jj)

我很想知道你以后是否能发表关于什么是 OWASP 以及为什么我们应该更加关注安全性的文章😉

基于此，我想我应该发布一个关于 OWASP 的帖子，它是什么，他们的目标是什么。

### OWASP

你听说过 [NIST](https://www.nist.gov/topics/cybersecurity) 吧？英国的[国家网络安全中心](https://www.ncsc.gov.uk/)怎么样？这些组织的目标是概述提高应用程序安全性的最佳实践和建议。

然而，他们给出的大多数建议通常都很模糊，足以[适用于大多数服务](https://www.ncsc.gov.uk/guidance/setting-two-factor-authentication-2fa)，或者关于某个[特定服务](https://www.ncsc.gov.uk/guidance/ncsc-advice-reddit-users)。

但是对于我们这些想要增加应用程序安全性的开发者来说呢？这就是 OWASP 的用武之地。

OWASP，即开放 Web 应用程序安全项目，是一个非盈利组织，其目标用他们自己的话来描述最为恰当:

> OWASP 是一个开放社区，致力于使组织能够构思、开发、获取、操作和维护可信任的应用程序。所有 OWASP 工具、文档、论坛和章节都是免费的，对任何对提高应用程序安全性感兴趣的人开放。我们提倡将应用程序安全作为人员、流程和技术问题来处理，因为最有效的应用程序安全方法包括所有这些方面的改进。

[来源](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project#The_OWASP_Foundation)

### 但是这对我有什么帮助呢？

我们都想创建尽可能安全的应用程序，对吗？我们当然知道，但是侵入您自己的应用程序(或者您的组织创建的应用程序)可能是一个灰色地带。

这也是 OWASP 创立的原因之一:收集所有技术栈的最佳安全建议。

我通常在书店工作。NET 技术套件(你可能从我的[项目](http://dotnetcore.show)的[中的一些](https://dotnetcore.gaprogman.com/)中了解到)，当提交我的一个项目进行代码审查和笔测试时，我不知道去哪里(快速)学习如何保护应用程序。

幸运的是，OWASP 有一系列的[备忘单](https://www.owasp.org/index.php/Category:Cheatsheets)来帮助开发者快速保护他们的应用程序，不管他们的技术是什么。他们的大多数建议都是不言自明的(通过[参数化您的 SQL 输入](https://www.owasp.org/index.php/Query_Parameterization_Cheat_Sheet)，或者通过使用[对象关系管理器](https://en.wikipedia.org/wiki/Object-relational_mapping)如实体框架来避免 SQL 注入攻击)，但是有些事情不容易在代码中实现(例如 IIS 设置)。

### 实施 OWASPs 建议

就像单元测试或行为测试一样，将安全性设计到应用程序中比事后实现要容易得多。这并不是说对于一个已经存在的代码库不能这样做，但是要实现它需要付出更多的努力和考虑。这是因为，就其本质而言，实现安全性最佳实践将会破坏您当前的代码——尤其是如果您仓促地构建了您的应用程序，并且没有正确地考虑问题。

因此，最好是了解他们的建议，并尽早将其实现到代码库中。物理建筑也是如此——在建筑建成之前，让它变得更安全更容易。

具体如何实现 OWASP 建议取决于您用来构建应用程序的框架、技术和语言。我通常和。网(。NET 框架，。NET Core)，所以实现 [OWASP 推荐的 HTTP 头列表](https://www.owasp.org/index.php/OWASP_Secure_Headers_Project)看起来有点像下面的:

```
httpContext.Response.Headers.Add("X-XSS-Protection", "1; mode=block"); 
```

Enter fullscreen mode Exit fullscreen mode

事实上，上面的代码块在 [X-XSS 保护](https://www.owasp.org/index.php/OWASP_Secure_Headers_Project#xxxsp)头中添加了一个值，告诉浏览器在检测到可能的[跨站脚本攻击](https://www.owasp.org/index.php/Cross-site_Scripting_(XSS))时停止呈现页面。

对于 HTTP 头，有两种方法可以确保它们被添加到站点生成的所有响应中:

*   在代码中(如上所示)
*   在 web 服务器/反向代理级别

我更喜欢用代码来实现，这样你的应用程序是 web 服务器/反向代理不可知的。然而，假设您想用 nginx 实现相同的头。看起来应该是这样的:

```
add_header X-XSS-Protection "1; mode=block"; 
```

Enter fullscreen mode Exit fullscreen mode

*顺便提一下:如果像我一样，用 ASP.NET Core 做很多开发，那么你可能想看看我的 [OwaspHeaders。核心项目](https://github.com/GaProgMan/OwaspHeaders.Core/)，或者是 [NuGet 包](https://www.nuget.org/packages/OwaspHeaders.Core/)T5】*

### 结论

您现在应该对 OWASP 有了更好的了解，以及他们的工作和建议如何帮助您的应用程序变得更加安全。

向您展示如何实现所有 OWASPs 建议和最佳实践超出了本文的范围，但是希望您现在应该有足够的信息来度过“未知的未知”阶段。

让我们让互联网变得更安全。