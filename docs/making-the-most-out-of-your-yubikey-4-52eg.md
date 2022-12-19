# 充分利用您的 Yubikey 4

> 原文：<https://dev.to/kamushadenes/making-the-most-out-of-your-yubikey-4-52eg>

【Yubikey 5 的新功能即将更新

我已经使用 YubiKey 很多年了。在过去的 5 年里，我的钥匙圈上有一个久经考验的 Yubikey NEO，老实说，经过雨、泥、摔、狗咬和洗衣机，它是无可挑剔的，如果有点磨损的话。那些东西几乎坚不可摧。

我最近在一个会议上得到一个新的，还有一个 YubiKey 4C，我决定把旧的给一个朋友，只是为了传递它。

我决定用 4C 作为我的主要 YubiKey，新的 NEO 作为备用。

我的另一个朋友参加了同样的会议，得到了同样的 YubiKey，并一直缠着我写一份如何使用我的设备的指南。我猜他明白了。

## YubiKey NEO x YubiKey 4C

在我们开始之前，让我们讨论一下我正在使用的 YubiKey 的两个版本之间的区别，以及为什么我选择其中一个作为我的首选。

两个版本都支持:

*   Yubico 的 OTP(类似于由 Yubico 的服务器验证的 HOTP)
*   2 个配置插槽(稍后将详细讨论)
*   誓言-HOTP
*   静态密码
*   挑战-回应
*   U2F
*   峰值反向电压（Peak Inverse Voltage 的缩写）
*   OpenPGP SmartCard

YubiKey NEO 的主要区别是支持 NFC，这使得它可以在 Android 和 iOS 上使用。此外，OpenPGP 小程序支持最长 2048 位的密钥。它的连接是普通的 USB。

另一方面，YubiKey 4C 不支持 NFC，但允许最长 4096 位的 OpenPGP 密钥。它的连接是 USB C，并且具有更小的外形。

