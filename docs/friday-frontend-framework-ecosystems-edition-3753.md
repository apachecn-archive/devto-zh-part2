# 星期五前端:框架生态系统版

> 原文：<https://dev.to/kball/friday-frontend-framework-ecosystems-edition-3753>

本周，我对 JavaScript 框架创新的最新浪潮感到非常兴奋——围绕框架的生态系统！

作为 React、Vue 和 Angular mature 的核心框架，我们看到围绕这些框架的工具和生态系统出现了越来越多的创新。

这让我想起了大约 5-10 年前服务器端框架生态系统的成熟，这对前端生态系统来说是个好兆头。在本周的周五前端灵感视频中了解更多信息:

[https://www.youtube.com/embed/ujT_co3lF14](https://www.youtube.com/embed/ujT_co3lF14)

尽情享受吧！

来自 ZenDev 的 KBall

附:我下周都在旅行，所以没有时事通讯。下一期简讯将于 8 月 17 日周五发布。到时候再聊！

#### CSS&SCS

##### [移动视口单元的诀窍](https://css-tricks.com/the-trick-to-viewport-units-on-mobile/)

处理来自移动浏览器处理视口单元方式的怪异的有趣方法，特别是。

##### [5 个热门 CSS 新特性及其使用方法](https://www.creativebloq.com/how-to/5-hot-new-css-features-and-how-to-use-them)

这里有一些非常有趣的新东西，通过一步一步的创建 UI 来教授。我对`:focus-within`特别感兴趣。让您解决一些非常有趣且常见的问题，这些问题过去需要使用纯 CSS 的 JavaScript 来解决。

##### [flexbox 的奇特魔力和自动余量](https://css-tricks.com/the-peculiar-magic-of-flexbox-and-auto-margins/)

嗯，显然`margin: auto;`在 Flexbox 中的工作方式不同。我做了很多 flexbox，但不知何故从来没有真正遇到过这种情况…我想是因为在 flexbox 中，我只是使用`justify-content`和`align-items`或`align-self`来处理它。也就是说，工具箱里多一件工具也无妨。

##### [使用 CSS 形状和 CSS 排除的挫折](https://benfrain.com/the-frustrations-of-using-css-shapes-and-css-exclusions/)

这篇文章的框架是负面的——嘿，使用这个新特性很有挑战性——但是我认为这实际上是新特性强大的一个很好的演示。除了缺乏 Microsoft Edge 支持，我认为作者提出的所有其他问题都是可以克服的(事实上，评论直接回答了其中一些问题)，这表明我们不必再在 CSS 中使用盒子。😁😁😁

##### [仅 CSS 双击](https://codepen.io/MartijnCuppens/pen/GZWgaQ?editors=1100)

这真是古怪。利用额外的(隐藏的)元素、焦点和 CSS 转换来实现“双击”功能，而无需任何 JavaScript。你应该这样做吗？不要！但是挖掘并弄清楚它是如何工作的会扩展你的思维和对 CSS 的理解吗？绝对的！

#### JavaScript

##### [打字稿 3.0——来了什么？](https://itnext.io/typescript-3-0-what-has-come-19f81d1fac60)

TypeScript 的最新主要版本已经发布，这篇文章对一些主要的新特性进行了分析。为`unknown`增加了一个新的类型/语言关键字，这是一个很大的变化，可能是迫使它成为一个主要版本的突破性变化，但其中也有很多其他好东西。

##### [理解 JavaScript 对象](https://blog.halolabs.io/understanding-javascript-objects-d31cd24ca60f)

一个很好的 JavaScript 对象的基本分类，涵盖了它们的一些底层机制(原型)，可用于处理它们的函数，以及它们行为的一些特点和细节。

##### [我在 React 和 Vue 中创建了一模一样的应用。以下是不同之处。](https://medium.com/javascript-in-plain-english/i-created-the-exact-same-app-in-react-and-vue-here-are-the-differences-e9a1ae8077fd)

非常有趣的 React 和 Vue 教学方法。既强调了思维上的差异，也向我展示了这些框架在许多方面的基本相同性。如果你对其中一个很熟悉，对另一个很好奇的话，这将会很有用。

##### [反应 vs. Vue (vs .角度)](https://medium.com/fundbox-engineering/react-vs-vue-vs-angular-163f1ae7be56)

类似的比较氛围，但方法不同。从几个方面比较 React 和 Vue 学习曲线、代码风格、性能、工具、社区等等。根据通读，我认为作者的观点有点偏向 Vue，但他们很好地展示了事实。

##### [介绍 Fusion.js:一个基于插件的通用 Web 框架](https://eng.uber.com/fusionjs/)

非常有趣——一个或多或少可以与 [Next.js](https://nextjs.org/) 或 [Nuxt.js](https://nuxtjs.org/) 相媲美的框架，提供通用 JavaScript 支持(服务器端渲染+客户端水合为 SPA)，内置代码分割，以及非常强大的中间件/插件方法。绝对值得一试！

#### 其他牛气

##### [用 Vue.js 构建 Wordpress Gutenberg 区块](https://dev.to/kball/building-wordpress-gutenberg-blocks-with-vuejs-4m7g)

(偏见警告:我写了这个)虽然 React 是 Gutenberg 支持的主要框架，但有迹象表明，使用 Vue.js 等其他 JavaScript 框架实现 Gutenberg 块应该可以使用 vuera 等库，所以我决定探索如何实现这一点以及它的工作情况。

##### [为什么新 V8 这么该死的快](https://nodesource.com/blog/why-the-new-v8-is-so-damn-fast)

探究新的 JavaScript 编译器架构，该架构自[版本 58](https://v8project.blogspot.com/2017/05/launching-ignition-and-turbofan.html)(2017 年年中)以来使 Chrome 更快，自[版本 8.3](https://medium.com/the-node-js-collection/node-js-8-3-0-is-now-available-shipping-with-the-ignition-turbofan-execution-pipeline-aa5875ad3367) 以来使 Node 更快。如果你像我一样对使我们的工具工作的内部结构进行研究，你会喜欢这个，甚至对于那些只专注于应用程序编程的人，我打赌你会学到一些新东西。就像性能对象参数顺序一样- { x，y，z}不同于{ y，x，z }...谁知道呢？！？

##### [2018 年 JavaScript 的成本](https://medium.com/@addyosmani/the-cost-of-javascript-in-2018-7d8950fbb5d4)

这是一个关于发布大量 JavaScript 代码的性能影响的很好的分析，以及一些关于如何减少发布量的建议，以及减轻您继续发布的代码的性能影响。

##### [2018 年 7 个 Javascript EEG 读心术库](https://blog.bitsrc.io/7-javascript-eeg-mind-reading-libraries-for-2018-9a8e28544cd7)

在“OMG JavaScript 无处不在”的阵营中，这需要通过一组开源库，这些库可用于使用 JavaScript 与 EEG 设备进行交互。我可以想象将这与一些基于 tensorflow 的面部[和](https://itnext.io/face-api-js-javascript-api-for-face-recognition-in-the-browser-with-tensorflow-js-bcc2a6c4cf07)[姿势](https://medium.com/tensorflow/real-time-human-pose-estimation-in-the-browser-with-tensorflow-js-7dd0bc881cd5)识别结合起来，构建不仅可以读取你的身体，还可以读取你的思想的应用程序。我们生活在未来！

##### [教你的客户如何使用你为他们建立的网站](http://mediatemple.net/blog/tips/teaching-clients-use-website-built/)

对于我们这些为客户构建网站和应用程序的人来说，我们知道移交是一个巨大的挑战。你如何将你已经建立的关于如何使用这个东西的所有知识封装成一组客户可以理解的信息？这是一个非常有用的不同人使用的技术的概要，虽然其中一些对你来说似乎是显而易见的，但我打赌你至少发现了一个你还没有做过的。

* * *

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】