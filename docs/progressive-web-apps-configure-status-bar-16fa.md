# 渐进式 Web 应用程序:配置状态栏

> 原文：<https://dev.to/akshaykumar6/progressive-web-apps-configure-status-bar-16fa>

渐进式网络应用(PWA)开始获得很大的势头，并被包括优步、推特、Instagram 和其他许多公司在内的主要科技公司采用。在开发我的第一个 PWA 时，支持跨平台定制状态栏被证明是一个比预期更大的挑战。这是一个帮助其他人快速定制 PWA 状态栏的快速帖子，该状态栏可以在 Android 和 iOS 设备上工作，包括带有 notch 的设备。

为了更改渐进式 web 应用程序屏幕顶部的状态栏(通常显示时间和电池状态)，您必须使用应用程序的配置少数属性来提供完整的本机外观。

### 安卓

Chrome、Firefox 和 Opera 允许你使用 meta 标签定义状态栏的颜色。

```
<!-- Chrome, Firefox OS and Opera -->
<meta name="theme-color" content="#014473"> 
```

示例:

[![](../Images/aa32b7a0bd18257141a877f8903617b1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xYwlUh1p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/NsUIohY.png)

### iOS

不幸的是，为 iOS 设备定制状态栏的方法相当有限，但苹果公司提供了`apple-mobile-web-app-status-bar-style` meta 标签来定制状态栏。有三种不同的设置可用:`default`、`black`和`black-translucent`。默认值为`default`。

除非添加`apple-apple-mobile-web-app-capable`元标签，否则该元标签无效。

```
<meta name="apple-mobile-web-app-capable" content="yes"> 
```

#### 默认

默认设置是白色背景，黑色文本和符号。

```
<meta name="apple-mobile-web-app-status-bar-style" content="default"> 
```

示例:

[![](../Images/8267b5caad95070de74c11ed022de9ac.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gjKukh-f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/BS17zf4.jpg)

#### 黑色

黑色设置有黑色背景和黑色文本和符号，使其看起来完全是黑色的。

```
<meta name="apple-mobile-web-app-status-bar-style" content="black"> 
```

示例:

[![](../Images/a74eb3727d3d053d277c21698e2f2269.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3KvIwDmF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/kPf8MUi.jpg)

#### 黑色半透明

黑色半透明设置有白色文本和符号。它将采用与你的 web 应用程序相同的背景颜色。

```
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent"> 
```

```
body {
    background-color: #014473;
} 
```

示例:

[![](../Images/dfa7b49e3ad6b52e7e5ef94772067efc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tvqoKklQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/XIrafYW.jpg)

### 现场演示

[头条 App](https://headlines-pwa.firebaseapp.com?ref=devto)

### 参考文献:

[安卓支持](https://developers.google.com/web/updates/2014/11/Support-for-theme-color-in-Chrome-39-for-Android)

[Safari HTML 参考](https://developer.apple.com/library/archive/documentation/AppleApplications/Reference/SafariHTMLRef/Articles/MetaTags.html#//apple_ref/doc/uid/TP40008193-SW4)

[Safari 网页内容指南](https://developer.apple.com/library/archive/documentation/AppleApplications/Reference/SafariWebContent/ConfiguringWebApplications/ConfiguringWebApplications.html#//apple_ref/doc/uid/TP40002051-CH3-SW1)

[演示 App - Github](https://github.com/akshaykumar6/headlines-pwa)