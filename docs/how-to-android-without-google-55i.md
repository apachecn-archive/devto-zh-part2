# 如何在没有谷歌的情况下使用安卓系统[简单方法]

> 原文：<https://dev.to/bytemybits/how-to-android-without-google-55i>

*原载于我的博客:[如何在没有谷歌的情况下安卓【简单易行】](https://bytemybits.gitlab.io/post/2018-05-23-how-to-android-without-google-easy-way/)T3】*

本指南展示了如何在**签名欺骗**和**微 G** 的帮助下安装没有 GApps 的 LineageOS，这样你就可以拥有**推送通知、位置服务之类的**，而不需要安装 Google Play 服务。(没有谷歌——任何与此相关的东西)

它是由围绕着[linegeos](https://lineageos.org/)、 [microG](https://microg.org/) 、 [XPosedFramework](http://repo.xposed.info/) 、 [F-Droid](https://f-droid.org/) 、 [Yalp Store](https://github.com/yeriomin/YalpStore) 和许多其他人的创造者、维护者和社区的辛勤工作而成为可能的。

1.  **注 1:** 我在这里描述的每个`adb`和`fastboot`命令都假设你所在的路径是指定文件的路径。如果不是，要么移动到那里，要么从你所在的地方指向它。
2.  **注 2:** 在整个过程中，我假设设备连接到您的计算机。
3.  **注 3:** 本教程参考了 OnePlus One 设备，但它应该适用于 LineageOS 支持的任何其他手机。当你看到`bacon` (OPO 代号)时，这意味着你应该为你自己的手机搜索该内容的链接。你可以在这里找到你手机的代号:[https://wiki.lineageos.org/devices/](https://wiki.lineageos.org/devices/)

**免责声明:**遵循这里的步骤可能会损坏您的手机或导致其他类型的问题。遵循它，风险自负，并确保你明白你在做什么。

至少有两种方法可以做到这一点——直截了当/简单易行的(本指南)或循序渐进/困难重重的*。选择一个最符合你需求的。*

 ** * *

### 搭建操场

1.  备份您的手机(应用程序、设置、文件、图像等)
2.  在你的设备上安装`adb`和`fastboot`——在这里按照说明[操作](https://wiki.lineageos.org/adb_fastboot_guide.html)

#### 一点背景

这种方法包括用所有的 microG 组件刷新 LineageOS 的定制版本。migroG 不是 LineageOS 本身的一部分的原因是因为它的维护者拒绝将签名欺骗功能作为 Lineage 的一部分，理由是安全问题，尽管 microG 团队几次试图证明它不会给它带来任何额外的风险(更多信息请参见[这里](https://lineage.microg.org/#faq6))。

因此，microG 团队所做的是在 Lineage 的基础上修补他们的组件，并创建新的版本，因此任何想要 Lineage w/ microG 避免任何 Google 的人都可以有一个几乎现成的解决方案(一步一步的方式显示了为什么这个过程会变得有点复杂)。他们以一种非常快速的方式发布他们的版本，并发布给所有受沿袭支持的设备。

我个人会选择这个指南，因为你可以设置好一切，而不需要启动你的手机，这不是一步一步的方法。

那我们就开始吧。

#### 装置

##### 下载以下文件:

1.  [从](https://download.lineage.microg.org/)开始，你的线条有/微
2.  [TWRP 复苏](https://dl.twrp.me/bacon/)

##### 解锁引导程序-指令[此处](https://wiki.lineageos.org/devices/bacon/install)

1.  引导进入引导程序- `adb reboot bootloader`
2.  [用快速启动安装客户恢复](https://wiki.lineageos.org/devices/bacon/install) - `fastboot flash recovery <path-to>/twrp-x.x.x-x-bacon.img`
3.  开机进入恢复模式(音量降低+电源按钮-因手机而异)
4.  转至:`Wipe → Factory Reset` -这对于大多数情况来说已经足够了。**高级擦除**和**格式数据**将在需要时为您提供补充选择——例如，如果您正在更换 rom(CM 到 LineageOS)并遇到签名问题。在这种情况下，你可能不得不删除一切。如果你这样做，你可能需要`Mount`否则文件系统将无法工作

##### 安装 ROM(在恢复过程中)

1.  `adb push <path-to>/lineage-14.1-20180516-nightly-bacon-signed.zip /sdcard/` -如果您计划通过`adb sideload` ( *高级→亚行侧装*然后`adb sideload <path-to>/lineage-14.1-20180516-nightly-bacon-signed.zip`安装，则无需复制此版本
2.  转到安装，选择您的自定义 ROM 文件并安装
3.  重新启动

##### 当所有的东西都加载完毕，并且你已经设置好设备之后，就可以使用 microG 应用程序了。

1.  首先允许访问它所要求的内容(这确实是必需的)
2.  启用*谷歌设备注册*
3.  启用*谷歌云消息*
4.  为 microG 关闭电池优化**功能**，否则它将无法工作，因为系统可能会将其关闭。(Google Play 服务也没有进行电池优化，至少你可以选择)
5.  进入*设置→电池→更多选项→电池优化→所有应用→微 g 服务核心→不使用优化*

迎面回到 microG，打开*自检*。所有的方框现在都应该被勾选。UnifiedIP 部分的一些框可能没有勾选，但这并不意味着定位服务不起作用。更多信息请查看*后安装*部分[此处](https://lineage.microg.org/#faq)。

最后，我们只需要检查我们的位置服务和推送通知是否正常工作。

### 测试定位服务和推送通知

现在我们已经设置好了一切，是时候实际测试它是否按预期工作了。

#### 定位服务

这个很简单。首先通过进入*设置→位置→模式*启用设备的位置，并设置为*高精度*(注意，仅设备不能使用*)。*

前往浏览器应用程序，打开 OpenStreetMaps(或任何你想要的地图/位置服务)，允许它访问设备的位置，并点击显示你的位置。

[![Test Locations](img/c9cf1cd04b5b4aa290a765e7eeea7e4f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--glTbJUjX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bytemybits.gitlab.io/post-content/2018-05-23/test-locations.png)

#### 推送通知

我们只是错过了推送通知，让我们测试一下。最简单的方法是使用一个 app - [推送通知测试器](https://play.google.com/store/apps/details?id=com.firstrowria.pushnotificationtester)。

要从 Play Store 安装应用程序而不安装 Play Store 应用程序，请检查**从哪里获取不在 F-Droid 部分**的应用程序。通过*雅普店*进行安装*推送通知测试仪*。

运行它应该会得到所有绿色的勾选框，最后会有一个推送通知。

[![Push Notifications Test](img/73e8fe18637cd2c0972ca770f3af0c37.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cmMC47l4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bytemybits.gitlab.io/post-content/2018-05-23/push-notification-test.png)

就这样，你正式拥有了一个运行 Android 的全功能设备**，没有 T2**的任何功能！

### 从哪里获取 F-Droid 上没有的应用程序

多亏了一个名为 [Yalp Store](https://github.com/yeriomin/YalpStore) 的项目，你可以从 Play Store 下载应用程序并保持更新。我过去用过镜子，但这是目前为止对我来说最安全的解决方案，也是最容易使用的。你可以在 F-Droid 上下载[应用，并在](https://f-droid.org/en/packages/com.github.yeriomin.yalpstore/)[他们的页面](https://github.com/yeriomin/YalpStore)上查看它是如何工作的。

* * *

### 想法和动机

我这么做的主要原因是想把我手机上所有的谷歌都去掉。这并不意味着我不会安装一些 GApp，因为我在过去安装过，但因为我对它有完全的控制权，就像我对任何其他应用程序一样。最令人困扰的是，Android 是免费和开源的，但不知何故，如果没有谷歌的东西，想要得到你需要的东西似乎并不容易。

有趣的是，真的没有办法不与谷歌打交道。如果你读了 microG 的目的，你会明白它主要是作为一个代理，仍然与谷歌服务器通信。事实是，这是无法回避的。整个 Android 生态系统都依赖于谷歌，要么是因为 Google Play 服务，要么是因为 Play Store(其他所有商店都是官方不推荐的——他们知道什么最适合你——*Apple dejávúmuch？*)。前者意味着 Play Store 上的每个应用程序都通过谷歌服务器注册推送通知，所以如果你没有连接到这些服务器的东西，你就很难获得推送通知(不过 K-9 邮件的做法不同)。尽管 Android 是免费和开源的，但这一天似乎越来越少了。看看这篇关于如何在没有谷歌的情况下运行 Android 版本的教程就知道了。

仔细想想，你有一个开放的平台，周围有一个紧密依赖于单个公司的生态系统。就个人而言，这不是 Android 的目的，从长远来看，我们只是因为这个而失败。此外，如果你想开发一个应用程序，你需要遵守谷歌 Play 商店的政策，不管你喜不喜欢，如果你对它有所向往，30%的收入归谷歌。

回想一下微软用 ie 浏览器统治网络的时代。如果你能收集所有浏览器的仇恨，你可能会造出几颗原子弹。快进到现在，Chrome 统治了网络，如果你足够注意的话，你会注意到像“在 Google Chrome 上效果最好”或“对不起，只能在 Google Chrome 上使用，在这里下载”这样的消息越来越频繁。开放网络的概念与过去大不相同。

不要误解我，我对 Play Store 或 Play Services 绝对没有问题，我只是认为需要有一个不完全依赖谷歌、不需要你成为技术忍者的替代方案的空间。

* * *

这种情况下，仅仅因为你可以，就意味着你应该！*