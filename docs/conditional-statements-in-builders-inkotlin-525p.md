# 科特林建筑者中的条件语句

> 原文：<https://dev.to/ditn/conditional-statements-in-builders-inkotlin-525p>

在 Blockchain，我们最近更新了我们的 Android 应用程序，通过 [FCM](https://firebase.google.com/docs/cloud-messaging/) 使用更强大的推送通知，通知用户他们的钱包收到了 BTC。

正如您所料，我们在通知中添加了一个自定义的振动模式，但这带来了一个小问题。事实证明，Android ≤4.3 的设备会抛出一个错误的`SecurityException: Requires VIBRATE permission`。这很容易解决，但是我们如何以惯用的方式做到这一点呢？

一个建议是尝试/捕捉触发通知——这显然很讨厌，对旧设备上的用户来说是不公平的。简单的方法是将构建器转换成本地的`val`，然后单独修改构建器: