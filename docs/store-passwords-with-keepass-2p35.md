# 使用 KeePass 存储密码

> 原文：<https://dev.to/jorinvo/store-passwords-with-keepass-2p35>

***让我给你看看为什么 KeePass 比你的云端密码管理器更好玩。*T3】**

现在是 2018 年，密码仍然是我们尚未解决的痛苦。更大的公司通常依靠支持[单点登录](https://en.wikipedia.org/wiki/Single_sign-on)或类似功能的产品来解决这个问题。但是个人和小公司仍然坚持自己管理密码，或者依赖谷歌、脸书等第三方认证。

我想我不需要解释为什么把你所有的登录信息都交给一家公司并不是一个好主意——尤其是当这家公司的核心业务是收集尽可能多的人的信息时。

如果你是一个关心控制他们的登录和保持登录安全的人，你需要一些方法来管理大量不同和复杂的密码。

这就是为什么有密码管理器。

它们帮助您记住并生成所有数字服务的安全密码。把你所有的登录放在一个地方，并且只需要记住一个密码*(请让它成为一个安全的密码——不要尝试在里面放太多复杂的符号，而是让它变长，用几个单词组成一个句子)*。

这并不是一个新的想法，有足够多的公司提供可靠的服务来帮助你——例如 [1Password](https://1password.com/) 或 [LastPass](https://www.lastpass.com) 。

现在，这些服务只有一个小问题:你需要付费，而且它们让你把所有的密码都放在一家公司的服务器上，灵活性很小，以防你觉得不再信任它们。

所以让我们来看看一个 14 年前的免费开源替代方案: [KeePass](https://keepass.info/) 。

[![Keepass Screenshot](img/a497afbc6260d7713f25d018e58e6fc8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aLO8Ld7C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8lwvwc3q5v49c113n0wi.png)

如果你认为这看起来很可怕，你不是一个人。

那为什么要留着屁股呢？

KeePass 不仅免费开源，而且非常简单。它侧重于管理密码。

它并不试图也为你解决云存储和同步。您的密码简单地存储在一个安全的加密文件中。你怎么处理那个文件取决于你自己。这使得它非常灵活，允许你将它与你选择的任何存储设备结合起来:你可以将这个文件放在 Google Drive、Dropbox 中，或者干脆保存在 USB 驱动器上。这取决于你。

更好的是:KeePass 不一定是你在上面的截图中看到的样子。KeePass 的核心是[。kdbx](https://keepass.info/help/kb/kdbx_4.html) 文件格式。任何开发人员都可以采用这种格式并构建可以使用它的软件！

人们这样做了:

如果你正在寻找一个现代的，漂亮的程序。kdbx 文件，
看看 [MacPass](https://macpassapp.org/) :

[![MacPass Screenshot](img/d723157d5ac674516fec6109a8f156e9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oI5OVgTE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gx4gjf200f1g6ejrxzcf.png)

或者 [KeeWeb](https://keeweb.info/) :

[![KeeWeb Screenshot](img/8680c1f5b3207483fbcceb3f79d97dee.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nMhioGTm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pcn8aejzbamdb0u75zvs.png)

我个人更喜欢 [KeePassXC](https://keepassxc.org/) :

[![KeePassXC Screenshot](img/ea555b2d716e04d789ec7811477985a7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XI2DhHGj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hzrwil4nthepky9v2sn9.png)

它看起来不太漂亮，但使用起来最舒服:

*   搜索、复制、编辑——所有的功能都可以通过快捷键来控制。
*   它支持在您的浏览器中使用全局快捷方式填写密码。
*   它允许存储和解锁 SSH 密钥。
*   它甚至支持为[双因素认证](https://www.securenvoy.com/en-gb/two-factor-authentication/what-is-2fa)设置 TOTP(请为您的所有重要服务启用此功能)，这可以是对[谷歌认证器](https://play.google.com/store/apps/details?id=com.google.android.apps.authenticator2)的一个很好的替代。

当然，你也想在手机上设置密码。你可以的。我对 [Keepass2Android 密码安全](https://play.google.com/store/apps/details?id=keepass2android.keepass2android)有很好的体验。

KeePass 可能不仅对你的个人使用有很大帮助，甚至你的工作场所也能从中受益:与同事分享秘密非常简单灵活。你所要处理的就是共享文件。