我决定使用 4C 作为主要选择，因为它的密钥长度以及我并不真正使用 NFC 的事实(另外，我的 Pixel 手机有 USB C 连接，它与 [OpenKeyChain](https://www.openkeychain.org/) 应用程序配合非常好，后者也支持 NFC)。较小的外形也是一个很好的优势，因为我已经有一个很大的钥匙链，因为我的莱杰纳米。

除此之外，拥有一个支持比默认更多连接的备份，以及一个更通用的 USB 端口，可以在紧急情况下提供更大的灵活性。

我会在所有适用的地方添加一个备份部分，这样你就知道你应该怎么处理你的第二辆 YubiKey，如果你决定要一辆的话。我强烈建议你这样做，以免被锁在你的东西外面。

## U2F

*U2F 是一种开放的认证标准，互联网用户只需一个安全密钥即可安全地访问任意数量的在线服务，无需驱动程序或客户端软件。*

把它想象成基于硬件的第二个因素。现在，这并不意味着被用作单一的认证因素，不像它的精神继承者 [FIDO 2，](https://www.yubico.com/solutions/fido2/)是为了完全取代密码。

今天大多数主要服务都支持 U2F，并承诺在 FIDO 2 推出时支持它。

我在脸书、G Suite、GitHub、GitLab、DropBox、BitBucket 和其他一些我不记得的网站上启用了它。

你可以在 Yubico 的网站上获得支持 U2F 的服务列表，但这绝不是一个详尽的列表。检查您使用的服务，看看您是否可以启用 U2F。

Google Chrome 是唯一完全支持它的浏览器，但您可以通过在 about:config 中将* * security . web auth . u2f * *设置为 true 来在 Firefox 上启用实验性支持。我使用了它，到目前为止，它运行得完美无缺。

**备份:**你可以用 U2F 添加任意多的硬件密钥，所以只要确保当你启用它时，你的两个密钥都在手边，你就可以添加两个。

查看下面一些 GitHub 上 U2F 的截图。

[![](../Images/353eec8b660467537855c5b3ded5cd4b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--34NJ3dOG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hadenes.io/uploads/1_a9offp3y8ocz6uexoghm2w.png)

*注意“2 个安全密钥”*

[![](../Images/7349ca16680d8be63e29a7194c517823.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MmludC0e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hadenes.io/uploads/1_sp7fy9hysjzpkwmishugcw.png)

第一个是我的 YubiKey 4C，第二个是我的 NEO。

*帕姆 U2F*

您可以将 YubiKey 的 U2F 功能与 PAM 配合使用，以更安全的方式登录您的机器。

不幸的是，除了在客户机和服务器上做一些修改之外，这在 SSH 上并不真正起作用，所以请坚持使用我将在下面解释的 GPG 公钥认证。

不过，在你的本地机器上安装它还是很酷的，为此我遵循了吟游诗人詹姆斯的指导，让它在我的 Arch Linux 上运行。考虑到 PAM 几乎在任何地方都是默认的，这应该可以在任何修改过的发行版上工作。

**备份:**按照备份密钥上的相同过程，让它在您的主机上工作。

## TOTP

一些版本之前(我不知道具体是什么时候)，YubiKey 通过给其中一个插槽分配一个誓言-HOTP 配置来支持存储 TOTP 秘密。

令人高兴的是，这已经不再需要了，新的 YubiKeys 能够在不驻留在任何配置插槽中的单独存储中存储多达 32 个 TOTP 秘密。

现在，这个 32 的限制对大多数人来说可能足够了，但对我来说不是。我启用了许多 totp，所以我所做的是使用 [Authy](https://authy.com/) 来存储所有 totp(启用同步，这样我就不会丢失它们)，并且只在 YubiKey 上存储重要的内容，确保它们也在 Authy 上。

添加新的秘密时，只需用 Authy 和 YubiKey 应用程序扫描二维码，就可以了。

对于你之前拥有的秘密，如果你没有二维码或秘密的备份，并且你使用了 Authy，你可以使用[这个提示](https://randomoracle.wordpress.com/2017/02/15/extracting-otp-seeds-from-authy/)或[这个](https://gist.github.com/Ingramz/14a9c39f8c306a2d43b4)从 Authy chrome 扩展中转储秘密。我用的是后者，效果很好。

由于我使用 [i3wm，](https://i3wm.org/)我寻找一种方法将这个 TOTP 与我的桌面集成在一起，这样我就可以将我的 2FA 键一键组合起来，前提是我的 YubiKey 已连接。[埃姆伦](https://github.com/emlun)也有同样的想法，并创造了 [yubikey-oath-dmenu，](https://github.com/emlun/yubikey-oath-dmenu)我一直沿用至今，非常满意。在 i3 中，我将它分配给 Super+T 快捷方式(对于 Token ),并将其复制到剪贴板。

```
bindsym $mod+t exec yubikey-oath-dmenu --clipboard clipboard --notify 
```

Enter fullscreen mode Exit fullscreen mode

对于移动设备，你可以使用 Yubico 的认证应用程序，除非你需要 Android 来做这件事。它还可以与 YubiKey 的 NFC 和 USB C 版本配合使用。

对于桌面，你有 [yubioath-desktop](https://github.com/Yubico/yubioath-desktop) 。

备份:我发现每次添加密钥到主密钥时都要添加密钥，这太烦人了，所以我在这里只使用 Authy 进行备份。

## PIV

我不怎么用 PIV 智能卡小程序，选择用适合我的 OpenPGP 小程序。但是一个并不能阻止你使用另一个，Yubico 的网站给出了一些很好的例子来说明你可以在哪里使用 PIV，例如 OSX 代码签名或 Docker 硬件签名。

即使不使用它，用默认值也不太好，所以我按照 [Yubico 的指南](https://developers.yubico.com/PIV/Guides/Device_setup.html)使用 [yubico-piv-tool](https://developers.yubico.com/yubico-piv-tool/) 更改它的 PIN、PUK 和管理密钥。

```
yubico-piv-tool -achange-pin -P123456 -NXXXXXX
yubico-piv-tool -achange-puk -P12345678 -NXXXXXXXX
yubico-piv-tool -aset-mgm-key -nXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX 
```

Enter fullscreen mode Exit fullscreen mode

**备份:**遵循相同的步骤，这样您就不会在默认状态下进行备份。

## GPG

在我看来，OpenPGP 智能卡 applet 是 YubiKey 的亮点。它允许做很多事情，从常规的文件或电子邮件签名，到 SSH 公钥认证和 GIT 提交签名。

有很多关于如何设置的指南，所以我将在这里留下一个好的， [drduh 的指南，](https://github.com/drduh/YubiKey-Guide/tree/1ad37577db92726eadde4dc302a6f982ba7e82dc)并指出我所做的。

我选择创建一个 4096 主密钥，并将其备份在一个安全的地方(不在我的常规位置)。gnupg keychain)，然后我上传了 4096 个子密钥到我的主 YubiKey 和 2048 个子密钥到我的备份 YubiKey。

在以前的版本中，我提到过子密钥是直接从主密钥中派生出来的，你可以使用其中任何一个，它们都会有效。这是错误的，所以应该道歉。共享您的主密钥公共部分以供日常使用。

**备份:**如前所述，只需将子密钥上传到备份密钥就足以让它在主密钥工作的任何地方工作。

*宋承宪*

对于 SSH 登录，在您配置了 GPG 密钥之后，您可以运行:

```
gpg2 --export-ssh-key KEY_ID 
```

Enter fullscreen mode Exit fullscreen mode

您将获得一个 SSH 公钥，您可以将它放在任何服务器的 authorized_keys 上并让它工作，前提是您在客户机上正确配置了 gpg-agent。下次登录时，它会要求您输入 6 位 GPG 个人识别码来进行身份验证。

*去*

要让它处理 git 提交，那就更简单了。首先，您必须使用
启用提交签名

```
git config --global commit.gpgsign true 
```

Enter fullscreen mode Exit fullscreen mode

然后，您必须指定您将使用哪个密钥来签署带有
的提交

```
git config --global user.signingkey KEY_ID 
```

Enter fullscreen mode Exit fullscreen mode

就这样，你准备好了。下次你执行*git 提交*时，它会要求你输入 GPG PIN 码(6 位数)并签署你的提交，在 GitHub 上留下一个漂亮的验证徽章。

[![](../Images/f5fb16c6bad7ed03686c0b2ca249139b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5JFkopMD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hadenes.io/uploads/1_rzayylq8sj9g9mw_4v62ua.png)

GitHub 还提供了一个关于提交签名的指南，你可能想看看。

## 结论

这就是我如何使用我的 Yubikeys。我发现它们非常有用，而且非常耐用，在一个非常小的设备上集成了许多功能。这不是一个付费的帖子，所以我猜这可以让你感觉到我是多么欣赏 Yubico 在这些设备上的工作。

希望你喜欢它，如果你有任何问题，请随时联系我。