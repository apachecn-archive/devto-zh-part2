# 为什么您应该实践安全性以更好地保护您的应用程序。

> 原文：<https://dev.to/shostarsson/why-you-should-practice-security-to-better-secure-your-application-29h5>

> 对于全世界的安全专业人员来说，这是一个有趣的时代。他们的工作是制造头条新闻。

但这也非常可怕，因为这意味着许多组织由于其产品和基础设施上的潜在安全缺陷而处于危险之中。

它经常成为头条新闻。因此，当它到达一个科技博客或一份非科技类报纸时，这意味着缺陷或安全漏洞已经存在很长时间了。并且可能被恶意的个人使用。

作为一名安全专家，我的工作是测试一些产品和基础设施的安全性，并尝试入侵它们，以便我们可以识别潜在的缺陷，然后在服务真正被入侵之前纠正它们。

#### 因此，您必须从被动安全转向主动安全。

这就是现在所谓的**安全设计**流程，目前正在公司中发展。

这就是理论。但在实践中，很难在所有产品和服务上通过设计来实现安全性。

* * *

#### 为什么这个这么重要？

安全性是您的组织绝对应该考虑的问题，因为设备和服务总是连接到互联网，您向全世界开放您的服务。这意味着坏人可以直接访问您的基础设施。

如果您不遵循一些非常标准的安全原则，不每天关注安全性的人不会知道访问基础架构的某些关键部分是多么容易。

* * *

#### 我们如何遵循行业最佳实践来实现这一目标？

业界有很多关于安全实践的指导方针可以遵循。

它涵盖了组织部分(这是非常重要的部分)并转移到更技术性的部分。

ISO/IEC 27000 系列标准有助于组织保护信息资产的安全。

EBIOS 是一种对信息系统相关风险进行分析、评估和采取行动的方法。

这两种方法都侧重于对您的信息系统和组织的风险分析，以及如何保护您的数字资产。

开放 web 应用安全项目(Open Web Application Security Project)是一个在线社区，提供 Web 应用安全领域的免费文章、方法、文档、工具和技术。

OWASP 有许多关于安全性的有趣出版物，以便安全地开发您的应用程序，在安全性方面进行适当的测试，同时部署您的服务并对安全漏洞做出响应。

但是我将只谈论最有趣的 OWASP 出版物《你是安全方面的初学者》。

定期更新的是 OWASP Top 10。它代表了对 web 应用程序最关键的安全风险的广泛共识。

这意味着，如果你将这些风险最小化，你就掩盖了许多非常常见的缺陷。因此，对于攻击者来说，让您的系统瘫痪的工作要复杂得多。

在本文的剩余部分，我将重点讨论 Android 手机。

OWASP 有一份关于手机十大缺陷的文档。

* * *

#### 让我们从安卓开始

你可以用一部简单的安卓手机做很多事情。测试应用程序的安全性并不需要让您的 Android 手机 root。

让我们回顾一下 OWASP 十大手机。

*   M1 — [平台使用不当](https://www.owasp.org/index.php/Mobile_Top_10_2016-M1-Improper_Platform_Usage)
*   M2 — [不安全的数据存储](https://www.owasp.org/index.php/Mobile_Top_10_2016-M2-Insecure_Data_Storage)
*   M3 — [不安全的通信](https://www.owasp.org/index.php/Mobile_Top_10_2016-M3-Insecure_Communication)
*   M4 — [不安全认证](https://www.owasp.org/index.php/Mobile_Top_10_2016-M4-Insecure_Authentication)
*   M5 — [加密技术不足](https://www.owasp.org/index.php/Mobile_Top_10_2016-M5-Insufficient_Cryptography)
*   M6 — [不安全授权](https://www.owasp.org/index.php/Mobile_Top_10_2016-M6-Insecure_Authorization)
*   M7 — [客户端代码质量](https://www.owasp.org/index.php/Mobile_Top_10_2016-M7-Poor_Code_Quality)
*   M8 — [代码篡改](https://www.owasp.org/index.php/Mobile_Top_10_2016-M8-Code_Tampering)
*   M9 — [逆向工程](https://www.owasp.org/index.php/Mobile_Top_10_2016-M9-Reverse_Engineering)
*   M10 — [无关功能](https://www.owasp.org/index.php/Mobile_Top_10_2016-M10-Extraneous_Functionality)

在这之后，我通常会首先检查不安全的数据存储，因为这是最容易的部分。你只需要下载应用程序，注册，玩一会儿就可以填充数据，然后你查看存储的文件，从安全角度寻找任何有价值的东西。

为了做到这一点，你将需要谷歌的 [Android 调试桥](https://developer.android.com/studio/command-line/adb) ( **亚行**)。

你现在可以从 Play Store 下载一个应用程序，或者从你的手机上下载一个应用程序，这样你就可以开始玩它并测试它的安全性。

在我的下一篇文章中，我将测试一个有安全缺陷的应用程序，即[不安全的银行](https://github.com/dineshshetty/Android-InsecureBankv2)，这样你就可以得到一些关于如何进行安全测试的提示。

* * *

这是我在 dev.to 上的第一个帖子。我希望你会喜欢它，并且你学到了一些东西。请在评论中告诉我，这对我意义重大。