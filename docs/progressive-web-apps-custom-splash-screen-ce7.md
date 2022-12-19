# 渐进式网络应用程序:自定义闪屏

> 原文：<https://dev.to/akshaykumar6/progressive-web-apps-custom-splash-screen-ce7>

当您的应用程序启动时，闪屏会立即出现。如果您的应用程序很重，加载应用程序可能需要几秒钟时间，具体取决于设备的配置。默认情况下，Android 和 iOS 都显示一个普通的白色屏幕作为 web 应用程序的启动屏幕。自定义闪屏以提供完整的应用程序体验总是更好。

为了更改渐进式 web 应用程序的闪屏，您必须为您的应用程序配置一些属性，以提供完整的本机外观。

### 安卓

Chrome for Android 会自动显示您的自定义闪屏，只要您在 web 应用清单中满足以下要求:

*   `name`属性被设置为 PWA 的名称。
*   属性被设置为一个有效的 CSS 颜色值。
*   数组指定了一个 512 像素乘 512 像素大小的图标。
*   图标存在并且是一个 PNG。

它还会使用`name`属性在图标下方显示应用程序名称。文本颜色无法自定义。根据这个帖子，它是基于对比的[。](https://github.com/w3c/manifest/issues/642#issuecomment-360777137)

### iOS

iOS 不支持自动生成闪屏的类似方法。相反，您需要使用`apple-touch-startup-image` HTML meta 标签为每个 iOS 设备提供定制的闪屏。我们可以使用[媒体](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/link#attr-media)属性和媒体查询来加载设备特定的图像。

```
<link rel="apple-touch-startup-image" href="assets/splashscreens/iphone5_splash.png" media="(device-width: 320px) and (device-height: 568px) and (-webkit-device-pixel-ratio: 2)" />
<link rel="apple-touch-startup-image" href="assets/splashscreens/iphone6_splash.png" media="(device-width: 375px) and (device-height: 667px) and (-webkit-device-pixel-ratio: 2)" />
<link rel="apple-touch-startup-image" href="assets/splashscreens/iphoneplus_splash.png" media="(device-width: 621px) and (device-height: 1104px) and (-webkit-device-pixel-ratio: 3)" />
<link rel="apple-touch-startup-image" href="assets/splashscreens/iphonex_splash.png" media="(device-width: 375px) and (device-height: 812px) and (-webkit-device-pixel-ratio: 3)" />
<link rel="apple-touch-startup-image" href="assets/splashscreens/ipad_splash.png" media="(device-width: 768px) and (device-height: 1024px) and (-webkit-device-pixel-ratio: 2)" />
<link rel="apple-touch-startup-image" href="assets/splashscreens/ipadpro1_splash.png" media="(device-width: 834px) and (device-height: 1112px) and (-webkit-device-pixel-ratio: 2)" />
<link rel="apple-touch-startup-image" href="assets/splashscreens/ipadpro2_splash.png" media="(device-width: 1024px) and (device-height: 1366px) and (-webkit-device-pixel-ratio: 2)" /> 
```

您可以使用 Appsco 的[工具](https://appsco.pe/developer/splash-screens)生成所有分辨率的图像。他们也有一个选项，你可以只使用你的标志和背景颜色来生成这些图像。

让你的应用程序的闪屏随着不同尺寸的 iOS 设备更新[这里](https://developer.apple.com/design/human-interface-guidelines/ios/icons-and-images/launch-screen#static-launch-screen-images)。

### 现场演示

打开应用，点击“添加到主屏幕”——[头条应用](https://headlines-pwa.firebaseapp.com)

[![](../Images/4f573bd54fd1f7727082bcd0c5b6aac5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8UT8FodK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/19OPpMK.gif)

### 参考文献

[安卓:自定义闪屏](https://developers.google.com/web/tools/lighthouse/audits/custom-splash-screen)

[Safari:指定启动屏幕图像](https://developer.apple.com/library/archive/documentation/AppleApplications/Reference/SafariWebContent/ConfiguringWebApplications/ConfiguringWebApplications.html#//apple_ref/doc/uid/TP40002051-CH3-SW6)