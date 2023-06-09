# 在 Golang 游戏中集成 Ad SDK

> 原文：<https://dev.to/ntoooop/integrate-ad-sdk-in-golang-games-198f>

前几天写了一篇关于用`gomobile build`命令把 Golang 项目移植到 Android 的文章。这几天我们团队开发了一款新游戏，准备整合 AD SDK 赚钱。

将 java 代码添加到 Golang 项目中并不容易，但是如果你从另一个角度考虑，我们可以简单地将 go 二进制文件复制到 Android 项目中，我们可以将这个项目视为普通的 Android 项目。

这是主要的步骤:

1.  创建新的 Android 项目
2.  将`GoNativeActivity.java`文件复制到项目中(可以在 gomobile 包中找到该文件)
3.  将`.so`复制到项目中
4.  配置`AndroidManifest.xml`文件以使用您的新活动

就这些，构建运行，可以看到 Android 项目一直在运行我们的 Golang 代码。但是我在哪里可以得到这些`.so`文件呢？我只是解压从`gomobile build -target=android ./`命令构建的`.apk`。

下一步是添加广告。在这个特殊项目中添加广告与任何 Android 项目没有区别。这里，我们在`GoNativeActivity`中添加广告代码。注意，GoNativeActivity 没有视图树，我们不能只添加 AdView 作为子视图。解决这个问题的一个方法是使用一个`PopupWindow`来包含 adview。

其实很简单:

```
// load adview
popup = new PopupWindow(this);
popup.setBackgroundDrawable(new ColorDrawable(android.graphics.Color.TRANSPARENT));
// banner
AdView adView = new AdView(this);
popup.setContentView(adView);

// show ad at bottom of screen
popup.showAtLocation(getWindow().getDecorView(), Gravity.BOTTOM, 0, 0); 
```

Enter fullscreen mode Exit fullscreen mode

在我的项目中，我集成了 AdMob 的 Banner SDK。它与测试 ad-uint-id 配合得非常好。下面是我的游戏截图:
[![Shoot Stack](img/8e8c2df4f9fa465dfaee6b76c5339474.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--043QAoWz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fdxs3gr7uivx4polaox7.png)

我已经发布了 MAC 版和 Android 版。你可以从 itch . io:[https://ntop.itch.io/shootingblock](https://ntop.itch.io/shootingblock)获得 MAC 版本，或者从 https://play.google.com/store/apps/details?的 GooglePlay: [下载 id=io.korok.shootingblock](https://play.google.com/store/apps/details?id=io.korok.shootingblock)

这是一款休闲游戏，我非常喜欢它的艺术风格。(PS:有没有办法远程关闭广告，一直显示。似乎 AdMob 没有提供控制频率的方法)