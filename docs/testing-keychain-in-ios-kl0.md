# 在 iOS 中测试钥匙串

> 原文：<https://dev.to/onmyway133/testing-keychain-in-ios-kl0>

[![](img/9ddda858115d46389c5019e9c49ae1fc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---CJd4Fo1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2AKt0PVlvJfhRO89zM.jpg)

今天我将[钥匙链](https://github.com/hyperoslo/Keychains)升级到 swift 4，借此机会修复测试。测试在 macOS 上通过了，但是在 iOS 上，我得到了-25300 错误

```
var status = SecItemCopyMatching(query as CFDictionary, nil) 
```

Enter fullscreen mode Exit fullscreen mode

这是因为测试目标没有钥匙串授权。但这是一个框架，我如何添加权限🤔解决方案是使用一个[测试主机](https://developer.apple.com/library/content/documentation/DeveloperTools/Conceptual/testing_with_xcode/chapters/05-running_tests.html)来托管 XCTest 测试。看我的[拉请求](https://github.com/hyperoslo/Keychains/pull/14)

**创建测试主机目标**

[![](img/9594306f57bf46f76ecba7eddf098291.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xF4DPG_f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2AW6oTAvwTo-0_XFzW.png)

首先创建一个 iOS 应用程序作为测试主机

**启用钥匙串功能**

然后启用钥匙串功能，让 Xcode 自动为您创建授权文件。请注意，您可以只输入钥匙串组。你不需要去苹果开发者仪表盘配置任何东西

[![](img/de0761ec1c3b1dc1692bcdbc60efe897.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--21UurOd9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2Aagu6L6QUNHjWEQg1.png)

**指定测试主机**

然后在测试目标中，使用$(build _ PRODUCTS _ DIR)/Test Host _ iOS . app/Test Host _ iOS 指定测试主机

[![](img/1e1dff6c564182bb7ea9f52e58920df6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GGKBdSPe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2AmxADBtFEGGJtH9xL.png)

现在再次运行您的测试，它应该会通过🎉

原帖[https://medium . com/fantageek/testing-keychain-in-IOs-9360 C1 f38 a 28](https://medium.com/fantageek/testing-keychain-in-ios-9360c1f38a28)