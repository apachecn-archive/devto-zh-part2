# 前端+每周第 10 期:D3 5.0，React 和 React 原生事件系统讲解，SketchCode

> 原文：<https://dev.to/wxyyxc1992/frontend-weekly-no10-d3-50react-and-react-native-event-system-explained-sketchcode-2gck>

[![周报封面 56.jpg](../Images/c517d2750ea9e9e929583a1665a1cbea.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--z6ufiTu1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://upload-images.jianshu.io/upload_images/1647496-b6ba72596e038da7.jpg%3FimageMogr2/auto-orient/strip%257CimageView2/2/w/1240)

与前端开发相关的最佳文章、链接和新闻等，每周发布一次。本期所有参考资料均来自[媒体](https://medium.com/@384924552)、[黑客新闻](https://news.ycombinator.com/news)、 [Reddit](//reddit.com) 、 [Twitter](//twitter.com) 、 [MyBridge](//mybridge.co) 等。

更多问题可以在[前端+周库](https://parg.co/U9x)中找到。

顺便说一句，很抱歉我的英语不好。

## 新闻

*   D3 5.0 : D3 5.0 只引入了一些不向后兼容的变化。D3 现在使用承诺代替异步回调来加载数据。承诺简化了异步代码的结构，尤其是在支持异步和等待的现代浏览器中。随着 promises 的采用，D3 现在使用 Fetch API 而不是 XMLHttpRequest，d3-request 模块已经被 d3-fetch 取代。关于这个的更多链接可以在[牛逼链接](https://github.com/wxyyxc1992/Awesome-Links/blob/master/README-en.md)中找到

*   [Java 10 发布](https://www.infoworld.com/article/3230507/java/java-jdk-10-what-new-features-to-expect-in-the-next-java.html) : JDK 10，Java 标准版 10 的实现，于 2018 年 3 月 20 日发布。关键的改进包括局部变量类型以及垃圾收集和编译的增强。JDK 10 计划只是短期发布，JDK 10 的公开更新预计将在六个月后结束。即将于 9 月发布的 JDK 11 将是 Java 的长期支持(LTS)版本。LTS 每三年发布一次。关于这个的更多链接可以在[牛逼链接](https://github.com/wxyyxc1992/Awesome-Links/blob/master/README-en.md)中找到

## 教程

*   [如何使用 Flutter SDK 构建加密货币价格表应用](https://parg.co/U2K) : Flutter 是谷歌新的开源工具包，用于帮助开发人员只用一个代码库构建 iOS 和 Android 应用。Flutter 应用程序是用 Dart 编程语言编写的，并编译成本机代码，因此性能非常非常好。在本教程中，我将向您展示如何使用 Flutter 构建一个显示不同加密货币当前价格的应用程序。我将带你了解颤振和飞镖的基本原理。关于这个的更多链接可以在[牛逼链接](https://github.com/wxyyxc1992/Awesome-Links/blob/master/README-en.md)中找到

*   [2018 年 React.js 综合指南](https://medium.freecodecamp.org/a-comprehensive-guide-to-react-js-in-2018-ba8bb6975597):这篇文章最初发表于 2015 年 1 月，但最近更新到 React 16.3 及其包含的所有优点。下面的主题是我认为 React 的基本方面。如果你理解了它们和它们的目的，在阅读完本教程后，你将处于一个非常好的位置:JSX，虚拟 DOM，反应。组件、状态、组件生命周期、事件。关于这个的更多链接可以在[牛逼链接](https://github.com/wxyyxc1992/Awesome-Links/blob/master/README-en.md)中找到

*   [介绍 React Apollo 2.1](https://dev-blog.apollodata.com/introducing-react-apollo-2-1-c837cc23d926) :最近，React Apollo 2.1 发布了，这是改进使用 GraphQL 开发 React 应用程序的一大进步。它具有新的渲染属性 API 和更强的类型脚本支持，以及升级的文档。在这篇博文中，我们将介绍以下特性:用查询获取数据，用突变更新数据，用 ApolloConsumer 简化本地状态，等等。关于这个的更多链接可以在[牛逼链接](https://github.com/wxyyxc1992/Awesome-Links/blob/master/README-en.md)中找到

## 工程实践

*   [比较 React 应用中使用的顶级 JS 动画库](https://parg.co/Ux9):你可能已经注意到动画在网络上非常流行。针对运动爱好者的教程、案例研究和图书馆并不缺乏。这是开发的一个方面，可以激发、增强、愉悦、激励和打动用户。在本文中，我选择了五个 JavaScript 动画库，并从商业和开发的角度剖析了它们的性能。而不是问“我用什么来构建 X？”，我探索了这样的问题:这个项目维护的怎么样？一个团队容易接吗？语法是什么样的？关于这个的更多链接可以在[牛逼链接](https://github.com/wxyyxc1992/Awesome-Links/blob/master/README-en.md)中找到

*   [我们如何大规模采用 CSS 网格](https://julian.is/article/css-grid-at-scale/):上个月我们启动了对 Thomasnet.com 的重新设计，从团队买入到后退，这是我们在 Thomas 采用的 CSS 网格实施方法。这篇博客包含以下内容:CSS 网格的快速入门，我们的网格“啊哈”时刻，解决 CSS 网格与 Flexbox 的争论，奠定网格基础，没有支持的浏览器的后退。关于这个的更多链接可以在[牛逼链接](https://github.com/wxyyxc1992/Awesome-Links/blob/master/README-en.md)中找到

*   [终极 React 组件模式与 Typescript 2.8](https://levelup.gitconnected.com/ultimate-react-component-patterns-with-typescript-2-8-82990c516935) :有状态、无状态、默认道具、渲染回调、组件注入、通用组件、高阶组件、受控组件。关于这个的更多链接可以在 [React DevPractices 链接](https://github.com/wxyyxc1992/Awesome-Reference/blob/master/Web/Framework/React/React-DevPractices-Links.md)中找到。

## 引擎盖下

*   [压缩图像重访](https://timkadlec.com/remembers/2018-03-22-compressive-images-revisited/):压缩图像技术依赖于你将 JPG 图像的尺寸调整到比它最终显示的尺寸更大，然后将其压缩到令人难以置信的低质量设置。这极大地降低了图像的重量，但也使图像看起来非常糟糕。重量上的好处是巨大的。在 Filament Group 的文章中，示例图像轻了 53%(从 95kb 到 44kb)。压缩图像的代价主要是内存成本(过去存在缩放和解码风险，但浏览器在这方面有所改进)。关于这个的更多链接可以在[牛逼链接](https://github.com/wxyyxc1992/Awesome-Links/blob/master/README-en.md)中找到

*   [使用深度学习的自动化前端开发](https://parg.co/UDc):在 Insight，他开发了一个模型，允许用户从手绘线框创建工作 HTML 网站，大大加快了设计过程。他现在是 Mythic 的深度学习科学家。关于这个的更多链接可以在[牛逼链接](https://github.com/wxyyxc1992/Awesome-Links/blob/master/README-en.md)中找到

*   [React 和 React 原生事件系统解释:和谐共存](https://parg.co/UDq):有很多帖子解释如何使用 React 的事件处理系统，但解释其工作原理的却不多。我最近一直在研究 React Native，我在事件处理方面的挣扎提醒了我，准确理解正在发生的事情是多么重要。因此，我决定收集尽可能多的关于 React 中事件处理的信息:以下是我在查看源代码时发现的报告。关于这个的更多链接可以在[牛逼链接](https://github.com/wxyyxc1992/Awesome-Links/blob/master/README-en.md)中找到

## 开源

*   Driver.js 是一个强大而轻量级的普通 JavaScript 引擎，用于驱动用户关注页面。Driver.js 不仅仅是另一个旅游库，旅游只是众多用例中的一个。当你需要对页面进行某种覆盖时，可以使用 Driver.js 一些常见的用例可能是:例如，当用户与一些组件交互时，背景变暗。

*   [brain.js](https://github.com/BrainJS/brain.js) : brain.js 是用 JavaScript 编写的神经网络库。

*   dejavu : dejavu 是 Elasticsearch 缺失的 web 用户界面。现有的 web 用户界面还有很多需要改进的地方，或者是用服务器端页面渲染技术构建的，这使得它的响应性更差，运行起来更笨重。从 v1.0 开始，dejavu 是唯一支持通过 JSON 和 CSV 文件导入数据，以及从 GUI 定义字段映射的 Elasticsearch web UI。