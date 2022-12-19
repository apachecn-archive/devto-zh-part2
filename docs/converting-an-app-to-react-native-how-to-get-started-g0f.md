# 将应用程序转换为 React Native —如何开始

> 原文：<https://dev.to/kenneth_skovhus/converting-an-app-to-react-native-how-to-get-started-g0f>

这是将现有的原生应用迁移到 React Native 系列的第二部分。如果你想知道我们为什么选择 React Native 以及我们是如何实现它的，请查看第一篇博文。

## 引擎盖下的反应原生，速成班

为了理解 React Native 如何在移动设备上工作，首先需要看一下 React 和 React Native 中使用的虚拟 DOM(文档对象模型)。如果你曾经做过 web 开发，你应该知道 DOM 是如何在浏览器中工作的。如果没有，你可能有兴趣在上阅读一下[。简而言之:DOM 将页面表示为节点和对象。在普通的 React 中，](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model/Introduction)[虚拟 DOM](https://reactjs.org/docs/faq-internals.html) 是实际 DOM 之上的 UI 的内存表示。当一个节点或对象改变时，React 将区分虚拟 DOM 和实际 DOM，并且只更新那些已经改变的节点或对象。在 React Native 中，没有浏览器 DOM。相反，虚拟 DOM 呈现在原生 iOS 或 Android 视图中。

<figure>[![Virtual DOM in React and React Native](../Images/8c2eb858cd5e972e1b849adcc04444f4.png "Virtual DOM in React and React Native")](///static/e964a98fe5f036fb3805b0a58b517587/bcbcb/react-native-vdom.png) 

<figcaption>【虚拟 DOM】在 React 和 React Native</figcaption>

</figure>

虽然您编写普通的 JavaScript 和 JSX(JavaScript 的扩展标记语法)来定义 React 本机应用程序的外观和行为，但 UI 仍然是本机呈现的。每当其中一个节点或对象发生变化时，React Native 就会自动更新 UI 的相关部分。

这是 sets React Native 区别于其他跨平台解决方案的地方。呈现原生组件显著加快了与应用程序的交互，虽然这也引入了一些复杂性(有时您需要创建一个自定义 UI 组件)，但对我们来说，这绝对是一个好的权衡。

## 航行艰难

导航在 React Native 上一直是个小问题，但是大量可用的包表明这个领域正在开发中。理想情况下，脸书会创造或至少推荐一个解决方案。但目前来看，事实并非如此，我们都必须选择第三方库并坚持使用。对我们来说，主要的竞争者是[反应导航](https://github.com/react-navigation/react-navigation)和[wix/反应本地导航](https://github.com/wix/react-native-navigation)。第一个非常灵活，完全用 JavaScript 运行，而第二个基于两个平台上的本地导航。我们决定使用 react-native-navigation，因为我们觉得原生组件会让用户对应用程序更熟悉。

【2019 年 1 月编辑:react-native-navigation 的原生感觉并没有弥补 Android 的大量 bug，与屏幕组件的紧密耦合，以及非声明性 API。我们[迁移到反应导航](https://twitter.com/kenneth_skovhus/status/1076186546322243584)。

## 习惯 JavaScript

#### JavaScript？不，谢谢

在移动开发圈子里，大多数人一想到用 JavaScript 编写整个应用程序就畏缩不前。你为什么要做这种事？JavaScript 的名声很差，这是理所当然的。如果你十年前使用 JavaScript，你可能有过可怕的经历。适合网站上的小脚本，但不适合开发任何大规模的应用程序。

然而，事情已经发生了变化，在过去的十年中，在工具、框架和引擎方面已经做出了重大的开发努力。我们看到了 [jQuery](https://jquery.com/) 、 [V8](https://developers.google.com/v8/) 、 [Node](https://nodejs.org) 、[Backbone](http://backbonejs.org/)……所有这些努力将 JavaScript 推向了世界上最常用的语言之一。今天，谷歌的 [Angular](https://angularjs.org/) 和脸书的 [React](https://reactjs.org/) 是两个最流行的 JavaScript 框架。它们被谷歌和脸书自己使用，为应用程序开发提供了坚实的基础。

#### 现代 JavaScript

JavaScript 本身在过去几年也取得了显著的进步。当 [EcmaScript 6](http://www.ecma-international.org/ecma-262/6.0/) 问世时，开发人员终于可以使用在大多数现代编程语言中已经很常见的功能，比如类、箭头函数(又名 lambdas)、字符串插值、`let`和`const`，以及[等等](https://github.com/lukehoban/es6features)。同时， [CoffeeScript](http://coffeescript.org/) 和 [Babel](https://babeljs.io/) 开创了 [transpilation](https://scotch.io/tutorials/javascript-transpilers-what-they-are-why-we-need-them) 这样每个人都可以开始使用尚未被所有浏览器或引擎实现的新语言功能。ES 7 和 es8 一直在显著地发展这种语言，现在我们可以说 JavaScript 是一种非常好的语言。

当然，也不全是阳光和彩虹。在语言本身越来越好的同时，还是很难设置好开发环境。由于语言的动态特性，像重命名变量这样简单的事情仍然是一个挑战。来自 Android 的你可能会发现 JetBrains 的 [IntelliJ](https://www.jetbrains.com/idea/) 很有用，因为它很熟悉。当 Web 开发人员使用 React Native 时，他们倾向于使用像 [VSCode](https://code.visualstudio.com/) 或 [Atom](https://atom.io/) 这样的编辑器。只要插件支持你所需要的，你可以使用任何你想要的编辑器。

我们发现 JavaScript 的许多缺点可以通过内部编码约定和良好的工具设置来解决。一旦你养成了在合适的架构中编写优秀、地道的 JavaScript 的习惯，它就会变得非常好，即使你来自本土的 Swift 或 Kotlin。

## 工装

在我们了解了 React Native 的工作方式并决定与 JavaScript 和解之后，我们希望确保我们有正确的工具来执行最佳实践，并且我们团队中的原生开发人员在编写非惯用 JavaScript 时会有所察觉。该设置使用了来自 JavaScript 和 React 生态系统的各种工具，还帮助我们编写更易于阅读的可维护代码。

#### 静态分析和代码一致性

JavaScript 的动态和松散类型的特性使得它特别容易出现运行时错误。为了帮助我们在运行应用程序之前发现这些错误，我们使用了 [ESlint](https://eslint.org/) 。ESlint 还帮助我们看到死代码，并检测出潜入代码库的有问题的模式。我们的配置基于广泛使用的 [eslint-config-airbnb](https://www.npmjs.com/package/eslint-config-airbnb) 。

尽管 ESlint 也可以检查代码库是否符合特定的风格指南，但我们坚信代码风格应该由工具来强制实施。我们不用争论编码风格，而是用更漂亮的来格式化我们的代码。它[将](https://github.com/prettier/eslint-plugin-prettier)与 ESlint 集成在一起，所以当在我们的编辑器中点击 save 时，代码被格式化并进行静态分析。

#### 状态管理

对于状态管理，我们享受 [Redux](https://redux.js.org/) 的简单性和可测试性。我们使用 [redux-persist](https://github.com/rt2zz/redux-persist) 中间件来读写我们的 redux 存储到磁盘的部分内容。

#### 静态类型检查

我们开始在 React Native 中重建没有类型的应用程序。但是随着应用程序的增长，很明显像 Flow 或 TypeScript 这样的静态类型检查工具可以帮助我们重构和发现错误。我们用类型覆盖的代码库越多，我们发现的错误就越多。

微软的 TypeScript 和脸书的 Flow 是类似的工具，提供渐进的静态类型能力、类似的语法和广泛的使用。

对于 React Native 来说,“心流”是一个自然的选择。它与构建工具很好地集成在一起，并且大多数第三方库已经提供了流类型。

从 2019 年 1 月开始编辑:在 TypeScript 获得更多动力后，我们决定给 TypeScript 另一个改变。我们将大部分项目迁移到 TypeScript，并且没有回头。编辑器支持和库支持是👌

然而，类型检查器并不是万能的。学习曲线很陡，你发现自己在和类型系统战斗。但令我们高兴的是，这一领域正在取得许多进展。未来最有希望的选择之一是 [Reason](https://reasonml.github.io/) (也是由脸书开发的)，这是一种基于 OCaml 的类型安全语言，可以编译成可读性很强的 JavaScript。

#### 故事书作为生产力助推器

Storybook 是一个用于 UI 组件的 UI 开发环境。有了它，您可以可视化 UI 组件的不同状态，并以交互方式开发它们。

如果我们想出一个开发 UI 组件和屏幕的高效设置，它会:

*   在不启动整个应用程序的情况下，独立处理组件和屏幕
*   能够描述不同状态下的组件和屏幕，并在组件和屏幕之间快速切换
*   当样式和标记改变时支持热重新加载
*   连接多个模拟器和跨平台设备，并在更新代码时看到它们都在更新

我们很高兴看到[故事书](https://storybook.js.org)提供了所有这些。在开发 ui 时，它是主要的生产力助推器。

#### 自动化测试

对于单元和集成测试，我们使用的是[Jest](https://facebook.github.io/jest/)——脸书的另一个伟大的开源工具。它提供了一个测试框架，具有出色的观察模式、覆盖支持、相当简单的嘲讽和编写测试时的快速反馈。当它在 Node 中运行时，您模拟出所有本机组件(尽管它需要一些[设置](https://github.com/facebook/react-native/blob/1490ab12ef156bf3201882eeabfcac18a1210352/jest/setup.js))。

我们已经用 Appium 和 Amazon Device Farm 进行了跨平台 UI 自动化测试的实验。但是目前，我们关注的是一个可靠而快速的单元测试设置，它可以帮助我们捕捉错误并记录代码的预期行为。

#### 编辑器支持

团队中的每个人都使用他们喜欢的编辑器，无论是 Visual Studio 代码、Atom 还是 IntelliJ IDEA。为了获得良好且一致的开发体验，我们确保我们所有的编辑人员:

*   显示 ESlint 错误
*   在文件保存时调用 eslint - fix(我们从不手工格式化，我们有更漂亮的方法)
*   理解流声明，所以我们在编辑器中得到自动完成和类型错误

## 接下来是什么？

虽然我们对目前的设置很满意，但仍有改进的空间。我们想做的一件事是有一个大的 UI 测试集。我们还不完全确定最好的选择是什么。但总的来说，我们现在有一个坚实的基础，我们可以在这个基础上构建更多的功能，并且我们有很好的检查来确保我们的代码符合最佳实践和我们的内部风格。由于单元测试和故事书，开发周期也快了很多。

在转换为 React Native 时，我们觉得还有一件事很重要，那就是原生模块和 UI 组件。我们将在下一篇博文中讨论这个问题。

* * *

凯文·佩格林斯合著。

*这篇博客文章是从[https://skov HUS . github . io/converting-an-app-to-react-native/](https://skovhus.github.io/converting-an-app-to-react-native/)交叉发布的，最初发布在 [Leo Innovation Labs' medium](https://medium.com/leoilab/converting-an-app-to-react-native-how-to-get-started-924548ff6c62) 。*