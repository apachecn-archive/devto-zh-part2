# 设置 React 状态后立即访问它

> 原文：<https://dev.to/dance2die/accessing-react-state-right-after-setting-it-2kc8>

*照片由[塞尔索](https://unsplash.com/photos/4RZx2k4sDj8?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在[Unsplash](https://unsplash.com/search/photos/sequence?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)T5 拍摄*

当我浏览堆栈溢出问题时，我注意到许多错误是由于设置状态值后试图访问它造成的。

*一个[例题](https://stackoverflow.com/questions/51412391)关于堆栈溢出。*

我曾多次因没有意识到`setState`是异步操作而栽了跟头。

在设置状态值之后，我们如何访问它呢？

## 😬重现问题

下面的代码展示了如何在同步设置状态值(`clickCounts`)后立即访问它。