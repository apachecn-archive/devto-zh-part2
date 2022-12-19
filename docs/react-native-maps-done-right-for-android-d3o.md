# 反应-本地-地图做了正确的 Android🕵️

> 原文：<https://dev.to/droidmakk/react-native-maps-done-right-for-android-d3o>

因此，这篇文章将带你浏览我在尝试将反应原生地图修补到我的 rn 应用程序中时遇到的陷阱

#### 因此，我认为您已经在清单中配置了以下内容

node js
NPM/yar
react-native CLI

PS:这是针对 android 的，因为我既没有 Mac 也没有 iPhone😉因此，让我们创建一个应用程序的样板设置。

`react-native init mapapp`
T1】

将**安卓**和 **ios** 的依赖项、脚本文件和引导源代码安装在两个独立的文件夹中需要一些时间。

项目树看起来像这样🌲。哎呀，不是那个😂👇

[![Project Tree](../Images/314c115e41f6ac5772a23956e564b421.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6Pgh-vAx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/QEm5tcS.png)

因此，继续尝试运行该命令

`react-native run-android`

确保你有一个 **avd** 或者一个 **android 设备**连接到你的机器上，并且启用了开发者选项。

一旦成功执行，您将基本上看到如下所示的屏幕。

[![First Screenshot](../Images/f6c239ff2747cf4a9a4d089cb6044767.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VPs7sCoS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/jjX9ee7.png)

现在让我们安装 react-native-maps 包。

> 若纱
> `yarn add react-native-maps`
> 
> 对于 npm
> `npm install --save react-native-maps`

安装完成后，你可以看到 **react-native-maps** 包被添加到 **package.json** 的依赖项部分

对于基本的 rn 包，你需要做的就是*react-native link react-native-package*。这将自动为你配置 android 和 ios 源文件，如 gradle 构建文件导入等。但在这种情况下不会。

如果你去 react-native-map [安装页面](https://github.com/react-community/react-native-maps/blob/master/docs/installation.md)有一个类似**的大通知！！请勿使用！！react-native link**
所以我会按照他们的步骤进行配置就是我所想的！

> 但是事情并不总是像你想象的那样发展。🙃

至于安装页面中建议的 android，我们需要在`android\app\build.gradle`中添加依赖项。

[![build.gradle file](../Images/31e0a3bdb13fca17618150ac20fed8db.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zHTAoBG8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/r3AyPp1.png)

然后在你的 **settings.gradle** 文件中配置如下图所示的代码。将 react-native-maps 的 android 包导入到项目中。

[![Settings gradle](../Images/04889f4a58a06bddfc134d9b54b47e93.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3NeSEgTC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/eNgf9gH.png)

现在在你的`android/app/src/main/AndroidManifest.xml`中添加谷歌地图的元数据内容。
确保您添加的元数据在应用程序标签的范围内。

```
`<application>
    <meta-data />
</application>` 
```

样本抽象看起来像上面的☝️。

> 在获取您的 **Google API 密钥**时，请确保您已经在 google cloud 控制台中启用了 google maps api。

[![AndroidManifest.xml](../Images/dbd7c81f40de6032080fa4a305a0d5a9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OdrvilHU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/uP12Hl4.png)

现在，在您的`android\app\src\main\java\com\mapapp\MainApplication.java`文件中包含包依赖项和一个 maps 包实例。

```
//Import it below all imports in the file
import com.airbnb.android.react.maps.MapsPackage;

//Now Under ReactList package add an instance of MapsPackage
@Override
     protected List<ReactPackage> getPackages() {
         return Arrays.<ReactPackage>asList(
                 new MainReactPackage(),
                 //This is where you create instance of package
                 new MapsPackage()
         );
     } 
```

一旦完成，你的**MainApplication.java**文件将看起来像这样。
T3![MainApplication.java](../Images/3571e425db93ec65b318eeb179c38d3c.png)T5】

现在，让我们试着运行命令`react-native run-android`。停止现有的打包程序以反映更新。

现在一旦你运行，你会得到这样的错误。

[![Error1](../Images/243b7fefb2e7d020fdcc54be1beb29f1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hWRMpEPO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/2UoGBdD.png)

> 现在我们如何解决这个问题！🤔

首先，我们需要更新`build.gradle`脚本，将其更新到最新版本，因为我们使用的 gradle 版本不支持实现。
~~实现~~ `compile project(':react-native-maps')`

```
dependencies {
    compile fileTree(dir: "libs", include: ["*.jar"])
    compile "com.android.support:appcompat-v7:25.3.1"
    compile "com.facebook.react:react-native:+"  // From node_modules
    compile project(':react-native-maps')
} 
```

在运行了一定级别的正确构建步骤之后...构建再次失败。但是这一次有点不同。在谷歌搜索了一段时间后，我在 github 上看到了一条评论，说需要从[到](https://gist.github.com/nazrdogan/87c63e89a2bfd4cefee24990e4e7ed0e)遵循修改步骤。

🐞发布链接是[这里是](https://gist.github.com/nazrdogan/87c63e89a2bfd4cefee24990e4e7ed0e)。

现在根据 [@nazrdogan](https://dev.to/nazrdogan) 的新指南。从 android 路径打开 **build.gradle** 文件。`android\build.gradle`。

[![build.gradle in android](../Images/964e1f85be12f4eb9deaacab6c63350d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LMX9oE8I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/7Il7LVE.png)

现在在 **android/app** 中的`build.gradle`文件更新 sdk 版本的几个参数。

```
android {
// Change compileSdkVersion from 23 to 25
compileSdkVersion 25
// buildToolsVersion from 23.0.1 => 25.0.1
buildToolsVersion "25.0.1"

defaultConfig {
    applicationId "com.mapapp"
    minSdkVersion 16
    targetSdkVersion 22
    versionCode 1
    versionName "1.0"
    ndk {
        abiFilters "armeabi-v7a", "x86"
    }
} 
```

[![buildGradle](../Images/ddbcea63435ae60ce5f5ead4da72bd8c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mborVRoW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/nbFT6OJ.png)

在`app/build.gradle`文件的依赖项部分，将~~编译成~~实现，并将 appcompat-v7 从 23.0.1
版本升级到 25.0.1 版本

```
dependencies {
    implementation fileTree(dir: "libs", include: ["*.jar"])
    //                      Change it from 23.0.1 => 25.0.1
    implementation "com.android.support:appcompat-v7:25.0.1"
    implementation "com.facebook.react:react-native:+"  
    // From node_modules
    implementation project(':react-native-maps')
} 
```

现在打开**Android \ gradle \ wrapper \ gradle-wrapper . properties**。在其中，将分发 URL 更改为 `https\://services.gradle.org/distributions/gradle-4.1-all.zip` 

更新后，它会像这样。

```
distributionBase=GRADLE_USER_HOME
distributionPath=wrapper/dists
zipStoreBase=GRADLE_USER_HOME
zipStorePath=wrapper/dists
distributionUrl=https\://services.gradle.org/distributions/gradle-4.1-all.zip 
```

😅现在尝试运行命令`react-native run-android`。现在这将更新 gradle 构建和更新包，这将需要一些时间来包括模块，然后启动 metro bundler。

🙌最后，在消息**建立成功后**。你可以看到**主活动**意图已经被触发👇。

[![Build Succeeded](../Images/0eddcd9ab40ecc0aa4f693a2ec24e755.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--C6JU3xtP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/dhrWtqL.png)

但是我们还是要看地图对吧！😁

让我们现在做那件事。打开您的 **App.js** 文件，用以下内容替换现有内容👇。

```
import React, { Component } from  'react';
import { StyleSheet } from  'react-native';
import MapView from  'react-native-maps';

export default class App extends Component {
    render() {
        return <MapView style = {styles.map}
            initialRegion = {{
                latitude: 13.139238380834923,
                longitude: 80.25188422300266,
                latitudeDelta: 0.0922,
                longitudeDelta: 0.0421,
                }}/>;
    }
}

const styles = StyleSheet.create({
    map: {
        height: 100,
        flex: 1
        }
}); 
```

一旦你尝试重新加载应用程序。你可以看到下面这样的东西。

> 双击 **R** 重新加载。

[![MapView](../Images/b29e3f912a4c47f94d78712e7dcf81c8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Hcd3tVF1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/wuwxS22.png)

😁👍。干杯，您已经成功地将 react-native-maps 集成到您的 react-native 应用程序中。🙌

> PS:那是我住的地方。📍

如果可能的话，我还会写一篇关于如何在谷歌云控制台中获取 api 键和启用谷歌地图 api 的小文章。

希望你们都喜欢这个。快乐编码💻。