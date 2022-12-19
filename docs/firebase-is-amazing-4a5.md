# Firebase 太棒了

> 原文：<https://dev.to/ngo275/firebase-is-amazing-4a5>

[![image](../Images/d74a336e7c725968949c347b3bc5d512.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cBDnVSLV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g439nsqwzmf3gatmgxza.png)

这几天我在用 Firebase，发现不用编写服务器端代码就可以非常容易地制作原生应用。Firebase 向开发者提供认证、分析、推送通知、数据库、文件存储等功能。此外，有趣的是，Firestore/Realtime 数据库的结构不应该是普通的，它很有特点。数据库模式应该表示视图本身，所以规范化在 Firebase 中并不总是好的。这是非常面向本地应用开发者的。

我使用 Firebase(Firestore，云消息，认证)制作了一个示例应用程序，就像下面的电影一样。当用户推送`Add Button`时，Firestore 中会保存一个新文档，然后这个更新会触发向该用户发送推送通知。

[![img](../Images/e0268cacf1c2b0f7ea08d1c5d9ebd575.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9YTejRZZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j2hnmhx5agxnze7ddc4r.gif)

源代码在这个库中。

[https://github.com/ngo275/FirebaseSample](https://github.com/ngo275/FirebaseSample)

[https://github.com/ngo275/FirebaseSampleFunctions](https://github.com/ngo275/FirebaseSampleFunctions)