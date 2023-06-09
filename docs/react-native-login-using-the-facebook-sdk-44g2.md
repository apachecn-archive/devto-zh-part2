# 使用脸书 SDK 对本地登录做出反应

> 原文：<https://dev.to/onmyway133/react-native-login-using-the-facebook-sdk-44g2>

[![](img/0e322e2fe412f075f772609de2e5c0a2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MaJGrG0a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2Aa2OpEbjt26rR2bIK)

Native 有许多依赖项，每个依赖项都依赖于大量其他依赖项，而且它们移动得相当快(并破坏事物)。有时两个框架合并在一起，有时一个框架分裂成两个，这可能会导致你的应用程序发生重大变化。虽然其中大部分都是众所周知的问题，可以通过谷歌搜索解决。对我来说，React Native 节省了一些(起初)为 iOS 和 Android 开发的时间，同时让我有更多的时间去钻研[问题](https://github.com/facebook/react-native/issues)和[问题](https://stackoverflow.com/questions/tagged/react-native)。

根据团队偏好和项目，React Native 可能是一个考虑因素。如果你需要用脸书 SDK 构建 React 原生应用，这篇文章就是为你准备的。它展示了如何为使用 [React 导航](https://reactnavigation.org/)和 [react-native-elements](https://github.com/react-native-training/react-native-elements) 的 React 原生应用实现脸书 SDK，以及我对 React Native 的体验。

截至本帖时间，我使用的是 macOS 10.13.3，Xcode 9.3，Android Studio 3.1.4，react-native-cli 2.0.1，npm 6.3.0，node 8.9.4，nodenv 1.1.2。我过去常常使用 [yarn](https://yarnpkg.com/en/) ，但是现在我发现 npm 工作得很好，所以让我们坚持使用 npm。

我用 Visual Studio 代码做编辑器，你最需要记住的热键是 Shift + Option + F，用来格式化代码。

[![](img/750b86a0c506a3937538174be3d48a3b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--N6T3nvYG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2408/1%2An4QWEW0p9jLRlM72zBoIUw.png)

## 创建 React 原生 app

为 React 应用程序设置环境和工具是很痛苦的。了解如何配置 Webpack、Babel、Jest 可能需要一些时间，因此人们为这个设置任务构建了许多样板文件生成器。

我对这个 [create-react-app](https://github.com/facebook/create-react-app) 很熟悉，所以当我发现 [create-react-native-app](https://github.com/react-community/create-react-native-app) 时，这也是官方 [React Native 入门](https://facebook.github.io/react-native/docs/getting-started.html)指南中推荐的，我立即尝试了一下。根据我的经验，这似乎是通过 Expo 玩 React Native 的一种快捷方式，但有太多的[警告](https://facebook.github.io/react-native/docs/getting-started.html#caveats)。虽然这种方法拒绝退出游乐场模式，但从长远来看，它会限制我。

> 因为在使用 Create React Native App 创建项目时不构建任何本机代码，所以除了 Expo 客户端应用中可用的 React 本机 API 和组件之外，不可能包含自定义本机模块。
> [**React-native 第一印象:Expo vs Native**
> *我最近第一次涉足 React-native，同时试图将 motogo 移植到 Android。Motogo 是一个应用程序，可以让…*medium.com](https://medium.com/@paulsc/react-native-first-impressions-expo-vs-native-9565cce44c92)

我认为他们应该[将](https://github.com/react-community/create-react-native-app/issues/742)重新命名为“repo to create-react-native-Expo-app”以避免[混乱](https://github.com/react-community/create-react-native-app/issues/516)。在使用 React Native 时，我的一点建议是，由于它的严重依赖性，您应该对您想要添加的每个工具或框架进行一些研究。依赖性越少越好。在很多情况下，这些东西是不必要的，你可以避免额外的不必要的依赖。

官方的 react-native init 工作得非常好，关于如何安装这个工具，请访问使用本机代码的 [Building Project。](https://facebook.github.io/react-native/docs/getting-started.html#installing-dependencies)

## 正在运行的应用程序

要运行 iOS 应用程序，您应该指定模拟器

react-native run-IOs—simulator = " iPhone X "

运行 Android app，要先启动 emulator，要么通过 Android Studio，要么通过 adb，然后 react-native run-android。

## 安装脸书 SDK

React Native 的[脸书 SDK 的设置非常简单。基本上它是围绕 iOS 和 Android 的原生 SDK 的包装器，所以我们需要使用 Xcode 和 Android Studio 来配置一些任务。你还需要在](https://github.com/facebook/react-native-fbsdk)[脸书开发者](https://developers.facebook.com/)页面注册一个应用。

首先，安装并链接 react-native-fbsdk 包

```
react-native install react-native-fbsdk
react-native link react-native-fbsdk 
```

Enter fullscreen mode Exit fullscreen mode

这给了你 react-native-fbsdk": "^.7.0 在你的 package.json 的依赖项下，这意味着它将 react-native-fbsdk 集成到 iOS 和 Android。

> Android 项目:你可以跳过 build.gradle 的修改，因为上面的 rnpm 链接步骤已经完成了

### iOS 设置

[react-native-fbsdk](https://github.com/facebook/react-native-fbsdk) 为我们指出了[入门](https://developers.facebook.com/docs/ios/getting-started/)，但它是针对原生 iOS 应用的！！！

相反，你应该按照[配置原生 iOS 项目](https://developers.facebook.com/docs/react-native/configure-ios/)，因为它有一个设置所有事情的脚本。首先，进入[快速入门](https://developers.facebook.com/quickstarts/?platform=ios)了解如何更改捆绑 id、配置 Info.plist 和添加跟踪应用安装和应用打开代码。请注意，在 react-native Xcode 项目中已经有了方法 didFinishLaunchingWithOptions，所以您不应该添加新的方法。

**如何更改 iOS 项目捆绑 id？**

这是一个非常常见的[问题](https://stackoverflow.com/questions/36119754/how-to-change-the-bundle-identifier-within-react-native),人们指向各种各样的脚本和框架来改变 bundle id。你只需要打开 ios 文件夹中的. xcproj，改变 bundle id，就像你用普通的原生 iOS 项目一样。

然后返回到[在步骤 7 配置本地 iOS 项目](https://developers.facebook.com/docs/react-native/configure-ios/)以安装并执行 ios_setup.js

```
curl -O [https://raw.githubusercontent.com/facebook/react-native-fbsdk/master/bin/ios_setup.js](https://raw.githubusercontent.com/facebook/react-native-fbsdk/master/bin/ios_setup.js)
npm install plist xcode adm-zip
node ios_setup.js [App ID] [App Name] 
```

Enter fullscreen mode Exit fullscreen mode

如果你检查[脚本](https://raw.githubusercontent.com/facebook/react-native-fbsdk/master/bin/ios_setup.js)，它会为我们安装 FBSDKCoreKit、FBSDKShareKit、FBSDKLoginKit。

```
const target = myProj.getFirstTarget().uuid;
    myProj.addFramework('./ios/Frameworks/FBSDKCoreKit.framework', { 'customFramework': true, 'target': target, 'link': true });
    myProj.addFramework('./ios/Frameworks/FBSDKShareKit.framework', { 'customFramework': true, 'target': target, 'link': true });
    myProj.addFramework('./ios/Frameworks/FBSDKLoginKit.framework', { 'customFramework': true, 'target': target, 'link': true }); 
```

Enter fullscreen mode Exit fullscreen mode

### Android 设置

遵循 [Android 与 React 原生 v0.30+项目配置](https://developers.facebook.com/docs/react-native/configure-android-current)并在 MainApplication.java 和 MainActivity.java 添加脸书 SDK 代码

同样如 [react-native-fbsdk](https://github.com/facebook/react-native-fbsdk) 所述，你应该声明脸书应用 Id。跟随【Android 平台快速入门了解如何在 strings.xml 中添加脸书应用 id，在 AndroidManifest.xml 中声明 com.facebook.sdk.ApplicationId

如果您想更改 Android 项目的包名，您可以在 Visual Studio 代码中这样做，并记住更新相关的包名。或者推荐的方式是在 [Android Studio](https://stackoverflow.com/questions/16804093/android-studio-rename-package) 中进行

[![](img/77dca071972d0079c12db6ee3f10c8bb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6jpUkTqq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2AAg65Wtg_fXrX4Rd5.png)

我[遇到了](https://stackoverflow.com/questions/48769719/runtimeexception-the-facebook-sdk-must-be-initialized-before-calling-activateap?rq=1) [和](https://stackoverflow.com/questions/44544866/deprecated-facebooksdk-method-throws-runtimeexception) [的 sdkInitialize 问题](https://stackoverflow.com/questions/41884349/facebooksdk-sdkinitialize-context-is-deprecated)，解决方案是删除它们。这是 onCreate 在 MainApplication.java 的样子

```
@Override
public void onCreate() {
  super.onCreate();
  SoLoader.*init*(this, /* native exopackage */ false);
} 
```

Enter fullscreen mode Exit fullscreen mode

## 导航

React Native 有很多导航框架，但目前最流行的是 [React Navigation](https://reactnavigation.org/) 。让我们从添加 npm 安装—保存反应—导航开始。

我们将创建 LoginNavigator 来管理登录流，创建 MainNavigator 来管理登录后的流。我们根据脸书的登录状态在两者之间切换。我喜欢将代码分成组件，这是文件夹结构

[![](img/b87bee99e06db8188c7fb18e24aa3413.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_avFjouw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AYRP0-3uHB3BEUxrf1MZ8Fg.png)

## 切换导航器

从这个项目中的 react-navigation 2.11.2 开始，它使用 makeXXX 来创建导航器。让我们根据脸书访问令牌的存在，使用 SwitchNavigator 在 MainNavigator 和 LoginNavigator 之间切换

下面是邮件导航器，它使用底部标签导航器

目前，LoginNavigator 只是一个只有 LoginPage 的 StackNavigator

## 登录脸书

让我们做一个方便的 FacebookService 来对付脸书 SDK

在这里，我们将 GraphRequest 包装在 Promise 中，以使用异步 await 样式，您可以阅读有关异步 await
[**回调、Promises 和异步/Await**
的更多信息。这是我试图在很高的层次上解释如何在 Javascript 中使用异步代码。*medium.com](https://medium.com/front-end-hacking/callbacks-promises-and-async-await-ad4756e01d90)

以及导出常量与导出默认的区别
[**Javascript (ES6)，导出常量 vs 导出默认**
*这是一个命名导出 vs 默认导出。export const 是带有 const 关键字的命名导出。默认导出(export…*stackoverflow.com](https://stackoverflow.com/questions/33611812/javascript-es6-export-const-vs-export-default)

### 在 LoginPage 中使用 FacebookService 登录

由 react-navigation 管理的每个组件都有一个名为 navigation 的属性，我们可以调用它来转换到新页面。
[ **在屏幕间移动 React 导航**
*在上一节[“你好 React 导航”](//hello-react-navigation.html)中，我们用两个……* React Navigation . org】([https://reactnavigation.org/docs/en/navigating.html](https://reactnavigation.org/docs/en/navigating.html))定义了一个堆栈导航器

我们可以手动使用 [LoginManager](https://developers.facebook.com/docs/react-native/login/) 来进行我的精细控制，但是这里的 LoginButton 工作得很好。它还根据登录状态显示登录或注销文本。

请注意，我们将登录方法绑定到 LogInPage，以正确处理 React
[**中的事件，这就是为什么我们需要在 React**
的类组件中绑定事件处理程序。我们需要绑定这些…*medium.freecodecamp.org](https://medium.freecodecamp.org/this-is-why-we-need-to-bind-event-handlers-in-class-components-in-react-f7ea1a6f93eb)

react-navigation 支持在导航器之间传递[参数](https://reactnavigation.org/docs/en/params.html)，但是这里我们只需要来自 FacebookService 的访问令牌，我们可以很容易地检索它。

请注意，如果您发出大量脸书请求，您可能会得到[限制](https://stackoverflow.com/questions/14092989/facebook-api-4-application-request-limit-reached)，并且在登录按钮
[**中声明读取和发布权限存在问题使用 facebook sdk 4.6.0**
【中的读取和发布权限*所以我正在使用 facebook api，我想在拥有读取权限的同时设置发布权限，所以我编写了…*stackoverflow.com](https://stackoverflow.com/questions/32743545/using-read-and-publish-permissions-in-facebook-sdk-4-6-0)

## 在个人资料页面显示脸书信息

对于 React Native 中一个很酷的 UI 工具包，让我们使用 [react-native-elements](https://github.com/react-native-training/react-native-elements) 。我们在个人资料页面中使用它

由于 ProfilePage 是在 react-navigation 中管理的，因此我们可以使用 this . props . navigation . navigate(' log in navigator ')轻松地过渡到新的 MainNavigator。我还用 safarea View 让它在 iPhone X 上看起来很好看 safarea View 默认为空视图在[Android](https://github.com/facebook/react-native/blob/master/Libraries/Components/SafeAreaView/SafeAreaView.android.js)
[**React Native&iPhone X**
*用 safarea View * medium . com](https://medium.com/react-native-training/react-native-iphonex-92ff511282af)轻松更新和创建 iPhone X 布局

ProfileView 是包含脸书配置文件信息的标题，这是为了在布局方面划分职责。

## 提示和故障排除

在使用 React Native 和脸书 SDK 的过程中，我遇到了一些问题，也学到了很多东西。大多数时候，这些都是众所周知的问题，在网上搜索一下就能找到答案。下面是一些可能有帮助的提示和故障排除。

### 几个小技巧

[* *如何在 Android 中打印控制台日志](https://stackoverflow.com/questions/36750531/how-to-print-console-log-in-reactnative-android) **

按照[调试](https://facebook.github.io/react-native/docs/debugging.html)运行 react-native log-android 或者 adb logcat。也按 Cmd+M 打开实时重新加载菜单。关于如何将 adb 添加到路径，请运行

```
export PATH="/Users/khoa/Library/Android/sdk/platform-tools":$PATH 
```

Enter fullscreen mode Exit fullscreen mode

[* *配置 Android SDK](https://stackoverflow.com/questions/32634352/react-native-android-build-failed-sdk-location-not-found) **

将 SDK . dir =/Users/USERNAME/Library/Android/SDK 添加到 local.properties 文件

**了解 React 中的模块系统**

阅读下面这篇令人敬畏的博文，你将不再困惑
[**需要 Node.js 中的模块:你需要知道的一切**
*Node 使用两个核心模块来管理模块依赖:*medium.freecodecamp.org](https://medium.freecodecamp.org/requiring-modules-in-node-js-everything-you-need-to-know-e7fbd119be8)

### 故障排除

没有什么是免费的。React Native 的强大带来了许多问题，你无法相信你在[问题](https://github.com/facebook/react-native/issues)中浪费了多少时间。

**不存在捆绑包 URL**

检查导入的模块名称是否正确，不区分大小写通常是罪魁祸首。此外，大部分时间缓存或错误的状态是问题，在那里你只需要清除缓存 rm -rf ios/build/或杀死当前进程 kill $(lsof -t -i:8081)

您可能还需要关闭/重置 iOS 模拟器和 Android 模拟器中的内容。确保 100%看到捆绑包。

**React-Native:模块 AppRegistry 不是已注册的可调用模块**

您应该小心[何时使用括号来导入](https://stackoverflow.com/questions/36795819/when-should-i-use-curly-braces-for-es6-import)

**可能未处理的承诺拒绝**

这只是警告，但你在处理承诺时应该抓住

**打包程序无法监听端口 8081**

要么使用不同的端口 react-native start - port=8088 启动，要么查找使用端口 8081 的进程 sudo lsof -i :8081 并使用其 PID kill -9 PID 终止

## 何去何从

我希望这篇文章对将脸书 SDK 集成到 React 原生应用有所帮助。此外，我认为学习变量作用域和 Flexbox 如何工作，从长远来看，对 React Native 有好处。

**了解 var**

Javascript 可能是一种奇怪的语言，但是在你[理解](https://codeburst.io/difference-between-let-and-var-in-javascript-537410b2d707)块作用域和函数作用域如何与 var、let、const 一起工作之前，尤其是在[循环](https://stackoverflow.com/questions/10801747/dealing-with-duplicate-declaration-warning-for-loop-variables)中

**学习 Flexbox**

Flexbox 是 React 应用程序中进行布局的首选方式。做错了，整个布局就会消失或混乱。
[**了解 React Native 中的 Flexbox**
*掌握这些简单的 Flexbox 食谱将使你很快成为 React Native UI 大师* blog . React Native coach . com](https://blog.reactnativecoach.com/understanding-flex-in-react-native-b34dfb4b16d1)
[**Flexbox 如何工作——用大的、丰富多彩的、动画 gif**
解释*Flexbox 承诺将我们从普通 CSS(如垂直对齐)的罪恶中拯救出来。* medium . freecodecamp . org](https://medium.freecodecamp.org/an-animated-guide-to-flexbox-d280cf6afc35)
[**完整的 React 原生布局备忘单**
*包含所有主要 React 原生布局属性实例的简单直观指南*medium.com](https://medium.com/@drorbiran/the-full-react-native-layout-cheat-sheet-a4147802405c)

原帖[https://medium . com/react-native-training/Facebook-SDK-with-log in-for-react-native-apps-in-2018-196601804 f66](https://medium.com/react-native-training/facebook-sdk-with-login-for-react-native-apps-in-2018-196601804f66)