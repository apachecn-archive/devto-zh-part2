# Unity 用蓝牙搭建的 iOS 上最好的 CI 工具是什么？

> 原文：<https://dev.to/wajiwaji/what-is-the-best-ci-tool-on-ios-built-by-unity-using-bluetooth-2lo1>

我正在使用多个蓝牙设备开发一个 Unity 产品(部署为 iOS 应用程序)。我们的团队有一个与自动化建设，测试，部署相关的未来工作。在谷歌搜索结果中，我发现了许多 CI 工具和一些展示如何将它们应用于 Unity app 的示例和演示。但是我不知道什么工具最容易用于开发。我是 CI 工具的初学者，我有使用 CircleCI for web service (Python)的经验。

尝试应用什么工具最好？我们的应用程序包含这些要求。

*   Unity(为 iOS 应用程序构建)
*   通过 [iOS 库](https://github.com/Polidea/RxBluetoothKit)使用蓝牙
*   通过 iOS SDK 使用 AWS Cognito(用于身份验证)