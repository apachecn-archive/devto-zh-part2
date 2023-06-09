# iOS 远程调试-操作指南

> 原文：<https://dev.to/rickey_oak/ios-remote-debugging---a-how-to-guide-2onh>

# iOS 远程调试-操作指南

当涉及到测试和调试 iOS 应用程序时，模拟器不能完全取代在实际设备上测试它们。嗯，如果你有连接 iPhone，iPad 等的可能性。直接到你的开发机器上，你不用担心。但是，如果你需要检查一个无法与你的 Mac 物理连接的设备上运行的应用程序呢？

## 无线调试

苹果在 Xcode 9 中增加了无线调试的选项。但是在你开始之前，你应该记住几个条件:

*   Mac 电脑和 iOS 设备应该共享同一个网络。

*   您应该使用 Xcode 9.0 或更高版本、macOS 10.12.4 或更高版本以及 iOS 11.0 或更高版本。

好了，现在让我们继续实际的步骤，这将帮助您启用远程调试:

1.  在 Xcode 上打开您的项目。
2.  选择窗口>设备和模拟器。
3.  在出现的窗口中，单击“设备”。
4.  通过 USB 电缆将您的设备连接到 Mac。
5.  在左栏中，选择设备，并在详细信息区域中，选中“通过网络连接”框。

[![iOS remote debugging](img/8deca7e0e944d247df3ed2373b7db8dd.png "Wireless debugging Xcode")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PSw5y5Qj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.flexihub.cimg/upload/flexihub/articles/ios/xcode_remote_debugging.png)

现在，Xcode 将与您的设备配对。如果 Xcode 可以通过网络与设备连接，左侧栏中的设备旁边会出现一个网络图标，您可以安全地断开设备的连接。

## 如何在远程设备上调试 iOS 应用

如果不满足第一个条件，并且设备不与 Mac 共享同一个网络，或者甚至位于远离机器的地方，则无法借助 Xcode 进行远程调试。

但是，有一个变通办法！您仍然可以借助 USB over Ethernet 重定向软件(例如 [FlexiHub](https://www.flexihub.com/) )来调试此类设备。它旨在虚拟化 USB 设备，(如 iPhone，iPad，iPod 等。)并通过网络重定向它们。该软件适用于局域网、以太网和互联网。

1.  要建立到 iOS 设备的远程连接，[下载并在运行调试器的本地机器和与 iOS 设备物理连接的远程计算机上安装 Flexihub](https://www.flexihub.com/download.html) 。
2.  在本地机器上启动 FlexiHub 并注册一个帐户——点击“注册”链接并按照说明进行操作。登录帐户。
3.  在远程计算机上启动软件，并使用相同的凭据登录到 FlexiHub 帐户。
4.  在本地 Mac 上的软件界面中，您会看到 iPhone/iPad 可用于连接。点击“连接”。

[![debug iOS app on device](img/c7d139c8003ed652682c0cfbeb0fd2dd.png "FlexiHub")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lnU-p0FV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.flexihub.cimg/articles-landing/fh.jpg)

就是这样。现在，您可以从本地计算机访问远程 iOS 设备，并开始调试过程，就像设备直接连接到您的机器一样。