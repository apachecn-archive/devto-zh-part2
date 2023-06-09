# 如何修复 iOS build 上找不到 Flutter 插件头文件的错误

> 原文：<https://dev.to/rkowase/how-to-fix-the-error-that-the-header-file-of-flutter-plugin-does-not-find-on-ios-build-4c22>

## 如何修复

*   用 XCode 打开头文件。
*   右栏`Target Membership`的`Project`变为`Public`。![](img/77bcabc0e2edc786395736bf49b315e7.png)

👇

[![](img/96309c681942aabb04706bfff210cccc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kIHWJVbR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qiita-image-store.s3.amazonaws.com/0/123122/0103e1bc-d7c3-618b-d298-42809be82929.png)

如果有多个头文件，您需要对所有文件都这样做。

## 环境

*   颤动 0.1.5
*   dart 2 . 0 . 0-开发版 28.0
*   iPhone X - 11.2

## 错误信息

```
/Users/rkowase/StudioProjects/stopwatch/ios/Runner/GeneratedPluginRegistrant.m:6:9: fatal error: 'firebase_admob/FirebaseAdMobPlugin.h' file not found
#import <firebase_admob/FirebaseAdMobPlugin.h>
        ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ 
```

Enter fullscreen mode Exit fullscreen mode

## 备忘录

如果你添加 Firebase 插件，你需要按照[将 Firebase 添加到你的 iOS 项目| Firebase](https://firebase.google.com/docs/ios/setup) 中所写的那样通过 XCode 添加`GoogleService-Info.plist`。
[![](img/4b0c70eb33601362cbe655823d2bbed2.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--U_i8zni7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qiita-image-store.s3.amazonaws.com/0/123122/02c5c5ee-90c2-495b-cd3a-24ea4400ecc3.png)

## 链接

使用 firebase_admob 插件进行 Flutter - Stack 溢出时找不到 FirebaseAdMobPlugin.h 文件
[https://Stack Overflow . com/questions/49473823/firebaseadmobplugin-h-file-not-found-when-using-firebase-admob-Plugin-for-flutte](https://stackoverflow.com/questions/49473823/firebaseadmobplugin-h-file-not-found-when-using-firebase-admob-plugin-for-flutte)

目标 c - Swift 编译器错误:"框架模块内非模块化头"-堆栈溢出
[https://Stack Overflow . com/questions/24103169/Swift-compiler-error-non-modular-header-inside-framework-module/24317456 # 24317456](https://stackoverflow.com/questions/24103169/swift-compiler-error-non-modular-header-inside-framework-module/24317456#24317456)

“使 pod 文件使用到本地 pod 的符号链接”不工作问题# 15099 flutter/flutter
[https://github.com/flutter/flutter/issues/15099](https://github.com/flutter/flutter/issues/15099)

在 iOS 构建中找不到 path provider-Google Groups
[https://groups.google.com/forum/#!msg/flutter-dev/7 qrk 1 rphlde/5 RLS 4 cibwaj](https://groups.google.com/forum/#!msg/flutter-dev/7QRk1RphLDE/5rLS4CciBwAJ)

Flutter 的 firebase
[https://codelabs . developers . Google . com/codelabs/Flutter-firebase/# 4](https://codelabs.developers.google.com/codelabs/flutter-firebase/#4)

将 Firebase 添加到您的 iOS 项目| Firebase
[https://firebase.google.com/docs/ios/setup](https://firebase.google.com/docs/ios/setup)