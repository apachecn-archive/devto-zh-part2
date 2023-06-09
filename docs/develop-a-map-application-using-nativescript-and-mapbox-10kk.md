# 使用 NativeScript 和 MapBox 开发地图应用程序

> 原文：<https://dev.to/cendekia/develop-a-map-application-using-nativescript-and-mapbox-10kk>

### 使用 NativeScript 和 MapBox 开发地图应用

[![](img/fc1f45684f0d34a137b6395a5d2aa69d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pfgabdrT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A9sbtou33tWALrpNOJyxbrQ.jpeg)

先决条件:

1.  在计算机上安装 NativeScript
2.  创建一个 MapBox 帐户以获取访问令牌
3.  对 JavaScript 知之甚少，无论是 JS 核心，Angular 还是 Vue JS

### 简介:

你可能知道，MapBox 是一个类似于谷歌地图的地图服务提供商。在这篇博客中，我试图展示用{N}ativeScript 和 MapBox 构建一个适用于 Android 和 IOS 的地图应用程序是多么容易。

### 步骤:

在您的计算机上成功安装{N}ativeScript 后，您需要运行:

tns 创建地图应用程序- ng

该命令将为您创建一个{N}项目，您可能想知道是否有一个..- ng，即使用角度模板的选项命令。

下一步，测试你的脚手架应用程序，确保将你的安卓手机连接到你的笔记本电脑上:

```
\> cd mapApp
\> tns run android 
```

此命令需要一段时间才能将您的应用程序构建到您的 Android 手机上。一旦完成，它将看起来像这样:

[![](img/6c6cd450088ea0a4174faa795379a5b4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wuvWBjyS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/432/1%2AwauOzWRcKC11e1EX_VOXMw.jpeg)

回到你的根应用，然后添加插件:

1.  首先，添加{N}插件来检测我们设备的当前位置:tns 插件添加 nativescript-geolocation
2.  然后，添加 mapbox 插件:tns 插件添加 nativescript-mapbox

就这些，现在我们可以开始编码了。

将以下代码添加到位于 src/app/app.module.ts 的应用程序中，以注册 Mapbox 视图: