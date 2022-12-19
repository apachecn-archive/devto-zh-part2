# 重新介绍离子(离子 4+，PWAs，模板，电容器和电子)

> 原文：<https://dev.to/techiediaries/a-re-introduction-to-ionic-ionic-4-pwas-stencil-capacitor-and--electron-21lp>

如果你一直在关注 Ionic 框架，那么你可能会注意到一些新技术/工具和概念，主要是 pwa(渐进式网络应用)、Stencil、Capacitor 和 Native PWAs。你对此感到困惑/担心吗？不要这样，这一切都是相关的，并且有一个最终目标，那就是让 Ionic 成为一个强大的**框架无关的**库，用于构建移动(渐进式)web 应用，这些应用可以一流地访问本地设备功能。

朝向新离子的主要部件是**模版**和**电容器**

如果你要向一个新的开发者描述离子键，你会告诉他离子键是一个在 Angular 和 Cordova 之上构建混合移动应用的框架(移动网络应用，本质上可以本地访问用户设备，并且可以通过应用商店安装)

但实际上 Ionic 并不是一个框架，它是一个建立在 Angular 之上的 UI(用户界面)库，所以这里真正的框架是 Angular，拥有漂亮的移动 UI 组件和最佳模式(列表和虚拟滚动等)。)

不仅仅是 UI，Ionic 还可以访问移动平台(Android 和 iOS 等)的原生功能。)感谢独立工具 Apache Cordova(它本质上是一个运行时和一组插件，允许您从 JavaScript 调用移动设备的原生功能)

因此，对你的新 Ionic 开发者朋友来说，更好的重新定义应该是:**这是一个基于 Cordova 的移动 UI 库，它使用 Angular(以前是 Angular.js)** 来处理你在构建客户端 web 应用程序时使用的任何框架/Angular(结构、助手库、依赖注入等强设计模式)。)

科尔多瓦和 Angular 构成了爱奥尼亚的两大基石技术。没有任何一个都不能用爱奥尼亚

因此，我们有**模板**和**电容器**用于新的现代和未来爱奥尼亚的两个角石和 **Cordova** 和 **Angular** 用于旧爱奥尼亚的两个角石(未来的爱奥尼亚版本仍支持)。你能看出模板和电容在**vs**Cordova 和 Angular 中的位置吗？如果还没有，也许你需要刷新一下什么是模板和电容器？

Stencil 基本上是一个 web 组件编译器，它使用一组类似于现代 web 框架中的功能(如类型脚本支持、JSX、异步呈现)来简化 web 组件的创作。在这一点上，你会明白模板是角的替代品？答案是肯定的(但不是直接的)和否定的。肯定是因为 Stencil 的整个目的是使 Ionic 框架不可知，也就是说，你将在没有任何框架的情况下使用 Ionic，否定是因为，作为一名 Ionic 开发者，你实际上不需要使用甚至不知道 Stencil。Ionic 团队在幕后使用它来构建和生成在现代 web 浏览器中本地支持的 web 组件，因此 Ionic 组件将成为在现代 web 中随处可用的 web 组件，并且在运行时不需要 Stencil(与 Angular 不同)，因此基本上**标准兼容的 web 组件是 Ionic 4+** 中 Angular 的替代品(不要担心，您仍然可以像以前一样使用 Ionic/Angular，它具有所有以前的功能和构造，但性能更好)。此外，如果你是一个反应，行动或 Vue 等。开发者你可以使用这些函数库和 Ionic 来构建你的移动应用。

