# React Europe 2018 的主要收获

> 原文：<https://dev.to/inthepocket/our-key-takeaways-from-react-europe-2018-3n5j>

上周，来自欧洲各地的开发者聚集在巴黎参加一年一度的欧洲反应大会。由于 React 的前景变化如此之快，我们派出了自己的袖珍代表团，以了解 React 社区中所有新的和即将到来的好处。

随着 React 16 以及最近的 16.3 版本的发布，React 内核得到了彻底的革新。通过使开发人员能够优先考虑组件的重新呈现，React 已经成为一个更加强大的工具来构建跨平台的高性能接口。然而，这些功能是在去年宣布的，所以我们非常好奇今年的讲座会让我们了解到什么。

# 分型和推理的前提

在今年的 React Europe 会议上，类型化是一个热门话题，一些演讲涵盖了在编写 React 应用程序时对类型化语言的好处和需求。Caleb Meredith——曾在 Oculus 工作，之前在脸书做过 Flow 就 Javascript 中的子类型化以及使用这种模式的技术缺陷做了广泛的讨论。

使用类型化编程语言的好处是可以在开发过程中更早地发现错误，它可以确保你在开发应用程序的某些功能时不会做出错误的假设，从而减少错误。MobX 的创始人 Michel Weststrate 打了个比方:

> “当拿破仑入侵俄罗斯时，他去了莫斯科，最终损失了大部分军队。他犯了什么错？假设俄罗斯有足够的食物养活他的军队。”

为 React 编写类型化代码的一个解决方案——已经被一些有影响力的大公司采用——是 ReasonML。ReasonML 是 Ocaml 的子集，由脸书创建。Jared Foresyth 谈到今天的 React 开发如何利用代码安全&的力量。“React 不是为 Javascript 设计的”，他说。今天，我们使用模式(类型，不变性，...)不是 Javascript 自带的。我们已经采用了像 Flow 这样的 devtools，但是用 Flow 建立一个定制的类型系统的过程通常是麻烦和耗时的。

今天采用 React ml 创建 React 应用程序在 Javascript 开发上仍然有一些缺点，因为社区更小，可用的包更少，并且一些常用的 API 例如 Firebase 没有 React ml 实现。然而，我认为我们即将迎来一个转折点。今天，ReasonML 仍然可以编译成 Javascript，但是负责 ReasonML 的团队已经宣布他们正在开发一个交叉编译工具，可以导出为本机代码！这意味着我们可以完全在移动设备的主线程上运行 React 原生应用。

# React 原生于网络:重访

去年，我们注意到社区中越来越多的开发人员在尝试 React 的跨平台实现，比如 React Native Web——我们今年早些时候在 React Academy 上演示了它。会议上引起广泛关注的一个演讲是文森特·里默尔的“将 React Native 连接到它的根源”。

作为实验，他为 Yoga 做了一个浏览器实现——与 powers React Native 相同的布局框架。但是他偶然发现用 Yoga 在浏览器中渲染整个应用程序，这根本不是 performant。然后，他尝试在 webworker 中运行 React，但是遇到了技术限制，放弃了这个项目。

快进几个月。与此同时，文森特与 React Native 合作，并开始尝试 React Native Web。他偶然发现 React Native Web 并不是真正的原生，并且有太多的 Web 优先的方法。他想创造一种开发体验，允许他编写 React 本机代码并在任何地方运行。

输入 [React Native Dom](https://github.com/vincentriemer/react-native-dom) 。React Native Dom 是 React Native 的一个 web 浏览器实现。遵循原生架构，所有应用和组件逻辑都在 webworker 上运行，而主线程可以完全专注于渲染。使用 Yoga 来渲染布局，我们可以在浏览器中访问所有的动画效果，就像我们会在本机设备上使用它一样。最后但同样重要的是，React Native Dom 使用与现有 React Native 项目相同的 bundler，允许熟悉的开发和调试过程。

演示结束后，房间里充满了兴奋。

# 超越 React:未来一瞥

通常，当 Airbnb 派一个演讲者参加会议时，很酷的事情必然会发生。Airbnb 开发者和设计工具的创造者 Devin Abbott 走上舞台。

> “我们应该用 React 吗？答案永远是肯定的。”

React 的未来是这样的，我们可以一次编写应用程序，然后编译它，这样我们就可以在任何地方运行它，而无需对我们的堆栈进行更改，这是目前使用跨平台 React 的主要成本。

在展示 React 组件被实时传输到 Swift 的演示之后——是的，确实发生了——Abbott 说:“React 是一种用于在编译时描述组件的数据格式”。要描述一个组件，你只需要一个 JSON。然而，在 Airbnb，他们提议使用 React 原生组件作为真实的单一来源，因为它们比 JSON 数据更容易可视化。

鼓声。介绍[洛娜](https://github.com/airbnb/Lona)。

Lona 是开发者的设计工具，也是设计者的开发工具。构建组件很无聊，Lona 允许你可视化地构建和维护你的组件。基本上故事书和素描相遇。它仍处于早期测试阶段，但演示看起来很惊人！

# 反应过来就呆在这里

看到 React 社区如何继续努力使 React 成为一个真正的跨平台框架真的很令人兴奋。本周，我们看到了一些新的有趣的提议来改善 React 开发人员的体验——通过使用类型化语言。面向设计人员和开发人员的有前途的新协作工具，将简化流程并提高生产率。最重要的是，这证明了一个庞大而活跃的社区。

展望未来，React 生态系统正在发展成为一个强大的跨平台框架，用于构建移动、web 和 AR/VR 应用。我们对在巴黎发布的新功能和新库充满了期待。请关注我们的下一个 React Native Academy！

最初贴在口袋里的[上。](https://www.inthepocket.com/)