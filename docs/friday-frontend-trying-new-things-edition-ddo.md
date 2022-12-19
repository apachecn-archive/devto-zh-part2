# 星期五前端:尝试新事物版

> 原文：<https://dev.to/kball/friday-frontend-trying-new-things-edition-ddo>

令人惊奇的是，在一个新的地方，走出你的舒适区会让你筋疲力尽，但同时也会激发你去学习更多。

这个星期的大部分时间我都在美丽的加拿大温哥华参加 Node + JS Interactive。与来自 JavaScript 世界各地的人们交谈非常愉快，也看到了一个我从未去过的美丽城市。我喜欢它把我从日常事务中摆脱出来，并促使我思考一些新事物。

本周，我要挑战你去挖掘一些你平时不会挖掘的东西。如果你主要是在 CSS 领域，深入研究一些 JS 帖子。如果你主要是 JS 开发人员，试试 CSS 帖子。如果你做所有的前端工作，也许可以在时事通讯的结尾阅读一下《WASM 邮报》。

不管怎样，祝你周末愉快！

最佳，

来自 ZenDev 的 KBall

P.S .即将推出一些学习 Vue.js 的新资源！请关注下周的公告！

#### CSS&SCS

##### [我们谈论 CSS 的方式](https://www.rachelandrew.co.uk/archives/2018/10/04/the-way-we-talk-about-css/)

这篇文章很好地概括了我一直在努力表达的东西:CSS 作为一种语言是独一无二的，因为它服务于一个独特的环境。但是它是被编纂的，被彻底设计的，并且(大部分)是一致的。如果不一致，通常是因为遗留原因(我们不能破坏 web)，这是 JavaScript 和 HTML 共有的问题。但这是不同的，我们需要停止为这些差异道歉，而是专注于解释它们并理解为什么。

##### [CSS 仅浮动标签带有:占位符-显示伪类](https://callmenick.com/dev/floated-labels-with-placeholder-shown/)

这是一个非常好的技术，当输入为空时使用占位符，但当输入中有内容时切换到标签，这样用户可以维护上下文。文章没有显示它，但你甚至可以添加动画，使它看起来像占位符滑入标签位置。非常非常酷的东西！

##### [萨斯选择器:要窝还是不要窝？](http://bradfrost.com/blog/post/sass-selectors-to-nest-or-not-to-nest/)

一个有趣的例子强调了利用 Sass 的`&`特性创建相关类集的利弊。这个结论与我在软件中一次又一次看到的观点相吻合…我们喜欢为了简洁而优化，但是为了长期的可维护性，显式几乎每次都是赢家。

##### [Flexbox 的用例](https://www.smashingmagazine.com/2018/10/flexbox-use-cases/)

打破 Flexbox 的思维模式，当你想使用它的时候，这是一个很好的选择，与 CSS Grid 或者更传统的布局机制相反。我喜欢把 flexbox 描述为“黏糊糊的”——在许多方面，它是最“网络本地”的布局机制，可以根据你的内容和观看设备无缝地调整布局。

##### [我怎么记得 CSS 网格属性](https://zellwk.com/blog/remember-css-grid-properties/)

让 CSS Grid 有时学习和采用起来有点令人生畏的一点是，有多少种不同的方式来做您可能想做的事情。这篇文章将网格的许多不同属性分成 4 个可理解的部分。

#### JavaScript

##### [使用事件总线在 Vue 组件之间共享道具](https://css-tricks.com/using-event-bus-to-share-props-between-vue-components/)

对于简单的组件关系，支持事件是处理事情的正确方式。对于复杂的有状态 ui，像 VueX 这样的状态管理器要好得多。但是在中间有一些情况下，事件总线是一个有用的工具，事实证明在 Vue 中实现它非常简单。

##### [在 JavaScript 中用绑定操作符驯服它](https://www.smashingmagazine.com/2018/10/taming-this-javascript-bind-operator/)

很有意思！一个 0 阶段的提议(也叫暂时不要使用它),继续迭代 JavaScript 的语法，添加一个绑定操作符来处理更多的绑定情况，在这些情况下`this`很难使用。

##### [这些你跳过的反应基本面可能会要了你的命](https://medium.freecodecamp.org/these-react-fundamentals-you-skip-may-be-killing-you-7629fb87dd4a)

如果您正在使用 React，但它仍然感觉像魔术一样，并且您不确定各个部分是如何组合在一起的，或者它实际上在做什么，那么这是一篇开始获得更多感觉的好文章。当然没有涵盖所有内容，但确实清楚地说明了组件呈现的工作原理以及它如何与实际的 DOM 交互。

##### [您好，创建 React App 2.0！](https://dev.to/progresstelerik/hello-create-react-app-20-316-temp-slug-3014493)

我是固执己见的支架的忠实粉丝，把它作为启动应用程序的地方，在 React 世界里,“创建 React 应用程序”是黄金标准。他们刚刚发布了他们的 2.0 版本，有一些非常好的升级！我特别喜欢内置的 SCSS/CSS 模块集成，但是这篇博文详细介绍了许多其他好东西。

##### [深入了解新的 Vue Devtools v5.0](https://blog.usejournal.com/new-features-in-vue-devtools-v5-0-and-why-they-are-useful-3ce732fdc7a0)

目前前端创新最有趣的领域之一不是核心 JavaScript 框架，而是围绕它们的元框架和工具。这篇文章介绍了 Vue Devtools 第 5 版的新特性，包括一些更好理解路由的工具，一些看起来超级棒的新性能工具，等等！

#### 其他牛气

##### [JavaScript 和 WebAssembly 之间的调用终于快了🎉](https://hacks.mozilla.org/2018/10/calls-between-javascript-and-webassembly-are-finally-fast-%F0%9F%8E%89/)

我喜欢这个。这个声明不仅非常棒——现在可以快速地在 JavaScript 和 WASM 之间来回调用了——而且这篇文章非常直观地解释了所有这些东西是如何工作的。

##### [写好文字的替代品](https://medium.com/@amyalexandraleak/should-you-use-alt-text-or-a-caption-48311e259ded)

我最近看到一个统计数据，网络上只有不到 50%的图片定义了替代文本，这使得屏幕阅读器和其他支持设备的用户对这些图片完全视而不见。但是，即使是那些有标签的文字，有多少是真正有用的呢？这篇文章解释了当你围绕一幅图像写替代文本和其他内容时，你应该考虑什么是最有帮助的。

##### [Windows 10 2018 年 10 月更新中微软 Edge 的新功能](https://blogs.windows.com/msedgedev/2018/10/04/edgehtml-18-october-2018-update/)

微软 Edge 即将推出一个新版本，里面有很多给网络开发者的好东西。我对 Web 身份验证 API 感到特别兴奋，但是还有更好的服务人员支持等等。看看吧！

##### [节点+ JS 基础](https://blog.npmjs.org/post/178734613655/node-js-foundation)

这个合并是我上周写的东西，也是 Node + JS Interactive 这里的会议的话题。这是生态系统中的一个大腕，npm 的创始人和 Node.js 的前“BDFL”的另一个观点

* * *

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】