# Android 设备出厂时启用了 TCP 端口 5555

> 原文：<https://dev.to/exadra37/android-devices-being-shipped-with-tcp-port-5555-enabled-1ig5>

根据[这篇文章](https://www.securityweek.com/many-android-devices-ship-adb-enabled)一些 Android 设备出厂时启用了 Android 调试桥(ADB)。

对于那些不知道什么是 *ADB* 的人来说，试着把它想象成 Android 设备的外壳，让任何人执行命令并安装他们想要的任何东西，从而允许他们完全控制设备而无需认证。

*ADB* 应该只在通过 usb 端口连接到计算机时工作，但正如安全研究员[凯文博蒙特发现的](https://doublepulsar.com/root-bridge-how-thousands-of-internet-connected-android-devices-now-have-no-security-and-are-b46a68cb0f20)*ADB*正在监听 tcp 端口 5555，因此互联网上的任何人都可以访问它，并接管你的手机、平板电脑、电视或任何其他支持 Android 的设备。

这个漏洞允许攻击者执行各种各样的事情，如加密货币挖掘，窃取凭证，勒索软件，对你正在做的事情进行间谍活动等。

其中一个例子是，我们通过利用这个 *ADB* 缺陷，针对 Android 设备的 [Hide 'N Seek 物联网僵尸网络](https://www.securityweek.com/hide-n-seek-iot-botnet-now-targets-android-devices)。

你对亚行的这次利用有何看法？

# 编辑

我链接的安全研究员凯文·博蒙特的文章似乎误导了评论中指出的 ADB 如何在 Android 设备上工作。

请阅读来自 [Subbu Lakshmanan](https://dev.to/subbramanil) 的[这篇文章](https://dev.to/subbramanil/understanding-android-device-bridge-3i2c)作为对我的文章的回复，并更详细地了解 *ADB* 和 is 安全模型是如何工作的。