Ionic 团队使用 Stencil 来构建 Ionic 组件，但是你也可以用它来构建你自己的 web 组件，甚至是完整的(也是最快的)应用程序。有两个官方模板，一个用于[构建独立的 web 组件](https://github.com/ionic-team/stencil-component-starter)，一个用于构建应用的[应用模板。](https://github.com/ionic-team/stencil-app-starter)

从 Ionic 4 开始，Ionic 将使用 web 组件作为 UI 组件，所以即使你不想使用 Stencil 作为开发工具，你仍然可以获得性能和跨框架使用的好处。

我希望你现在明白模板在等式中的位置。

现在让我们继续讨论**电容与 Cordova**

首先什么是电容器？这是来自 https://capacitor.ionicframework.com/docs/的官方定义

> Capacitor 是一个跨平台的应用程序运行时，可以轻松构建在 iOS、Android、electronic 和 web 上运行的 web 应用程序。我们称这些应用为“原生渐进式网络应用”，它们代表了混合应用之外的下一次发展。

我们来分解一下

*   电容器是一个跨平台的运行时，就像科尔多瓦，除了它有对网络和电子(跨平台桌面应用程序)的本地支持
*   电容器可用于构建在移动设备中也具有本地接入的 pwa

爱奥尼亚已经在科尔多瓦的基础上建立了爱奥尼亚原生层。当我第一次听说 Ionic Native 时，我认为它是 Cordova 的替代产品，但它只是一个现代的基于 Promise/Observable 的 TypeScript 接口，包装了现有的 Cordova 插件(这些插件使用了所有人都不喜欢的 JavaScript 回调——参见[回调地狱](http://callbackhell.com/)和[从回调地狱大逃亡](https://itnext.io/the-great-escape-from-callback-hell-3006fa2c82e)),并确保本地事件触发 Angular 中的变化检测。

Ionic Native 允许开发者模仿插件，这些插件可以用于许多伟大的任务。看看如何通过模仿需要真实设备的插件(如 SQLite 插件)和[模仿 Ionic Native 3.x 插件](https://www.techiediaries.com/mocking-ionic-native-3-x-plugins/)来[完全在浏览器中开发 Ionic 应用。](https://www.techiediaries.com/mocking-native-sqlite-plugin/)

作为 Ionic 项目的一部分，Ionic 的原生库和插件是最接近移动原生平台/特性的东西。但它仍然是科尔多瓦，有它的设计原则，局限性，优点和缺点。这就是为什么在未来的版本中，Capacitor 是 Cordova 的替代品，具有现代的工具和功能以及 Ionic 的官方原生层。

你可能会问:他们在重新发明轮子吗？

在我看来，这并不是因为现代特性是值得的，另一个像 Cordova 这样的开源软件或者更好的由最流行的混合框架 Ionic 支持的软件肯定会有它的位置。

让我们再次引用电容器文档

> 电容器是 Apache Cordova 和 Adobe PhoneGap 的精神继承者，灵感来自其他流行的跨平台工具，如 React Native 和 Turbolinks，但完全专注于使现代 web 应用程序能够在所有主要平台上轻松运行。电容器对许多现有的 Cordova 插件有向后兼容的支持。

让我们也打破这一点

*   电容器是科尔多瓦(和 PhoneGap)的继任者，所以这意味着它需要考虑科尔多瓦的弱点，并避免它们，我们将只看到当项目推进时如何实现
*   电容器也从 React Native 等工具中获得灵感，这些工具使用了与科尔多瓦不同的方法
*   电容器有一个兼容层，为现有的 Cordova 插件提供支持

### 原生外壳插件

Capacitor 将支持使用本地 shell 附加组件，如菜单、标签和导航，并支持 web 等效功能。

移动应用的原生用户界面有许多优势，例如

原生用户界面的优势

*   表演
*   本机外观和感觉
*   更加尊重用户可访问性设置等。

科尔多瓦有相当于原生插件的东西。

Cordova 允许访问带有许多插件的原生 UI，例如[对话框、警告、提示和动作表等。](https://developer.telerik.com/featured/adding-native-touches-hybrid-app/)

另请参见微软的 [ACE](https://github.com/microsoft/ace) Cordova 插件，该插件允许使用真正的原生 ui 构建 Cordova 应用程序，但由于 GitHub 存储库中列出的原因，该插件不由 MS 维护。

### 电子支持

电容器也将支持电子作为一个目标平台，所以你的电容器/离子应用也将针对桌面平台除了网络和移动平台。

## 什么是 PWA？

PWAs 或渐进式网络应用程序是网络应用程序

### 原生 PWAs 怎么样？

渐进式网络应用在电容器中具有第一等级，并且由于它是原生层，这些 pwa 也可以访问原生特性，这就是原生 pwa 名称背后的原因

## 离子原生 5？

Ionic Native 5 也被更新为框架不可知的状态，这是向有框架或没有框架的 Ionic 迈出的一步。你现在可以将 Ionic Native 与 Angular.js 一起使用，因此如果你仍然想使用 Angular.js 来构建你的 Ionic 应用程序(如 Ionic 1)，你就不再需要绝对的 ngCordova 了

Ionic Native 5 有三个可用的捆绑包，可以以不同的方式使用:

*   使用角度 5+提供者，
*   使用 ES6 模块(静态使用插件)
*   使用 Angular.js 模块(用于 Angular.js 支持)

## 结论

从 Ionic 4 和 Ionic Native 5 开始，您将能够使用一些未来的 Ionic，这将改变您构建跨平台应用程序的方式，这些应用程序可以在多个移动平台、web、渐进式 web 应用程序和桌面平台上运行。

我还喜欢围绕 Ionic 的生态系统，即 Stencil、Capacitor 和 Ionic Native 5，因为这些工具可以与 Ionic 分开使用，以构建成熟的尖端应用程序，所以请继续收听教程，我将介绍所有这些令人惊叹的工具来构建不同的应用程序。