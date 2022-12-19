# 使用 Android Studio 构建混合应用程序

> 原文：<https://dev.to/caperminttech/building-a-hybrid-app-with-android-studio--2491>

Android 设备的普及程度与日俱增，对 Android 应用的需求也是如此。虽然原生 Android 应用程序是使用原生移动平台构建的，但混合应用程序内置于原生应用程序中。混合应用程序是 HTML、CSS、Javascript 等 web 代码和由 Java 和 Android 框架组成的本机代码的组合。你主要需要一个本地外壳来保存应用程序，比如 Android 中的 web view，它使你能够将网页显示为活动布局。

一家 Android 应用程序开发公司更喜欢使用 Android Studio 来开发混合应用程序，因为它是谷歌 Android 操作系统的免费且易于使用的集成开发环境(IDE)。随着 2017 年 Android Studio 3.0 的推出，一系列新的工具和向导被纳入其中，以加速 Android O APIs 上的应用开发。 [Android O 面向希望使用 Android Studio 开发高质量应用的开发人员](https://www.indianappdevelopers.com/blog/android-o-developers/)，因为它允许您使用 XML 添加自定义字体和可下载字体。您可以使用这里给出的步骤用 Android Studio 创建混合应用程序。

#### 第一步:准备好所有的先决条件

在采用一种方法之前，收集所有的先决条件。这将使所有需要的东西在现场都很容易得到。您将主要需要 Android Studio、Android SDK 和 Java 开发工具包(JDK)。将 JDK 安装在通常的安装文件夹中。Android SDK 工具和 Cordova CLI 应该准备好了，安装 Node.js 版后需要安装 Cordova CLI。如果您正在构建一个纯 HTML5 移动应用程序，您还需要访问浏览器可用的功能。之后，通过运行命令“sumo npm install -g cordova”安装 Cordova CLI，并安装 Android SDK 工具。

#### 第二步:创建你的第一个项目

一旦 Cordova CLI 安装完毕并可以使用，您就可以开始创建您的项目了。使用 CD 命令，您可以通过控制台导航到要存储所有 Cordova 项目的文件夹。

#### 步骤 3:向您的项目添加平台

创建 Cordova 项目后，您需要向其中添加一个平台，以便它可以成功运行。用于创建 HTML5 移动应用程序的 HTML5 移动框架是一个很好的选择。您可以在网站上使用 jQuery 轻松创建动画、选择显示或隐藏内容等等。然后添加您的网站并编辑内容的 index.html 页面。

#### 第四步:测试应用程序

您可以使用模拟器或浏览器来测试您的应用程序。在 HTML5 框架的情况下，最好的事情是你可以通过本地服务器浏览器直接测试它，也可以通过本地 web 服务器访问你的应用程序。如果你使用的是 Chrome DevTools，也可以通过点击屏幕左上角的移动图标来模拟这个视图。您还可以使用控制台、网络、时间线等调试应用程序的内容。

#### 第五步:打包你的应用程序

为了将您的应用程序打包到本机包装器中，您可以使用第三方应用程序，如 PhoneGap 和 Xamarin，它们充当本机 API 和您的应用程序之间的桥梁。然后，这些第三方应用程序会将您的应用程序显示给用户，并在 web 视图中加载您的应用程序。之后，您可以使用“Cordova build–release”生成 APK 的发布版本，并在以下位置找到您的 APK 文件(platforms/Android/ant-build/MyApp-release-unsigned . apk)。您需要登录以向 App Store 提交应用程序。

#### 步骤 6:在设备上测试应用程序

虽然您已经在任何本地浏览器上测试了 HTML5 应用程序，但是您需要在实际设备上运行它，以便使用本地 API 进行测试。之后，您可以使用浏览器调试工具，也可以使用 GapDebug 直接在设备上进行调试。它为你提供了一个模拟 Chrome 开发工具的调试界面，并允许你安装。Android 或的 apk 文件。设备上 iOS 的 ipa 文件。然后，您的实时应用程序将反映您在该界面中所做的任何更改。

#### 第七步:分发到 App Store

调试后，您的应用程序就可以启动了，您需要在将应用程序安装到任何设备之前，将应用程序签名到 Play Store。为此，您需要一个密钥库文件。这个密钥库文件可以用 Java Keytool 实用程序创建。您可以在%JAVA_HOME%\bin 中找到这个 Java Keytool 实用程序，因为它可用于任何标准的 JDK 发行版。

#### 第八步:最终流程

最后一步是你需要跨越的最小的障碍。你需要准备好你的应用商店列表，并注册成为 Google Play 开发者。

**总结**
你也可以选择转换现有的 Android 应用程序，并为其添加混合组件。Android Studio 现在已经很好地适应了 Android O 平台，用于开发混合应用。然而，随着 [Android P developer review](https://www.indianappdevelopers.com/blog/get-ready-new-flavour-android-android-p-developer-preview/) 的发布，由于行为变化以及随之而来的新功能和 API，开发人员将很快不得不升级到更新的开发代码。这些使用 Android Studio 创建的混合应用程序帮助您在 web 和本地平台之间来回共享数据，并对它们进行有吸引力的使用。所以，你还在等什么——走出去，开始建设吧！