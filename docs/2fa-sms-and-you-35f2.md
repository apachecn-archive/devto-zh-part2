# 2FA，短信，你呢

> 原文：<https://dev.to/juliaferraioli/2fa-sms-and-you-35f2>

*这是从[我的博客](https://www.juliaferraioli.com/blog/2018/08/2fa-sms-you/)交叉发布的。*

[双因素认证(2FA)](https://en.wikipedia.org/wiki/Multi-factor_authentication) 或多因素认证(MFA)是在常规密码保护之外保护帐户的方法。通常，我们认为 2FA 是你知道的东西(你的密码)和你拥有的东西(设备)。这个想法是，如果您破坏了一个，您仍然不能访问受保护的资源。

[![Image of an antique lock](img/37bbf40199b43f9df7f4aaaa2e770226.png "Image of an antique lock")](https://res.cloudinary.com/practicaldev/image/fetch/s--pY2MEk7d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://juliaferraioli.com/blimg/2fa-sms-you/lock.jpg)T3】

<center>**Antique, ornate lock**</center>

一个由键盘和 ID 卡阅读器保护的房间是 2FA 的一个很好的例子。你也许能猜出密码，但你还需要一个有效的身份证才能进入上锁的房间。

> 生物识别呢？好问题。从技术上来说，仍然是你知道的东西(密码)和你拥有的东西(视网膜/指纹/等等)，但存储的生物特征让我紧张。我避开。

(部分)2FA 选项:

*   [基于时间的一次性密码](https://en.wikipedia.org/wiki/Time-based_One-time_Password_algorithm) (TOTP)
*   [通用第二因子](https://en.wikipedia.org/wiki/Universal_2nd_Factor) (U2F)
*   [短信](https://en.wikipedia.org/wiki/SMS)

从较高的层面来看，以下是每种方法的工作原理:

**TOTP** 通过手机上的应用程序、浏览器扩展或命令行工作，并在你登录账户时为你生成一个限时代码。

U2F 的工作原理是配置一个设备(你可能听说过 Yubikey)，并在每次登录账户时提供该设备。

短信的工作原理是获得一条带有代码的短信，你每次登录你的账户时都要输入该代码。

2FA 实际上并没有那么复杂。是的，这是一个额外的步骤，但值得配置以保护您的帐户。

然而，并不是所有的 2FA 都是平等的。我不得不帮助不止一个人恢复受基于 SMS 的 2FA 保护的帐户。SIM 卡劫持是指有人接管了你的电话号码，因此可以拦截你的电话和短信。一些使用基于短信的 2FA 的网站/应用程序将允许仅通过短信或语音重置密码。如果有人劫持了你的 SIM 卡，他们不需要知道(或者猜测)就可以进入那些账户。)你的密码。

这就是为什么我建议不要使用基于短信的 2FA。如果你反对随身携带额外的设备，在你的手机上安装一个 TOTP 应用程序(比如 [Google Authenticator](https://support.google.com/accounts/answer/1066447) )。让我们面对现实吧，你可能会带着它到处走。U2F 是我的首选，但如果你刚刚开始使用 2FA，那可能是一个更大的障碍。

> 这里有一个关于如何设置 2FA 的很好的[通用指南](https://www.theverge.com/2017/6/17/15772142/how-to-set-up-two-factor-authentication)。如果你想走 U2F 路线，你可能会想要一辆 [YubiKey](https://www.yubico.com/products/yubikey-hardware/) 。

其他最佳实践:

*   恢复码-下载，存储。
*   保持你的密码安全——2FA 不能代替一个好的密码。
*   使用密码管理器——如果你需要使用多台设备，可信的密码管理器可以存储 TOTP 种子。
*   异地备份——对于真正的偏执狂，将备份代码、设备存储在异地、离线的位置，比如保险箱。

**问题**:如果一个提供商只允许基于短信的 2FA 怎么办？

很好的问题，但是没有简单的答案。这取决于很多因素。首先，评估你是否真的需要那个账户。如果不是，那就没有问题。如果您确实需要该帐户…

评估你的风险状况。如果这个账户被泄露，会造成多大的损失？如果它没有任何作用，我可能不建议一开始就用短信来保护它。如果它有机会造成损害，那么我会调查提供商如何允许帐户恢复的政策，并基于此打电话。肯定仍然使用一个独特的密码，并明确要求其他 2FA 选项！

> 需要针对提供商的建议来保护您的帐户？twofactorauth.org 是了解他们支持什么以及如何实现支持的好方法。

有其他问题吗？让我知道，我会试着回答他们。