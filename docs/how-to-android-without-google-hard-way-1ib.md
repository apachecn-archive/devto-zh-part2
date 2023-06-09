# 如何在没有谷歌的情况下使用安卓系统

> 原文：<https://dev.to/pabloportugues/how-to-android-without-google-hard-way-1ib>

*原载于我的博客:[如何 Android 而不用 Google](https://bytemybits.gitlab.io/post/2018-05-23-how-to-android-without-google-hard-way/)*

本指南展示了如何在**签名欺骗**和**微 G** 的帮助下安装没有 GApps 的 LineageOS，这样你就可以拥有**推送通知、位置服务之类的**，而不需要安装 Google Play 服务。(没有谷歌——任何与此相关的东西)

它是由围绕着[linegeos](https://lineageos.org/)、 [microG](https://microg.org/) 、 [XPosedFramework](http://repo.xposed.info/) 、 [F-Droid](https://f-droid.org/) 、 [Yalp Store](https://github.com/yeriomin/YalpStore) 和许多其他人的创造者、维护者和社区的辛勤工作而成为可能的。

1.  **注 1:** 我在这里描述的每个`adb`和`fastboot`命令都假设你所在的路径是指定文件的路径。如果不是，要么移动到那里，要么从你所在的地方指向它。
2.  **注 2:** 在整个过程中，我假设设备连接到您的计算机。
3.  **注 3:** 本教程参考了 OnePlus One 设备，但它应该适用于 LineageOS 支持的任何其他手机。当你看到`bacon` (OPO 代号)时，这意味着你应该为你自己的手机搜索该内容的链接。你可以在这里找到你手机的代号:[https://wiki.lineageos.org/devices/](https://wiki.lineageos.org/devices/)

**免责声明:**遵循这里的步骤可能会损坏您的手机或导致其他类型的问题。遵循它，风险自负，并确保你明白你在做什么。

现在，至少有两种方法可以做到这一点——一步一步/艰难的方法(本指南)或简单的方法(T4)直接的方法。选择一个最符合你需求的。

* * *

### 设置操场

1.  备份您的手机(应用程序、设置、文件、图像等)
2.  在你的设备上安装`adb`和`fastboot`——在这里按照说明[操作](https://wiki.lineageos.org/adb_fastboot_guide.html)

#### 一点脉络

这种方法包括刷新 LineageOS 的定制版本，并手动安装 Xposed Framework 以及所有的 microG 组件。困难的原因将在接下来的步骤中变得清楚，因为您已经对正在安装的组件有了完整的粒度。

那我们就开始吧。

##### 下载以下文件:

1.  你的血统来自 https://download.lineageos.org/
2.  SU 包(检查手机的 CPU 架构是否为 arm/arm 64/x86)-
3.  (可选)气象提供商—[https://download.lineageos.org/extras](https://download.lineageos.org/extras)
4.  被曝光的框架安装程序-[https://forum.xda-developers.com/attachment.php?attachment id = 4319220&d = 1509453299](https://forum.xda-developers.com/attachment.php?attachmentid=4319220&d=1509453299)(更多信息请点击:[https://forum.xda-developers.com/showthread.php?t=3034811](https://forum.xda-developers.com/showthread.php?t=3034811))
5.  F-Droid 商店 apk-[https://f-droid.org/packages/org.fdroid.fdroid/](https://f-droid.org/packages/org.fdroid.fdroid/)
6.  FakeGapps Xposed 模块(该模块与 microG GmsCore 配合使用，允许推送通知、位置服务等)-[http://repo . x posed . info/module/com . therma tk . Android . xf . FakeGapps](http://repo.xposed.info/module/com.thermatk.android.xf.fakegapps)

##### 解锁引导程序并恢复闪存

1.  解锁引导程序的说明在这里:[https://wiki.lineageos.org/devices/bacon/install](https://wiki.lineageos.org/devices/bacon/install)
2.  引导进入引导程序:`adb reboot bootloader`
3.  用快速启动([https://wiki.lineageos.org/devices/bacon/install](https://wiki.lineageos.org/devices/bacon/install))([https://dl.twrp.me/bacon/](https://dl.twrp.me/bacon/))安装客户恢复
4.  `fastboot flash recovery <path-to>/<recovery-name-and-version>.img`
5.  开机进入恢复模式(音量降低+电源按钮)-取决于手机
6.  转到擦除→恢复出厂设置(这对于大多数情况来说已经足够了。如果需要，高级擦除和格式化数据将为您提供补充选择——例如，如果您正在将 rom-CM 更改为 linegeos——并且有签名问题。在这种情况下，你可能不得不删除一切。如果你这样做，记住`Mount`否则文件系统将无法工作)

##### 让我们将必要的数据复制到我们的设备中

1.  `adb push <path-to>/lineage-14.1-20180516-nightly-bacon-signed.zip /sdcard/` -如果您计划通过`adb sideload`(高级→亚行侧装，然后是`adb sideload <path-to>/lineage-14.1-20180516-nightly-bacon-signed.zip`)安装，则无需复制此版本
2.  `adb push <path-to>/addonsu-14.1-arm-signed.zip /sdcard/`
3.  `adb push <path-to>/XposedInstaller_3.1.4.apk /sdcard/`
4.  `adb push <path-to>/com.thermatk.android.xf.fakegapps_v3_bfc686.apk /sdcard/`
5.  `adb push <path-to>/org.fdroid.fdroid_1001000.apk /sdcard/`

##### 安装 ROM

1.  转到安装，选择您的自定义 ROM 文件并安装
2.  既然来了，那就把 SU 包(`addonsu-14.1-arm-signed.zip`)也装上吧
3.  重新启动
4.  考虑到是第一次启动，第一次启动会花更长的时间。之后，你应该会看到通常的 Android 初始配置屏幕。

##### 签名欺骗和 F-Droid

我们现在拥有的是你最简单格式的只读存储器。这意味着没有谷歌应用，没有 PlayStore，没有推送通知，没有位置服务，等等...没有后两者的生活是相当困难的，有点击败了拥有一个机器人的目的，所以我们将专注于解决这个问题。

1.  让我们从解锁开发者模式开始，这样我们就可以解锁 SU 访问。进入设置→关于手机→点击内部版本号 7 次
2.  返回并进入新菜单*开发者选项*
3.  在 Root 访问中选择仅*应用*
4.  打开 Android 调试(如果你连接到你的电脑，会出现一个授权的提示，这样你就可以使用 adb 了)

因为我们要安装的每个应用程序都不是来自 Play store，所以我们需要启用来自“非英国来源”的分期付款

1.  进入设置→安全，打开*未知信号源*

现在是时候安装东西了。转到您的文件系统并安装以下内容:

1.  暴露的安装程序
2.  FakeGApps 模块
3.  F-Droid(我们一会儿再来讨论这个)

是时候配置 x 曝光了。打开应用程序，我们会看到 Xposed 框架没有安装，所以让我们这样做。

1.  点击*安装/更新*并选择两种形式中的一种
2.  *安装*需要你有根权限
3.  *通过恢复安装*与我们安装 ROM 的方式相同(这里不需要根目录)
4.  通过进入*模块*并启用 *FakeGApps* 以及 *XposedUnifiedNlp* 来结束
5.  重新启动

##### 安装微 g

现在让我们继续安装将取代 Google Play 服务的东西。microG 的应用程序和库可以通过 F-Droid 获得，所以我们将把它们添加到我们的 F-Droid 库。

1.  设置→存储库→新建存储库-[https://microg.org/fdroid/repo](https://microg.org/fdroid/repo)
2.  在设置中，启用*专家模式*和*不稳定更新* -我们需要这个用于微 g 软件包，因为那些工作的还不稳定

让我们安装它的依赖项。通过 F-Droid 安装:

1.  microG 服务核心(确保你安装的是 0.2.4-10-xxx 或更新版本)-这是 microG 的主要应用程序，也是取代谷歌服务的核心
2.  microG 服务框架代理——这是一些老客户使用 Google 云消息传递所需要的
3.  微 G DroidGuard 助手-在隔离环境中运行 DroidGuard
4.  FakeStore -向游戏服务客户端模拟游戏商店的存在

##### 位置服务

既然我们已经在这里，让我们安装一些位置提供者(从这里选择:[https://f-droid.org/packages/com.google.android.gms/](https://f-droid.org/packages/com.google.android.gms/)):

1.  MozillaNlpBackend(或者其他你喜欢的名字)
2.  提名后端

**注意:**我使用 F-Droid 是因为我每天都在使用它，但是你没有义务这样做。去把每一个单独装在这个包裹里。

现在，你还需要安装 *NetworkLocation.apk* 如果你在这里找到:[https://github . com/microg/Android _ packages _ apps _ unified NLP/releases](https://github.com/microg/android_packages_apps_UnifiedNlp/releases)
如果像我一样，你不能安装它(它说它已损坏，这是一个 ROM 问题，我们需要手动将其放在它应该在的地方。如果是这样的话(更多信息在这里:[https://github . com/microg/Android _ packages _ apps _ unified NLP):](https://github.com/microg/android_packages_apps_UnifiedNlp):)

1.  进入设置→开发者选项，在 Root 访问中选择*应用和 ADB*
2.  下载 *NetworkLocation.apk* ，运行`adb root && adb remount`挂载 */system* 目录
3.  现在将 apk 移动到正确的位置`adb push NetworkLocation.apk /system/priv-app/NetworkLocation.apk`

##### 回微 g

1.  从允许访问它所要求的开始，这是真正需要的
2.  启用*谷歌设备注册*
3.  启用*谷歌云消息*
4.  进入 *UnifiedNlp 设置*并设置您刚刚安装的两个位置提供商

接下来，我们需要为 microG 关闭电池优化，否则它将无法工作，因为系统可能会关闭它。(Google Play 服务也没有进行电池优化，至少你可以选择)。

1.  进入设置→电池→更多选项→电池优化→所有应用→ microG 服务核心→不使用优化
2.  重新启动(以便定位服务正常工作)

最后一件事是测试定位服务和推送通知

### 测试位置服务和推送通知

现在我们已经设置好了一切，是时候实际测试它是否按预期工作了。

#### 位置服务

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

### 思想和动机

我这么做的主要原因是想把我手机上所有的谷歌都去掉。这并不意味着我不会安装一些 GApp，因为我在过去安装过，但因为我对它有完全的控制权，就像我对任何其他应用程序一样。最令人困扰的是，Android 是免费和开源的，但不知何故，如果没有谷歌的东西，想要得到你需要的东西似乎并不容易。

有趣的是，真的没有办法不与谷歌打交道。如果你读了 microG 的目的，你会明白它主要是作为一个代理，仍然与谷歌服务器通信。事实是，这是无法回避的。整个 Android 生态系统都依赖于谷歌，要么是因为 Google Play 服务，要么是因为 Play Store(其他所有商店都是官方不推荐的——他们知道什么最适合你——*Apple dejávúmuch？*)。前者意味着 Play Store 上的每个应用程序都通过谷歌服务器注册推送通知，所以如果你没有连接到这些服务器的东西，你就很难获得推送通知(不过 K-9 邮件的做法不同)。尽管 Android 是免费和开源的，但这一天似乎越来越少了。看看这篇关于如何在没有谷歌的情况下运行 Android 版本的教程就知道了。

仔细想想，你有一个开放的平台，周围有一个紧密依赖于单个公司的生态系统。就个人而言，这不是 Android 的目的，从长远来看，我们只是因为这个而失败。此外，如果你想开发一个应用程序，你需要遵守谷歌 Play 商店的政策，不管你喜不喜欢，如果你对它有所向往，30%的收入归谷歌。

回想一下微软用 ie 浏览器统治网络的时代。如果你能收集所有浏览器的仇恨，你可能会造出几颗原子弹。快进到现在，Chrome 统治了网络，如果你足够注意的话，你会注意到像“在 Google Chrome 上效果最好”或“对不起，只能在 Google Chrome 上使用，在这里下载”这样的消息越来越频繁。开放网络的概念与过去大不相同。

不要误解我，我对 Play Store 或 Play Services 绝对没有问题，我只是认为需要有一个不完全依赖谷歌、不需要你成为技术忍者的替代方案的空间。

* * *

这里有一个挑战:在 iOS 上尝试这样做↑！