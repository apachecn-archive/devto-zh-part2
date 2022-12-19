# 菲尼克斯:Mozilla 的新手机浏览器

> 原文：<https://dev.to/justyntemme/fenix-a-new-mobile-browser-from-mozilla-3dcf>

## 关于项目

Mozilla 最近为一个名为菲尼克斯的全新移动网络浏览器创建了一个 Github 库。对这个项目了解不多。目前 Mozilla 还没有官方声明。一位接近这些项目的消息人士预计，一个可用的项目将在 2019 年登陆某个地方。以目前项目的代码产出速度，我预计如果不是更早的话。Sebastian Kaspari 似乎是提交历史最多的主要开发人员。

### 技术细节

### 壁虎

菲尼克斯选择使用 Mozilla 自己的 GeckoView，而不是默认的 android webview。Geckos wiki 提到它还没有为生产做好准备，这让我相信 GeckoView 的很多工作都是专门为菲尼克斯做的。您可以按照这里的步骤[试用 GeckoView](https://wiki.mozilla.org/Mobile/GeckoView#What_is_GeckoView)

### 每夜追踪途中

七天前，项目负责人 Sebastian 创建了一个问题，在 google play 上设置了一个测试轨道。这将允许开发者和早期用户第一次看到菲尼克斯，以及它如何与其他现代浏览器相媲美。

### Docker 建立文件

该项目最近添加了一个 Dockerfile 文件，用于构建 APK 的所有版本。Docker 项目使用相同的构建链，这使得开发人员帮助项目变得非常简单。您所需要的只是 docker，其他的构建依赖项都在 docker 文件中处理。这是一个很好的 Docker 用例，更多的项目应该利用它

### Mozilla 公共许可 V2

该项目目前在 Mozilla 公共许可证版本 2 下被许可为开源

### 测试

我的所有测试版本都不能安装在 Android 物理设备和设备模拟器上。如果你能够在某个版本的 android 上运行，请发邮件给我，地址是[justyn @ nextwavesolutions . io](mailto:justyn@nextwavesolutions.io)，这样我就可以分享关于浏览器当前状态的图片。在那之前，我将等待测试分支在 Play Store 上部署。

### 没有 IOS

有趣的是注意到这款浏览器只有安卓版。这并不是说 IOS 设备不会有发展。

### 投稿

最近，Ryan Sipes 要求澄清构建说明，以帮助对项目做出贡献。如果你看一下第 55 期，你会看到 Sebastian 推荐贡献给 Mozilla mobiles android 组件库。菲尼克斯使用的许多工具都是在那里托管的。关于第 55 期的第三个评论是我自己写的，详细介绍了如何使用 Docker 来构建 apk。在这里，您可以找到如何构建和检索正确的 APK 的说明。

阅读更多...[http://nextwavesolutions.io/post/fenix/](http://nextwavesolutions.io/post/fenix/)