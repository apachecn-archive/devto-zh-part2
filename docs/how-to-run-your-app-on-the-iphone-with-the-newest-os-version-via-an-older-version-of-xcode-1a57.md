# 如何通过旧版本的 Xcode 在装有最新操作系统版本的 iPhone 上运行应用程序？

> 原文：<https://dev.to/nikola/how-to-run-your-app-on-the-iphone-with-the-newest-os-version-via-an-older-version-of-xcode-1a57>

[![](img/c2917cb6c489c57893a94af9c8efb17c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uAD13vpz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/eb5uo0q24j0fl37jboc3.jpg)

*最初发布于[我的博客](http://www.nikola-breznjak.com/blog/ios/run-app-iphone-newest-os-version-via-older-version-xcode/)T3】*

在这篇短文中，我将解释如何在装有最新操作系统(在我写这篇文章的时候是 11.4.1)的 iPhone 上通过旧版本的 Xcode(在我的例子中是 9.2)运行你的应用程序。

假设您将 iPhone OS 更新到最新版本，然后在旧版本的 Xcode 中打开项目并尝试运行它。您很可能会得到类似以下的错误(版本号可能会有所不同):

> 在带有 Xcode (9.2 9C32c)的 iPhone X (11.1)上找不到设备支持文件

这两个 StackOverflow 帖子已经解决了这个问题:

*   [iOS 11.3 不支持 Xcode，Xcode 9.2 需要 9.3](https://stackoverflow.com/questions/49720178/xcode-not-supported-for-ios-11-3-by-xcode-9-2-needed-9-3/5059639)
*   [如何修复 Xcode 错误:在装有 Xcode Beta (9.2 9C32c)的 iPhone X (11.1)上找不到设备支持文件](https://stackoverflow.com/questions/47119186/how-to-fix-xcode-error-could-not-locate-device-support-files-on-iphone-x-11-1)

解决方案基本上是进入 Github 项目的[，下载设备支持文件，并将它们放在 Xcode 应用程序的`DeviceSupport`文件夹中。](https://github.com/filsv/iPhoneOSDeviceSupport)

你可以进入你的`Applications`进入`DeviceSupport`文件夹，找到 Xcode 应用，右击它，选择`Show Package Contents`。然后导航到`Contents > Developer > Platforms > iPhoneOS.platform > DeviceSupport`并将下载文件的内容粘贴到那里。

你现在要做的就是重启 Xcode，再次运行 app。

⚠️:这是一个潜在的棘手的聚会；你可能意识到即使你已经从 Github 下载了正确的支持文件(并重启了 Xcode ), Xcode 仍然不满意。比方说，你已经下载了`11.4 (15F79)`，但是在重启并再次运行后，你会得到一个类似于`11.4.1 (15G77)`的错误提示。解决方法是将文件夹重命名为这个名称，重新启动 Xcode，瞧，它工作了！

希望这对某人有所帮助💪

*编辑:*感谢 Ikem Krueger 的评论，这里是[最新设备支持文件的链接](https://stackoverflow.com/questions/49564533/xcode-device-support-for-11-3-15e5216a/49808795#49808795)。