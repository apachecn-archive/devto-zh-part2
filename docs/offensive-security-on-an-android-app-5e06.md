# Android 应用上的攻击性安全

> 原文：<https://dev.to/shostarsson/offensive-security-on-an-android-app-5e06>

对服务、应用程序或服务器的安全攻击可以分为 7 个不同的步骤。

1.  侦察
2.  扫描
3.  访问和升级
4.  漏出
5.  支持
6.  攻击
7.  困惑

即使当我们对网站或服务器进行攻击时，这些步骤非常清楚，我们也可以遵循类似的模式对移动应用程序进行攻击，从而评估其安全性。

* * *

### 1。安卓应用上的侦察

在本文的剩余部分，让我们关注一个 Android 应用程序(但在 iOS 上模式非常相似)。

一旦您定义了要测试的应用程序，第一步就是要了解该应用程序是如何工作的，以及构建该应用程序的组织是如何工作的，因为这可以为我们在接下来的步骤中应该将精力放在哪里提供很好的提示。

Android 应用程序基本上是压缩文件。它的扩展名是“apk”。

* * *

### 2。扫描

首先，我们必须*扫描*该应用程序，以了解其内部工作原理。
在 Android 上这样做是很常见的，谷歌正在用 [Android 调试桥](https://developer.android.com/studio/command-line/adb)帮助我们评估应用程序的安全性。
当连接到电脑时，Android Debug Bridge 允许我们向设备发送命令来执行一些操作。
我强烈推荐你*阅读更多关于 Android Bridge 提供的可能性的*。
在一个 Android 应用上，*扫描*基本就是把应用设置成调试模式，重新打包，在设备上玩。
我正在使用 [apktool](https://ibotpeaches.github.io/Apktool/) 来做这件事。您应该将它安装在您的设备上，并查看使用 apktool 的文档。

然后，您可以访问存储器中的所有信息，并检查是否有有价值的东西泄露了(密码、cookies 等)。它是来自 [OWASP 移动 Top10](https://www.owasp.org/index.php/Mobile_Top_10_2016-Top_10) 的[不安全本地存储](https://www.owasp.org/index.php/Mobile_Top_10_2016-M2-Insecure_Data_Storage)。
我们正在开发 apk，非常有趣的是*对 apk* 进行反编译，看看它的*源代码*，然后看看代码库中是否有一些有价值的东西，从密码、密钥到阻止用户特定行为的机制。我个人使用的是 Jadx，这是一个非常流行的 Java 反编译器。

[![Jadx Graphical User Interface](img/50059a9abb8a46b091512dd608b3ce66.png)T2】](https://camo.githubusercontent.com/2b504e792e3ddb1d95094b44e01ab21e101b6f56/68747470733a2f2f692e696d6775722e636f6d2f6839313749425a2e706e67)

然而，比起图形用户界面，我更喜欢命令行 Jadx。

要使用 Jadx 反编译 Android 应用程序，您只需运行以下命令:

```
> jadx --deobf ~/app/javaFiles/ ~/insecureApp.apk 
```

Enter fullscreen mode Exit fullscreen mode

一旦从本地存储中获得了源代码和文件，就可以使用 [grep](http://pwet.fr/man/linux/commandes/grep) 命令在文件中查找特定的字符串。

```
> grep -Eo '(http|https)://["]+' -R . 
```

Enter fullscreen mode Exit fullscreen mode

寻找“管理”、“密码”或任何互联网链接可以给你很多非常有价值的信息。

如果你认为你的密码被公司很好地加密和保存，请再想想雅虎、Tinder 或…
的大规模泄露，这在 Android 应用程序中非常相似。一些应用程序将您的登录名和密码以纯文本形式存储在本地存储中。

现在我们已经有了源代码和设备上可调试的应用程序，我们可以访问它了。

* * *

### 3。接近

[![“Credentials.xml” in the “shared_preferences”. Really?!!](img/7177e390bb0493755802d4d85154e340.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ICovLhMT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2A3a4V67Ee51qYE71JknMEiA.png)

正如你在我的一次渗透测试的截图中看到的，一旦我们通过 adb shell 进入“shared_preferences”文件夹，我们就可以看到一个“Credentials.xml”文件。我可以在里面找到以明文形式存储的登录名和密码。😱你可以想到一个应用程序/恶意软件，它会从用户的手机中过滤这些数据，然后非常容易地创建一个登录/密码数据库。这确实是一个非常严重的缺陷。🙅

类似的事情是在我们使用应用程序时查看来自设备的日志。

```
> adb logcat > ~/grep-result.txt 
```

Enter fullscreen mode Exit fullscreen mode

然后使用 grep 命令在日志中查找特定的字符串。

```
> grep password ~grep-result.txt > ~grep-password-result.txt 
```

Enter fullscreen mode Exit fullscreen mode

我们已经快速了解了本地存储，为了继续“访问和上报”步骤，我们将了解网络。

* * *

我使用 [mitmproxy](https://mitmproxy.org/) 或 [bettercap](https://www.bettercap.org/) 来查看应用程序和设备之间的网络调用。

Owasp ZAP 和 [Burp](https://portswigger.net/burp/) 也是非常流行的工具，它们有一个图形用户界面。选择你喜欢的。

> Owasp ZAP

[![Owasp ZAP](img/a61d4187ced136796557f095ecaf425e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xU-uffHd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.owasp.oimg/thumb/7/78/ZAP-ScreenShotHelp.png/400px-ZAP-ScreenShotHelp.png.jpeg)

* * *

> 打嗝代理

[![Burp Proxy](img/78b9e0260fef30cb6d8eb8b327f6e466.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Sc6Oh-2V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://portswigger.net/sc/UsingBurp_BurpTools_1.png)

* * *

让我们考虑一下，我们将从查看 *HTTP 请求和响应*开始。我将写一篇文章来解释如何从 https 请求中获取信息。

正确设置设备后，跟踪设备的互联网流量到您的电脑。

在 Android 上，它位于:

> /设置/网络/Wifi/您的 Wifi ssid/修改网络/代理手册

你只需要在你的电脑上安装 mitmproxy，启动它，然后在你的设备上使用这个应用程序。

您应该会很快看到应用程序的所有请求和响应。

[![Here is a extract of mitmproxy from the Dailymotion application](img/d2c16f8864dc4872f9671624399ddeca.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EXxUZLNv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2ABkFuwBK6dcsT0Mm_J4vbOA.png)

我喜欢将所有的响应/请求放在一个文件中，然后用 grep 和 Wireshark 自动学习。

然后你必须用你的大脑和你的经验来寻找特定的字符串，并尝试破解应用程序。

这在很大程度上取决于应用程序及其行为。

* * *

### 下一步— 4、5、6、7

我将不讨论第 4、5、6 和 7 步，因为它们对于安全评估来说不是必需的，而是作为真实攻击的一部分。

* * *

*如您所见，无论您开始评估哪种应用，这种设置都非常相似。
这就是为什么我做了一个 Go 应用程序来自动完成所有的设置和静态攻击*

*它叫做 [AndroSecTest](https://github.com/Shosta/androSecTest) ，在 Github 上有售。* 

## *[![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--i3JOwpme--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev.to/assets/github-logo-ba8488d21cd8ee1fee097b8410db9deaa41d0ca30b004c0c63de0a479114156f.svg)[Shosta](https://github.com/Shosta)/[androSecTest](https://github.com/Shosta/androSecTest)*

### *从这个 app 中，连接一部手机，从中提取任何 app，反编译，解压，移除证书锁定，重新打包。同时，对其进行静态和动态分析。*

<article class="markdown-body entry-content container-lg" itemprop="text">

*[![Ask me anything](img/f38375515ef01b773629ceba17ffaae6.png)](https://github.com/Shosta/androSecTest/stargazers)[![Maintained](img/81116ae2c8c78934156f66f8872a36f3.png)](https://github.com/Shosta/androSecTest/stargazers)[![GitHub stars](img/ab208ac81e6cf807434ac458f72f9240.png)](https://github.com/Shosta/androSecTest/stargazers)[![GitHub forks](img/51b7fd2b4a750d62e647d93c49268706.png)](https://github.com/Shosta/androSecTest/network)[![GitHub license](img/f1b2e7dbcae465684900fa3a04dfa1b3.png)](https://github.com/Shosta/androSecTest/blob/master/LICENSE.md)[![Pentest](img/37bcc7d5e3c66d48b39893e2cdd480d9.png)](https://github.com/Shosta/androSecTest/stargazers)*

# *Android-Static-Security-Audit*

*下面是一个快速小抄，用来测试 AndroSecTest 正在做的一个 Android app 的安全性。*

 *你可以在 Youtube 上快速浏览一下这个应用程序是如何测试一个 Android 应用程序的:[https://youtu.be/zzyTFjnwolo](https://youtu.be/zzyTFjnwolo)

## 最简单的尝试方式

### 使用 docker 容器

1.  构建 Docker 容器，其中已经安装了所有的依赖项和工具。

    > `docker build .`

2.  连接你的安卓设备

    2.1。确保“adb 服务器”是运行在主机上的**而不是**，因为 android 手机在给定时间只能连接到一个 adb 服务器。

    2.2

    。MacOS 上主机设备到容器的 USB 连接不工作，所以暂时只在 Linux 主机上工作。

3.  运行 Docker 容器

    > `docker run -it --privileged -v /dev/bus/usb:/dev/bus/usb "The Container ID"`

    3.1`-it`在这里，这样我们就可以进行交互式会话。

    3.2

    。`--privileged`…* </article>

*[对 GitHub 的看法](https://github.com/Shosta/androSecTest)* *它仍在开发中，但我希望能很快向公众发布。

T101】*

* * *

视频由 [Wild & Secure](https://wildnsecure.com) 制作，这是您的安全和房地产咨询公司。
如果你想每周收到高质量的安全内容，请在我们的网站上订阅我们的时事通讯[。](https://wildnsecure.com/contact)