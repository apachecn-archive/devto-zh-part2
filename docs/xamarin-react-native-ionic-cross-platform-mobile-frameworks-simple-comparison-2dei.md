# Xamarin，React Native，Ionic:跨平台移动框架简单比较

> 原文：<https://dev.to/carl_conton/xamarin-react-native-ionic-cross-platform-mobile-frameworks-simple-comparison-2dei>

跨平台开发包括使用特殊的工具(框架)来创建基于 JavaScript 语言家族的应用程序。应用程序的整个结构和逻辑是借助 JavaScript 中的这类工具创建的，然后变成一个原生触发器元素。

## Xamarin

这个平台是微软在 2016 年收购的[。](https://blogs.microsoft.com/blog/2016/02/24/microsoft-to-acquire-xamarin-and-empower-more-developers-to-build-apps-on-any-device/)

**特性:**

邀请开发者将 C #作为编程语言。Xamarin 基于 Mono 平台的应用。

所有平台 API 都是通过 C #层类提供的。
可以重用 Android 和 iOS 上的 UI 组件- Xamarin.Forms。

如果您有一个现成的 c#-程序员团队需要转向移动开发，这个解决方案可能是一个好主意。同样值得注意的是，由于使用了 Mono，性能不会像使用 JavaScript 框架时那样受到影响。也许，这是实现项目的最佳框架之一。不过需要注意的是，移动[应用的开发在工具](https://docs.microsoft.com/en-us/xamarin/tools/)方面可能会有不适，Android Studio 和 Xcode 提供了更丰富的功能。

## 反应原生

**框架为:**

它是 React only 在移动开发中的逻辑延续。使用 JavaScript 作为编程语言。
UI 是每个平台原生的；UI 作为当前状态的函数。
通量模式的基础。

UI 被分配给单独的组件以提供[状态呈现](https://github.com/Flipboard/react-canvas)。正确地组织数据流以及在组件之间建立交互是非常重要的。这对于 cod 结构的良好组织是必要的。一旦你学会了如何为网络写作，你就可以很容易地开始在移动设备上使用同样的方法。由于 UI 是隔离的，您可以在移动和 web 解决方案之间实现大量代码重用。

## 离子型

**特性:**

基于角度的框架；在 WebView 中进行[渲染。面向具有大量控件的移动设备，以移动设备为目标。如果有必要，使用平台 API，你需要使用额外的插件。显然，与反应式脚本和本地脚本相比，工作速度较低。](https://developer.android.com/reference/android/webkit/WebView)

实践表明，在大多数情况下[跨平台移动开发](https://svitla.com/blog/major-cross-platform-mobile-development-tools)中，JS 解决方案被最有效地用作与本地组件一起工作的专用应用程序功能的一部分；实施与移动应用相关的测试假设。复杂的跨平台解决方案用于“长期”实施 SIP-drain、数学计算、图形库等组件。

## 结论

未来的混合解决方案，有时来自原生 Ul 的组件不能被丢弃，那么它可以将业务逻辑放入跨平台的 C / C ee 库中。或者重要的是做一些弹出窗口，或者设置屏幕，这些都可以用 JavaScript 轻松实现。幸运的是，没有超级万能的解决方案，为什么，幸运？由于世界的多样性，我们可以感受新技术并找到最合适的解决方案，还可以将相邻技术的优雅方法引入到正在开发的解决方案中。