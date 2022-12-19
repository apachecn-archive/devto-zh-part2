# 如何在试运行和生产阶段向 React 原生应用添加应用图标和闪屏

> 原文：<https://dev.to/onmyway133/how-to-add-app-icons-and-splash-screens-to-a-react-native-app-in-staging-and-production-4di9>

[![](../Images/a5b9a3279a2be82064da1999b2caf44f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Wzjsk_Fl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2AJXVkvIcwnggBNCRp.jpg)

React Native 旨在“一次学习，随处编写”，通常用于构建 iOS 和 Android 的跨平台应用程序。对于我们构建的每个应用程序，有时我们需要重用相同的代码，构建并调整它以使其适用于不同的环境。例如，我们可能需要多个皮肤、主题、免费和付费版本，或者更常见的不同的登台和生产环境。

而我们无法避免的任务就是给我们的应用添加应用图标和闪屏。

事实上，要添加一个 staging 和 production 环境，并添加应用程序图标，需要我们使用 Xcode 和 Android Studio，我们使用的方式与使用原生 iOS 或 Android 项目相同。

让我们调用我们的应用程序 MyApp，并用 react-native init MyApp 引导它。当然，将会有大量的[库](https://github.com/thekevinbrown/react-native-schemes-manager)来帮助我们管理不同的环境。

在这篇文章中，我们将像对待原生应用那样做，这样我们就知道了基本步骤。

## 构建配置、目标、构建类型、产品风格和构建变体

我们需要记住一些术语。在 iOS 中，调试和发布被称为[构建配置](https://developer.apple.com/library/archive/featuredarticles/XcodeConcepts/Concept-Build_Settings.html)，测试和生产被称为[目标](https://developer.apple.com/library/archive/featuredarticles/XcodeConcepts/Concept-Targets.html)。

> # A build configuration specifies a set of build settings for building a target product in a specific way. For example, the debug version and release version of a product usually have different version configurations.
> 
> # The target specifies the product to be built, and contains instructions to build the product from a set of files in the project or workspace. Define the target single product; It organizes the input into the building system needed to build the product-source files and instructions for processing these source files. A project can contain one or more objectives, each of which produces a product

在 Android 中，调试和发布被称为构建类型，阶段化和生产被称为产品风格。它们一起形成了[构建变体](https://developer.android.com/studio/build/build-variants)。

> # For example, a "demo" *product style* can specify different features and equipment requirements, such as customized source code, resources and minimum API level, while the "debug" *build type* applies different build and package settings, such as debug options and signature keys. The generated build variant of is the "demoDebug" version of the application, which includes the "demo" product style, the "Debug" build type and the combination of configuration and resources contained in the main/source set.

## iOS 中的试运行和生产目标

使用 Xcode 在 ios 中打开 MyApp.xcodeproj。下面是引导后我们得到的结果:

[![](../Images/bfe4500616b30b01c51044db11ca2796.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WXnX7pKu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2004/1%2AHMMS0zLAfLU_bvli-IESBg.png)

React Native 创建 iOS 和 tvOS 应用，以及两个测试目标。在 Xcode 中，一个项目可以包含许多目标，每个目标都意味着一个具有自己的构建设置(Info.plist 和 app 图标)的独特产品。

### 重复目标

如果我们不需要 tvOS 应用程序，我们可以删除 MyApp-tvOS 和 MyApp-tvost。让我们使用 MyApp 目标作为我们的生产环境，右键单击->复制来制作另一个目标。姑且称之为 MyApp 分期吧。

[![](../Images/19d4e9402f221a1137e5ca77043152f7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ASwsRZ6H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2694/1%2AT2RDoSaQRJv8GRGkphUkGw.png)

每个目标必须有唯一的包 id。将 MyApp 的捆绑包 id 更改为 com.onmyway133.MyApp，将 MyApp Staging 更改为 com.onmyway133.MyApp.Staging。

[![](../Images/dd47b397975337f14af56dca87cb7be5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_hs2XypY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3720/1%2AI3qdG0cgDrwc1bep76ckcA.png)

### 信息列表

当我们复制 MyApp 目标时，Xcode 也会将 Info.plist 复制到 MyApp copy-Info.plist 中，用于暂存目标。将其更改为一个更有意义的名称 Info-Staging.plist，并将其拖到 Xcode 中的 MyApp 组，以保持有序。拖动后，MyApp Staging target 找不到 plist，所以点击选择 Info.plist 文件，指向 Info-Staging.plist。

[![](../Images/9613bf0019033fa0d2148e6c6165b396.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GvFJ15Tu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/4732/1%2A_FYFXy6NDHhSm5XwOPFDmQ.png)

### 方案

当我们复制目标时，Xcode 也复制方案，所以我们得到 MyApp 副本:

[![](../Images/a5e5ef8f367c20dfed3db869df7f801a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FB4jJzVJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/5158/1%2ADW9wbUJWU1P4gXymv1LT8g.png)

点击方案下拉菜单中的管理方案，打开方案管理器:

[![](../Images/580e17c6f753b8979a1e713b788c5f1f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xb4GbK6l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3208/1%2A-bjXILHt-Y88etVJDlmyFQ.png)

我通常会删除生成的 MyApp 复制方案，然后为 MyApp Staging 目标再次创建一个新方案。您需要确保该方案被标记为 Shared，以便在 git 中进行跟踪。

[![](../Images/4f1d80447176768b64e1dc5b11822b3f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8_qYk5zt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3208/1%2AVTXk1T-j9W-Yd24uU7Wmqg.png)

出于某种原因，试运行方案没有像生产方案那样设置所有的东西。您可能会遇到诸如“React/RCTBundleURLProvider.h”文件未找到或[RN:“React/rctbridgemodule . h”文件未找到](https://github.com/onmyway133/notes/issues/380)之类的问题。这是因为反应目标尚未链接。

要解决这个问题，我们必须禁用 Parallelise Build 并添加 React target，并将其移动到 MyApp Staging 之上。

[![](../Images/25c6be944ee6f334676fc54b47d35729.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OHy_JGtW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3720/1%2AF7NF8BBg44H6AZHk_A7Jpg.png)

### Android 中的暂存和生产产品口味

在 android Studio 中打开 Android 文件夹。默认情况下，只有调试和发布版本类型:

[![](../Images/4a7cc0e0ed435fd6723f88632f4f836a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jRr-W9Va--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3528/1%2Ae362XxJHJpR0tn-ry86brA.png)

它们在应用程序模块 build.gradle 中配置:

```
buildTypes {
    release {
        minifyEnabled enableProguardInReleaseBuilds
        proguardFiles getDefaultProguardFile("proguard-android.txt"), "proguard-rules.pro"
    }
} 
```

首先，让我们将应用程序 id 改为 com.onmyway133.MyApp 以匹配 iOS。这不是必须的，但我认为保持有条理是有好处的。然后为试运行和生产创建两种产品风格。对于分期，我们来补充一下。转移到应用程序 id。

在 Android Studio 3 中，“所有的味道现在必须属于一个命名的味道维度”——通常我们只需要默认的维度。下面是我们的应用模块在 build.gradle 中的样子:

```
android {
    compileSdkVersion rootProject.ext.compileSdkVersion
    buildToolsVersion rootProject.ext.buildToolsVersion
    flavorDimensions "default"

defaultConfig {
        applicationId "com.onmyway133.MyApp"
        minSdkVersion rootProject.ext.minSdkVersion
        targetSdkVersion rootProject.ext.targetSdkVersion
        versionCode 1
        versionName "1.0"
        ndk {
            abiFilters "armeabi-v7a", "x86"
        }
    }
    splits {
        abi {
            reset()
            enable enableSeparateBuildPerCPUArchitecture
            universalApk false  // If true, also generate a universal APK
            include "armeabi-v7a", "x86"
        }
    }
    buildTypes {
        release {
            minifyEnabled enableProguardInReleaseBuilds
            proguardFiles getDefaultProguardFile("proguard-android.txt"), "proguard-rules.pro"
        }
    }

productFlavors {
        staging {
            applicationIdSuffix ".Staging"
        }

        production {

        }
    }
} 
```

点按“立即同步”以让 gradle 执行同步工作。之后，我们可以看到我们有四个构建变体:

[![](../Images/c1a25e39121d8c3c452e5953cf2be446.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bsOLYye0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/5478/1%2AKxKqB-NdxtieuhFF-AUvwA.png)

### 如何运行暂存和生产

要运行 Android 应用程序，我们可以指定一个类似 react-native run-android 的变体— variant=productionDebug，但我更喜欢去 Android Studio，选择该变体，然后运行。

运行 iOS app，我们可以指定 react-native run-iOS—simulator = ' iPhone X '—scheme = " MyApp Staging "这样的方案。从 react-native 0.57.0 开始，这不起作用。但这没关系，因为我通常会进入 Xcode，选择方案，然后运行。

### 为 iOS 添加应用图标

根据[人机界面指南](https://developer.apple.com/design/human-interface-guidelines/ios/icons-and-images/app-icon/)，我们需要针对不同 iOS 版本、设备分辨率、情况(通知、设置、Spring Board)的不同大小的 app 图标。我制作了一个叫做[图标生成器](https://github.com/onmyway133/IconGenerator)的工具，之前在[开发者最佳开源工具](https://dev.to/sarthology/best-open-source-tools-for-developers--300f)中提到过。将您想要的图标(我更喜欢 1024x1024 像素的高分辨率应用程序图标)拖到图标生成器 MacOS 应用程序中。

[![](../Images/5692d85c2da7fa3f1401b52ca09f9599.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--C8NyfQeR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2848/1%2A4uBuihDMn3nq3Y1NwVhJsA.png)

单击 Generate，我们得到 AppIcon.appiconset。其中包含所需大小的应用程序图标，可在资产目录中使用。将它拖到 Xcode 中的资产目录。那是为了生产。

对于试运行，添加一个“试运行”标语是一个好的实践，这样测试人员就知道哪个是试运行，哪个是生产。我们可以很容易地在草图中做到这一点。

[![](../Images/8a8d71004987a58dc8c330e340bd26ee.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rCDEYGRe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/4536/1%2A6uxkwnsGkDp0K12LXh_yiw.png)

记得设置一个背景，这样我们就不会得到透明的背景。对于一个背景透明的 app 图标，iOS 将背景显示为黑色，看起来很恐怖。

导出图像后，将 staging 图标拖动到 IconGenerator，就像我们之前做的一样。但是这一次，将生成的 appiconset 重命名为 AppIcon-Staging.appiconset。

对于要使用暂存应用程序图标的暂存目标，请打开 MyApp 暂存目标并选择 AppIcon-Staging 作为应用程序图标源。

[![](../Images/6b47a4d9af2cce3de0640aff83f9b716.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--epMtvnXE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/4908/1%2AoIGuaxyftwCD0_72Rnv9-g.png)

### 为 Android 添加应用图标

[![](../Images/0939275200fca339b517835f0c570313.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lym3_u25--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/6720/1%2ACS_tKOts1zz05PVJ5XEc9A.png)

我喜欢切换到项目视图，因为它更容易改变应用程序图标。单击 res -> New -> Image Asset 打开 Asset Studio。我们可以使用与 iOS 中相同的应用图标:

[![](../Images/0d49a037d8918302ad64c6e2644e9edc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--95ukASMg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/4528/1%2AbzJZh1exxXGnrXw_j4AMgQ.png)

Android 8.0 (API level 26)引入了[自适应图标](https://developer.android.com/guide/practices/ui_guidelines/icon_design_adaptive)，所以我们需要调整调整大小滑块，以确保我们的应用程序图标看起来尽可能漂亮。

> # Android 8.0 (API level 26) introduces the adaptive launcher icon, which can display multiple shapes across different device models. For example, the adaptive launcher icon can display a circle on one OEM device and a small circle on another device. Each device OEM provides a mask, and then the system uses this mask to present all adaptive icons in the same shape. Adaptive launcher icons are also used for shortcuts, setting up applications, sharing dialogs, and overview screens. — [Android Developer](https://developer.android.com/guide/practices/ui_guidelines/icon_design_adaptive)

我们首先是为了生产，这意味着主要的 Res 目录。这一步将替换 Android Studio 在引导 React 原生项目时生成的现有占位符应用程序图标。

[![](../Images/0bda767dd9d6f617daff636036925918.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--166w_-Nu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/4528/1%2AkIYEWBrWMG_qOkY-LsWnxw.png)

现在我们有了生产应用程序图标，让我们制作分期应用程序图标。Android 通过惯例管理代码和资产。单击 src -> New -> Directory 并创建一个暂存文件夹。在 staging 中，创建一个名为 res 的文件夹。我们放在 staging 中的任何内容都将替换 main 中的内容，这就是所谓的 source sets。

[![](../Images/fe321adffb78dde6d6c875a78e7ff0ab.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wNYGmOwi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3652/1%2AdAkYONFc86mHNXKnbPf2fA.png)

你可以在这里阅读更多:[用源集构建](https://developer.android.com/studio/build/build-variants)。

> 您可以使用源集目录来包含您希望仅与某些配置一起打包的代码和资源。例如，如果您正在构建“demoDebug”构建变体，它是“demo”产品风格和“Debug”构建类型的交叉产品，Gradle 会查看这些目录，并给予它们以下优先级:
> src/demoDebug/ *(构建变体源集)*
> 【src/debug/*(构建类型源集)*
> src/demo/ *(产品风格源集)*
> 【src/main/*(主源)*

右键单击暂存/资源->新建->图像资产，制作用于暂存的应用程序图标。我们也使用与 iOS 中相同的 staging 应用程序图标，但这次我们选择 staging 作为 Res 目录。这样 Android Studio 就知道如何生成不同的 ic_launcher，并把它们放入 staging。

[![](../Images/941227e3ce909f4838c8f569cce5c177.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QGuW1loU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/4528/1%2AjKI8Erm57SGwZI2ylLaJ0w.png)

### 为 iOS 添加启动屏幕

闪屏在 iOS 中被称为[启动屏](http://Launch%20Screen)，很重要。

> # When the application is started, a startup screen will appear immediately. The startup screen is quickly replaced by the first screen of your application, giving the impression that your application is fast and responsive

在过去，我们需要为每个设备和方向使用不同大小的静态启动图像。

[![](../Images/a70bca859f53af881a11f25c6e7c610b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--n6poGieN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2912/1%2AgncLBHGa4ntt1a9NCLL_1g.png)

### 启动屏幕故事板

目前推荐的方法是使用启动屏幕故事板。React Native 的 iOS 项目附带了 LaunchScreen.xib，但是 xib 已经是过去式了。让我们删除它并创建一个名为 Launch Screen.storyboard 的文件。

右键单击 MyApp 文件夹->新建并选择启动屏幕，将它添加到两个目标，因为通常我们会为试运行和生产显示相同的启动屏幕。

[![](../Images/22011a27bfcc133d731fcb293cb5b809.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mpICxSXD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/5140/1%2AkfGNEVm_gJevBIziQ3iXbw.png)

[![](../Images/e3f68cfcb9c7cc97aab27c935cbf3388.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ihBtTN9k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2984/1%2Av0ZGfvbSFgljeW34PQsw7g.png)

### 图像设置

打开资产目录，右键单击并选择新建图像集。我们可以给它起任何名字。这将用于启动屏幕。故事板

[![](../Images/887273bcb7d7b429677cd7466237e4c5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--h7Sp2yhB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/4496/1%2AWOAJ9Vr7fSqRSxwBwRb3qg.png)

打开 Launch Screen.storyboard 并添加一个 UIImageView。如果您使用的是 Xcode 10，请点按右上角的“资源库”按钮，然后选取“显示对象资源库”。

[![](../Images/072b400aeb1425004b8dad0e24386e39.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5Y59I54S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/6720/1%2AI6hTPAW_L4fUbo8cTakXlg.png)

将图像设置为图像视图，并确保内容模式设置为纵横比填充，因为这可以确保图像始终覆盖整个屏幕(尽管它可能会被裁剪)。然后使用约束将 ImageView 连接到视图，而不是安全区域。您可以通过按住 Control 键并从图像视图(splash)拖动到视图来完成此操作。

[![](../Images/2fed77d2eff3f46face11ebf0115cb91.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0oq3xSRU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/5700/1%2AqdEg7IejnxG3Y0Uo1bpiCA.png)

### 约束无余量

单击每个约束并取消选中相对于边距。这使得我们的 ImageView 固定在视图的边缘，没有任何边距。

[![](../Images/3026b9f6daf3e5aeb706fe340b179e4c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nTDxXc9H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/6720/1%2A5AIBO-ZLXfiLGsk9P32nFw.png)

现在转到两个目标并选择 Launch Screen.storyboard 作为启动屏幕文件:

[![](../Images/b67c0f8c96620a82932c010df968da85.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YEZoZqAs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/7168/1%2ASrrFr1lk7oIwc4QvdoJVxg.png)

在 iOS 上，启动屏幕经常被缓存，所以你可能看不到这些变化。避免这种情况的一种方法是删除应用程序，然后再次运行它。

### 为 Android 添加一个启动器主题

有几个 [方法](https://android.jlelse.eu/right-way-to-create-splash-screen-on-android-e7f1709ba154)为 Android 添加闪屏，从使用启动器主题，闪屏活动，和定时器。对我来说，一个合理的 Android 闪屏应该是一个极简的图像。

由于有许多不同比例和分辨率的 Android 设备，如果你想显示全屏启动图像，它可能不会为每个设备正确缩放。这只是关于 UX。

对于闪屏，让我们使用带有 splash_background.xml 的启动器主题。

### 了解设备度量

没有适合所有 Android 设备的单一闪屏图像。更合理的方法是为纵向和横向的所有常见分辨率创建多个 splash 图像。或者我们可以设计一个最小的飞溅图像。您可以在这里找到更多信息:[设备指标](https://material.io/tools/devices/)。

[![](../Images/6591f508111eea034bfd49cc323d7f49.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VYuEUS3p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AVY5n-nyMet5xlgQyr_fWWw.png)

[![](../Images/b8d03f89f2847b5fdcde3a18d8588c04.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AomXvdCZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/5940/1%2A491dzlEkHT3FvIuuLosGKg.png)

以下是如何在 4 个简单的步骤中添加闪屏:

### 添加启动画面

我们通常需要一个公共的闪屏用于筹备和生产。将图像拖到 main/res/drawble 中。Android Studio 似乎在识别闪屏的一些 jpg 图像方面有问题，所以最好选择 png 图像。

### 添加 splash_background.xml

右键单击可绘制->新建->可绘制资源文件。随便你怎么命名——我选 splash_background.xml。选择根元素作为层列表:

[![](../Images/1d2a825eaa1e6a440d1db28d71fcf9c0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vlrzViu1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/4170/1%2ArEa10WkfXx9VCBX_0sQK3A.png)

[![](../Images/03bd72760fd2b68f720f5a14666070bf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pgJWCcxw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AFwCOuThRAGCZSWvdhVrfCw.png)

一个[层列表](http://Layer%20List)意味着“一个管理其他可绘制对象数组的可绘制对象”。它们是按数组顺序绘制的，所以索引最大的元素被绘制在最上面”。splash_background.xml 如下所示:

```
<?xml version="1.0" encoding="utf-8"?>

<!-- The android:opacity=”opaque” line — this is critical in preventing a flash of black as your theme transitions. -->
<layer-list xmlns:android="http://schemas.android.com/apk/res/android"
    android:opacity="opaque">

    <!-- The background color, preferably the same as your normal theme -->
    <item android:drawable="@android:color/white"/>

    <!-- Your splash image -->
    <item>
        <bitmap
            android:src="@drawable/iron_man"
            android:gravity="center"/>

    </item>

</layer-list> 
```

请注意，我们指向我们之前在 android 中添加的 splash 图像:src="@drawable/iron_man "。

### 申报样式

打开 styles.xml 并添加 SplashTheme:

```
<style name="SplashTheme" parent="Theme.AppCompat.NoActionBar">
    <item name="android:windowBackground">@drawable/splash_background</item>
</style> 
```

### 使用 SplashTheme

转到 Manifest.xml 并更改 launcher 活动的主题，其类别为 Android:name = " Android . intent . category . launcher "。改成 Android:theme = " @ style/splash theme "。对于 React Native，启动器活动通常是 MainActivity。以下是 Manifest.xml 的外观:

```
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.myapp">

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW"/>

    <application
      android:name=".MainApplication"
      android:label="@string/app_name"
      android:icon="@mipmap/ic_launcher"
      android:allowBackup="false"
      android:theme="@style/AppTheme">
      <activity
        android:name=".MainActivity"
        android:label="@string/app_name"
        android:configChanges="keyboard|keyboardHidden|orientation|screenSize"
        android:theme="@style/SplashTheme"
        android:windowSoftInputMode="adjustResize">
        <intent-filter>
            <action android:name="android.intent.action.MAIN" />
            <category android:name="android.intent.category.LAUNCHER" />
        </intent-filter>
      </activity>
      <activity android:name="com.facebook.react.devsupport.DevSettingsActivity" />
    </application>

</manifest> 
```

现在运行应用程序，当应用程序启动时，您应该会看到闪屏显示。

## 管理环境配置

试运行和生产的区别仅仅在于应用程序名称、应用程序 id 和应用程序图标。我们可能会使用不同的 API 密钥和后端 URL 来准备和生产。

目前最流行的处理这些场景的库是 [react-native-config](https://github.com/luggit/react-native-config) ，据说它“给你的移动应用带来了 12 种爱的因素”。它需要很多步骤才能开始，我希望有一个不那么冗长的解决方案。

### 何去何从

在这篇文章中，我们接触 Xcode 和 Android Studio 多于 Visual Studio 代码，但这是不可避免的。我希望这篇文章对你有用。这里有更多的链接来阅读更多关于这个主题的内容:

*   [添加应用图标并启动屏幕以反应原生应用(iOS & Android)](https://medium.com/@scottianstewart/react-native-add-app-icons-and-launch-screens-onto-ios-and-android-apps-3bfbc20b7d4c)

*   [如何给 React 原生应用添加闪屏(iOS 和 Android)](https://medium.com/handlebar-labs/how-to-add-a-splash-screen-to-a-react-native-app-ios-and-android-30a3cec835ae)

*   [在 React Native 中管理配置](https://medium.com/differential/managing-configuration-in-react-native-cd2dfb5e6f7b)

*   [为 React 原生应用(和浪子 CircleCI 部署)pt 添加多个目标管道。1](https://medium.com/@jacks205/adding-multiple-target-pipelines-for-react-native-apps-and-fastlane-circleci-deployment-pt-1-ae9590ae52f2)

*   [(完整的)Android 闪屏指南](https://android.jlelse.eu/the-complete-android-splash-screen-guide-c7db82bce565)

原帖[https://medium . freecodecamp . org/how-to-add-app-icons-and-splash-screens-to-a-react-native-app-in-staging-and-production-d1dab 615 e7c 6](https://medium.freecodecamp.org/how-to-add-app-icons-and-splash-screens-to-a-react-native-app-in-staging-and-production-d1dab615e7c6)