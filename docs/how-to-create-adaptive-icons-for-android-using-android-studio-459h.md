# 如何使用 Android Studio 为 Android 创建自适应图标

> 原文：<https://dev.to/sfarias051/how-to-create-adaptive-icons-for-android-using-android-studio-459h>

我发现自己的 android 应用程序无法使用自适应图标，导致不同的启动器中出现难看的应用程序图标和奇怪的形状...解决方案是创建自适应图标，而且效果很好。🙂

在 API 的> 26 android 实现了自适应图标，这可能会导致现有应用程序中的应用程序图标出现问题，就像下面的例子。

<figure>

[![Example: Old app icons](img/f0aa7067c0f5ea3b419e6ad5c77b2134.png "Old app icons")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sobvAIZ0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_E5BDCE34FA744C8B9CF5B8D37F689ADCFA70DD76E310D78941E0487F015653CD_1537327485177_Screen%2BShot%2B2018-09-19%2Bat%2B00.24.20.png)

<figcaption>Example: Old app icons</figcaption>

</figure>

为了避免这种情况，有必要创建一个自适应图标。为此，您需要以下资产:

*   没有背景的图像图标，示例如下(前景)
*   彩色背景

<figure>

[![Foreground icon image](img/4938347a18d2eb019f4d13f744eb2bc3.png "Foreground icon image")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IionBI2d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_4E9831D4455D570AE14137599E816F77C0D9972382FFC988D0AB1312B7DF216A_1537312779083_baseline_directions_bike_black_48.png)

<figcaption>Foreground icon image</figcaption>

</figure>

有了这些元素，您需要按照以下步骤使用 Image Asset Studio 生成图标。

1.  在 Android Studio 中打开项目
2.  选择应用程序文件夹
3.  转到文件→新建→图像资产
4.  在“前景层”选择提供的图像和调整大小，以获得图标的适当方面
5.  在“背景层”使用提供的背景
6.  点击下一步按钮，然后完成
7.  之后，在“mipmap-* * * *”文件夹中创建图标

<figure>

[![Foreground layer configuration](img/8af5ae38f1bf733c924ba42e9f84948e.png "Foreground layer configuration")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MRNRZqq3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_4E9831D4455D570AE14137599E816F77C0D9972382FFC988D0AB1312B7DF216A_1537313441920_Screen%2BShot%2B2018-09-18%2Bat%2B20.26.25.png)

<figcaption>Foreground layer configuration</figcaption>

</figure>

<figure>

[![Background layer configuarion](img/7ea631fd7f5cef24264e5b1e2b8dc25a.png "Background layer configuarion")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fTYSHeGH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_4E9831D4455D570AE14137599E816F77C0D9972382FFC988D0AB1312B7DF216A_1537313451765_Screen%2BShot%2B2018-09-18%2Bat%2B20.30.07.png)

<figcaption>Background layer configuarion</figcaption>

</figure>

<figure>

[![Last screen and creation of assets](img/287466a38de82b564ad73186b836d439.png "Last screen and creation of assets")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NxBZvOka--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_4E9831D4455D570AE14137599E816F77C0D9972382FFC988D0AB1312B7DF216A_1537313504996_Screen%2BShot%2B2018-09-18%2Bat%2B20.31.35.png)

<figcaption>Last screen and creation of assets</figcaption>

</figure>

您需要将新的圆形图标添加到 AndroidManifest.xml 上相应的圆形图标选项中

```
 <application
        ...
        android:icon="@mipmap/ic_launcher"
        android:roundIcon="@mipmap/ic_launcher_round"
        ...>
    </application> 
```

Enter fullscreen mode Exit fullscreen mode

应用更改后。应用程序图标将根据 android 自适应图标的指导方针。

<figure>

[![Example: Android 9, API 28 stock launcher](img/8ea161641fc782ceca06594ba6f1a829.png "Example: Android 9, API 28 stock launcher")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ubm3UBVK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_E5BDCE34FA744C8B9CF5B8D37F689ADCFA70DD76E310D78941E0487F015653CD_1537327521464_Screen%2BShot%2B2018-09-19%2Bat%2B00.25.03.png)

<figcaption>Example: Android 9, API 28 stock launcher</figcaption>

</figure>

<figure>

[![Example: Android 7.0, API 24, Smart Launcher](img/f0a5b87c320a03097939f8aaa0cd39be.png "Example: Android 7.0, API 24, Smart Launcher")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KO5Q90q3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_4E9831D4455D570AE14137599E816F77C0D9972382FFC988D0AB1312B7DF216A_1537317665511_Screen%2BShot%2B2018-09-18%2Bat%2B21.37.22.png)

<figcaption>Example: Android 7.0, API 24, Smart Launcher</figcaption>

</figure>

* * *

> 所有信息均来自安卓官方信息
> [https://developer.android.com/](https://developer.android.com/)
> [https://developer . Android . com/guide/practices/ui _ guidelines/icon _ design _ adaptive](https://developer.android.com/guide/practices/ui_guidelines/icon_design_adaptive)
> [https://developer . Android . com/studio/write/image-asset-studio # create-adaptive](https://developer.android.com/studio/write/image-asset-studio#create-adaptive)

🙂