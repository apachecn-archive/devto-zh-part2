# 通过 VS 应用中心代码推送对本机更新做出反应

> 原文：<https://dev.to/chandrasekarg/react-native-updates-with-vs-app-center-codepush-30ff>

**‘不那么本土’**应用的一个容易被忽视的便利特性是——
发布更新，而不必经历通常在 play store/app store 提交更新的痛苦。

在本教程中，我们将了解如何使用 Visual Studio 应用中心和 CI 分发来发送代码推送更新。

**先决条件**

1.  [GitHub 账户](https://github.com/)
2.  [VS App Center 账户](https://appcenter.ms/apps)(可以使用 github 登录)
3.  [react-native cli](https://facebook.github.io/react-native/docs/getting-started.html)

**第一步:**

使用 react-native CLI 创建 react native 应用程序，并将代码推送到 GitHub repo。