# 周五前端:JSConf。美国版

> 原文：<https://dev.to/kball/friday-frontend-jsconfus-edition-3m1e>

本周的周五前端灵感是 [Johnny-Five](http://johnny-five.io/) 、 [Nodebots](http://nodebots.io/) ，以及令人惊叹的社区活动 [JSConf。美国](https://2018.jsconf.us/)。

JSConf。美国在许多方面是独特的，但其中之一是他们在会议中间包括一个活动日。这一天包括研讨会(我花了一天时间用 Tessel-2 和 Johnny-Five 做硬件黑客)，但其他活动包括无人直升机(无人机！)、Nodeboats，以及高尔夫、冲浪课程和海上皮划艇(进入一个洞穴，不多不少)等替代活动。

总的来说，这是一次令人惊奇的经历，我回来时对 JavaScript 和 JS 社区充满了难以置信的热情和灵感。了解本周周五前端灵感视频中的更多信息:

尽情享受吧！

来自 ZenDev 的 KBall

#### CSS&SCS

##### [带有 CSS 背景混合模式的高级效果](https://dev.to/bnevilleoneill/advanced-effects-with-css-background-blend-modes-3kd8-temp-slug-684746)

非常有趣的演示了使用`background-blend-mode`属性在图像上创建背景的效果。我特别喜欢夜视效果。

##### [从十六进制& RGB 切换到 HSL](https://www.sarasoueidan.com/blog/hex-rgb-to-hsl/)

你知道你的 CSS 不一定要使用 RGB 颜色，事实上在很多情况下使用 HSL 更容易理解吗？原因很简单:HSL 代表“色调、饱和度、亮度”，如果你试图增加对比度或基于用户交互进行一些变暗，这些因素比看似任意的 hexcodes 更直观。

##### [打造“完美”CSS 系统](https://medium.com/gusto-design/creating-the-perfect-css-system-fa38f5bcdd9e)

少讲 CSS 细节，多讲创建一个可伸缩的、健壮的 CSS 架构的高级考虑。如果你想改进公司的 CSS 实践，但是不知道从哪里开始或者考虑什么，我认为这将对你有所帮助。

##### [省时的 CSS 技术创造出反应灵敏的图像](https://medium.freecodecamp.org/time-saving-css-techniques-to-create-responsive-images-ebb1e84f90d5)

对在页面中创建响应性图像的许多不同方法进行了很好的分类，并说明了它们的优缺点以及何时需要使用哪种方法。

##### [在 CSS 中建造战舰](https://css-tricks.com/building-battleship-in-css/)

绝对是在“我靠，你能做到吗？”类别，而不是“这是我想学着做的事情”，但令人惊讶的是，你可以只使用 HTML 和 CSS 来实现交互性。

#### JavaScript

##### [升一级。过滤游戏](https://css-tricks.com/level-up-your-filter-game/)

使用`filter`是函数式 javascript 中非常常见的基本操作之一。我每天都用它。这是一篇关于使用它们的极好的教程，甚至深入到了谓词的函数合成。A+。

##### [React 应用的简单国际化](https://itnext.io/simple-internationalization-of-react-apps-34b3bda95725)

这真的很酷！利用新的上下文 API 和 babel 插件进行预编译，以创建一种超级干净优雅的方式在 React 应用程序中实现国际化。

##### [跨标签与 Web 锁 API 同步](https://www.sitepen.com/blog/2018/08/14/cross-tab-synchronization-with-the-web-locks-api/)

这很有趣，有几个原因。首先，这是一种在同一网站上打开的多个选项卡之间创建一些真正有趣的交互的方式，具有 Chrome 的原生支持和对任何支持 SharedWorkers 的浏览器的 polyfill(基本上只是将 Firefox 和 UC 浏览器添加到组合中)。但是第二，它很有趣，因为它来自于 [Web 孵化器社区组](https://wicg.github.io/admin/charter.html)，我以前没见过它，但它似乎是一种超级轻量级的方式，旨在促进 Web 平台中的创新和实验，没有 W3C 工作组的开销，但与 W3C 有联系，以促进成功实验的采用。

##### [关于 JavaScript 项目你最终会学到的 10 件事](https://blog.usejournal.com/10-things-you-will-eventually-learn-about-javascript-projects-efd7646b958a)

一套优秀的编写优秀 JavaScript(或者非 JavaScript，就此而言)项目的指南。在解决您在构建越来越多的项目时会遇到的许多高层次问题方面做得很好。

##### [从 JavaScript 错误中恢复](https://hugogiraudel.com/2018/08/13/recovering-from-javascript-errors/)

曾几何时，我们谈到了“渐进式增强”，我们从没有任何客户端脚本的应用程序开始，并在可选的 JavaScript 交互性上分层。现在，我们经常颠倒这种方法，从 JavaScript 开始，向后工作。这并不一定意味着我们不能处理 JS 方法——本文强调了一种“优雅退化”方法作为替代。

#### 其他牛气

##### [强尼-五 1.0](http://johnny-five.io/news/v1_0/)

有点超出我的典型前端节拍，但当我在 JSConf。本周我有机会参加一个关于节点机器人和约翰尼五号的研讨会。哇这东西太酷了。创建所有逻辑都用 JavaScript 编写的交互式机器人——[Tessel 2](https://tessel.io/)甚至附带了一个本地节点运行时，但即使是像 Arduino 这样的电路板也没有，Johnny-Five 为您提供了一个超级干净和直观的基于节点的 API 来对它们进行编程。

##### [创造好的类比](https://zellwk.com/blog/creating-good-analogies/)

不针对任何特定的代码片段，而是讨论我们如何谈论代码，我们如何相互教授，或者更好地交流概念和想法。绝对值得通读一遍，尤其是当你努力向人们解释概念的时候。

##### [让你的网络应用更易访问的 7 种方法](https://codeburst.io/seven-ways-to-make-your-web-app-more-accessible-411a8c716fcb)

在最近的一集 JSParty 中，我们谈论了很多关于可访问性的话题，对我来说最重要的一点是我们需要让可访问性不那么令人生畏。这篇文章正是这样做的，通过一系列简单的步骤，一次一步。

##### [【古滕贝格全集】WordPress 编辑](https://www.smashingmagazine.com/2018/08/complete-anatomy-gutenberg-wordpress-editor/)

我之前已经谈过我对 Gutenberg 感到多么兴奋——这将把现代 JavaScript 实践带给 WordPress 开发者的广大受众，并为各地的前端开发者打开一个巨大的新机会。好吧，如果你很兴奋，但还没有花很多时间去挖掘什么是古腾堡或它是如何工作的，这里有一个很好的概述，它是什么，它是如何工作的，以及如何开始。

##### [在 2018 年创造一个 Chrome 扩展:好的，坏的和不好的](https://checklyhq.com/blog/2018/08/creating-a-chrome-extension-in-2018-the-good-the-bad-and-the-meh/)

现代浏览器扩展的一个美妙之处在于，您可以完全使用 web 开发技术和工具来创建超级强大的浏览器扩展。扩展只是简单的 HTML、CSS 和 JavaScript，但是以特定的方式排列并利用特定的 API。这篇文章很好地概述了各个部分是什么，其中一些 API 是什么，以及您可能会在哪里遇到挑战。

* * *

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】