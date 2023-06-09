# 开源项目中的数字签名

> 原文：<https://dev.to/jozefizso/digital-signatures-in-open-source-projects-17f5>

*最初发布于[开源项目中二进制文件的数字签名](https://izsak.net/weblog/860/digital-signatures-for-binaries-in-open-source-projects/)T3】*

数字签名提供了文件由可信实体创作的证据。它们允许验证以二进制形式分发的应用程序的完整性。在 Windows 上，软件作者使用 Authenticode 对应用程序及其安装包进行签名，因此 Windows 可以验证是谁创建了该应用程序，并允许 it 管理员创建仅运行受信任的应用程序的策略。

开源应用程序(用于 Windows)通常不进行签名，因为 Authenticode 证书非常昂贵，并且签名的学习曲线非常平滑。

我选择 Certum 来获得我的开源应用程序的证书。Certum 的 Authenticode 证书只需 28 欧元左右。如果你没有任何可以存储证书私钥的兼容智能卡，你可以从 Certum 购买一张，但这使得证书有点贵(出于爱好的目的)-智能卡的价格为 80 欧元，运费为 30 欧元。

从 Certum 订购证书是一个有点复杂和痛苦的过程，因为他们的网站喜欢突然切换到波兰语。Authenticode 证书必须颁发给自然人(或法律实体)，因此该过程不是自动的(就像加密域验证一样)，您必须向他们提供您的身份证和一些公用事业账单或银行对账单来验证您的身份。

开箱即用，您可以使用默认的 SHA1 算法，通过**signtool.exe**使用证书对应用程序(EXE、DLL 和 MSI 文件)进行签名。您必须运行 **proCertum CardManager** 应用程序，这样**signtool.exe**才能在签署二进制文件时与智能卡通信。每次你签署文件时， **CardManager** 会询问证书的 PIN。

## 签字申请

要使用名为`Open Source Developer, Jozef Izso`的证书签署名为`VCardSplitter.exe`的应用程序，请使用以下命令:

```
signtool.exe sign /n "Open Source Developer, Jozef Izso" VCardSplitter.exe 
```

Enter fullscreen mode Exit fullscreen mode

这将只是签署文件。您还必须将时间戳添加到签名中，以便签名即使在证书过期后仍然有效。

```
signtool.exe sign /n "Open Source Developer, Jozef Izso" /fd sha1 /t http://timestamp.verisign.com/scripts/timstamp.dll VCardSplitter.exe 
```

Enter fullscreen mode Exit fullscreen mode

## 使用 SHA256 算法签名

微软要求新的应用程序使用 SHA256 算法进行签名。当您配置**signtool.exe**使用 SHA256 时，您将在签署文件时收到错误。要解决这个问题，打开 **proCertum CardManager** ，点击**选项**，启用 **EV 代码签名——用 minidriver 库替换 CSP**，点击 **Ok** 。这将重新配置系统，SHA256 算法将正确工作。**注:**Certum 的开源开发者证书不是 *EV(扩展验证)证书*。只是用智能卡签名的 CSP 方法只与旧的 SHA1 签名兼容。

启用 minidriver 模式后，您可以像这样签署您的二进制文件:

```
signtool.exe sign /n "Open Source Developer, Jozef Izso" /fd sha256 /tr http://timestamp.comodoca.com VCardSplitter.exe 
```

Enter fullscreen mode Exit fullscreen mode

## 签名 NuGet 包

NuGet 4.6 支持 NuGet 包的签名。它要求签名为 SHA256，因此请确保您启用了**迷你驱动程序**模式。
签约类似于**signtool.exe**流程:

```
nuget.exe sign library.1.0.0.nupkg -CertificateSubjectName "Open Source Developer, Jozef Izso" -Timestamper http://timestamp.comodoca.com 
```

Enter fullscreen mode Exit fullscreen mode

## 在 proCertum CardManager 中切换 CSP 和 minidrive 模式

**proCertum CardManager** 使用名为`cryptoCardRegister.exe`的特殊 app 在 **CSP** 和 **minidriver** 签名模式之间切换。
这可以通过**程序卡管理器**用户界面进行更改:

1.  打开**程序卡管理器**应用程序
2.  点击**选项**按钮
3.  启用或禁用 **EV 代码签名-用 minidriver 库替换 CSP**复选框
4.  点击**确定**

如果您在使用 UI 改变模式时遇到问题，您可以直接从命令提示符执行`cryptoCardRegister.exe`。

要手动启用 **CSP** 模式，使用管理提示运行:

```
"C:\Program Files (x86)\Certum\proCertum CardManager\cryptoCardRegister.exe" csp 
```

Enter fullscreen mode Exit fullscreen mode

要手动启用 **minidriver** 模式，使用管理提示符运行:

```
"C:\Program Files (x86)\Certum\proCertum CardManager\cryptoCardRegister.exe" md 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

数字签名可以确保您的 Windows 二进制文件可以被验证为来自受信任的来源。作为开源开发者，你必须投入大约 100-150 欧元才能获得第一个证书。来自 Certum 的证书将作为自然人颁发给你，它将被命名为**开源开发者，**。在正确地更改了 **CardManager** 的配置后，你可以签署你的 Windows 应用程序、库、安装包以及 nuget 包。签名不能自动进行，因为每次签署文件时都必须输入 PIN。这就禁止了像 AppVeyor 这样的持续集成服务中构建输出的自动签名。

我希望开源开发者和项目可以获得更多的代码签名证书，云服务可以用来在构建过程中自动签名。这将使 Windows 开源库的生态系统更加可信。