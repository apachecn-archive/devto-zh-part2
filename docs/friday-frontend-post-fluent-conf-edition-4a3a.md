# 周五前端:发布流畅-会议版

> 原文：<https://dev.to/kball/friday-frontend-post-fluent-conf-edition-4a3a>

本周后半周，我在圣何塞参加了奥莱利的流畅会议，采访了博客 [Changelog](https://changelog.com/podcast) 和 [JSParty](https://changelog.com/jsparty) 播客的发言人。正如我去参加会议时经常发生的那样，我离开时既疲惫又精力充沛。

我们的行业现在有这么多很酷的事情发生，我们真的处于一个不可思议的进步时期。

**这种进步不仅仅是技术上的** -这是我去年参加的第二个大型会议，它有一个完整的轨道专门讨论人类和多样性问题，并把伦理问题放在主题演讲的前面和中心。作为技术人员，我们有不可思议的力量来影响世界，伴随这种力量而来的是以一种授权他人的方式做事的责任，而不是操纵和剥削他人。我很高兴看到这在我们的行业中变得越来越重要。

无论如何，我们在本周的时事通讯中已经有了一堆很棒的文章和资源。我最喜欢的是我在会议上了解到的一个资源:[http://houdini.glitch.me/](http://houdini.glitch.me/)。这是一个展示 CSS 胡迪尼力量的网站。仍然只有在你使用 Chrome Canary 并启用了一些实验标志的情况下才真正可用，但是哇，这东西真的很酷，很令人兴奋。

星期五快乐！尽情享受吧！

来自 ZenDev 的 KBall

#### CSS&SCS

##### [你从未听说过的新(旧)CSS 单位](https://dev.to/maxart2501/the-new-and-old-css-units-youve-never-heard-about-1mn1)

好吧，我觉得`vh`和`vw`很酷，但是`lh`、`lrh`、`vi`和`vb`怎么样？当然，还没有浏览器支持，但是 CSS 的未来是光明的！或者即使有支持的东西，我也不知道`turn`是一个 CSS 单元，或者你可以在媒体查询中使用纵横比。超级棒的东西！

##### [CSS 媒体查询:快速参考&指南](https://alligator.io/css/media-queries/)

没有什么令人兴奋的，但一个伟大的快速参考媒体查询，以及通过一些新的和即将到来的媒体查询定义的媒体查询 4 级规范。

##### [使用 CSS 变量的实用技巧](https://css-irl.info/practical-tips-css-variables/)

与 CSS Grid 类似，CSS 自定义属性已经存在了足够长的时间，我们开始看到一些真正的最佳实践正在出现。明确欣赏关于范围的观点；我期待看到这种方法在组件框架和 UI 工具包中越来越多地使用。

##### [用 CSS 网格创建条形图](https://css-tricks.com/creating-a-bar-graph-with-css-grid/)

当然，对于超级复杂的图形和图表，JavaScript 工具可以让你创建定制的 SVG，但我喜欢用纯 CSS 创建简单的图表。当我在做基础构建块的时候，我为一个[‘dashboard’工具包](https://foundation.zurb.com/building-blocks/kits/dashboard.html)做了很多这样的事情，但是那是在 CSS grid 得到很好的支持之前。这篇文章很好地使用了 grid 和 CSS 自定义属性来制作一个超级酷的条形图，并带您了解整个思路。

##### [弹性的、陈述性的、情境性的](https://keithjgrant.com/posts/2018/06/resilient-declarative-contextual/)

对于许多开发人员来说，让 CSS 变得困难的一个原因是，它解决的问题在概念上与大多数逻辑编程语言不同。对我来说，最大的区别之一是它的视觉本质——视觉问题与逻辑问题非常不同，因此你解决它们的方法也不同。这位作者强调了不同的观点，但在传达一些使 CSS 与众不同的独特“思维模式”方面做得很好。

#### JavaScript

##### [表象和容器组件](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0)

这是 Dan abra mov(《Redux & Create React App》的合著者)关于 React 组件架构和他认为有用的模式的一篇优秀博文。即使您没有使用 React，也值得一读，因为它突出了一种分解前端应用程序的好方法。

##### [什么时候(为什么)应该使用 ES6 箭头功能，什么时候不应该使用](https://medium.freecodecamp.org/when-and-why-you-should-use-es6-arrow-functions-and-when-you-shouldnt-3d851d7f0b26)

对 arrow 函数所有不同语法特点的分析，接着是对何时使用和不使用它们的简短讨论。即使您非常熟悉箭头函数，也值得快速回顾一下——您可能会学到一些东西(例如，我没有意识到您可以通过用括号将单行箭头函数括起来，从而在单行箭头函数中返回对象文字)。

##### [用 Vue 替换 jQuery](https://www.sitepoint.com/replacing-jquery-vue/)

与 React 和 Angular 等框架相比，Vue 最吸引我的一点是它是轻量级的，无需构建步骤即可使用，并且易于增量使用，无需完全投入到一个完整的单页面应用程序中。这意味着我们可以在传统上使用 jQuery 的地方开始使用它，只是为了增加页面的交互性。然后，如果您最终想要扩展和做一些比 jQuery 更容易做到的事情，那么您已经在一个框架中了，这个框架为您提供了灵活性和功能。

##### [2018 年 11 个 Javascript 动画库](https://blog.bitsrc.io/11-javascript-animation-libraries-for-2018-9d7ac93a2c59)

从 Velocity，一个与 jQuery animate API 兼容的高性能库(还记得吗？如此简单，但如此缓慢和滑稽！)到使用 three.js 的 3d 动画，到使用 Greensock 的全猪细粒度动画控制。所有的都在那里——甚至是一个纯 CSS 动画库——所以你可以根据你的需要选择你的复杂程度和功能。太棒了。

##### [建筑内角项目](https://medium.com/@cyrilletuzi/architecture-in-angular-projects-242606567e40)

我不倾向于包括太多的角度链接，因为三大框架(Angular、React 和 Vue)是我了解最少的一个，但是这个太好了，不能错过。Angular 的一个缺点是学习曲线和生态系统的复杂性，但这篇文章做了大量工作，简单地展示了一个高级架构，并理解如何使用 NgModules 来组织 Angular 应用程序。

#### 其他牛气

##### [Houdini . glitch . me](http://houdini.glitch.me/)

这是一个展示 CSS 胡迪尼力量的网站。仍然只有在你使用 Chrome Canary 并启用了一些实验标志的情况下才真正可用，但是哇，这东西真的很酷，很令人兴奋。一定要去看看。

##### [纱线导入现在使用 package-lock.json](https://yarnpkg.com/blog/2018/06/04/yarn-import-package-lock/)

将此文件归入“重要事项”类别——当将项目移植到 yarn 时，您现在可以从 package-lock.json 导入，而不仅仅是 package.json。

##### [15+专家分享 2018 年网络性能建议](https://www.keycdn.com/blog/web-performance-advice-2018/)

Web 性能是一个迷人的、多方面的话题。我最近没有想到但在 Fluent conf 的一次对话中提到的一个领域是我们复杂的 JavaScript 框架的字面 CPU 使用率。我们习惯于从大小和网络速度的角度来考虑网络性能，但是对于不使用高端计算机的用户来说，纯粹的 CPU 使用会严重影响用户界面。无论如何，这篇文章有很多不同的观点和建议，从超级简单到极其复杂。从中挑选你喜欢的。

##### [图像不一致:浏览器如何以及何时下载图像](https://csswizardry.com/2018/06/image-inconsistencies-how-and-when-browsers-download-images/)

通过浏览器处理图像下载的方式的变化，一个迷人的外观。对于那些构建图像密集型网站的人来说，这是非常值得一读的，以确保你不会在不期望的时候意外下载大量的图像并淹没移动用户。

##### [你的大脑在前端开发](https://css-tricks.com/your-brain-on-front-end-development/)

精彩的几乎是意识流风格的演示，讲述了克里斯·科伊尔在看设计时脑子里在想什么。对于那些仍在学习如何最好地将设计转化为实现的人来说非常有帮助，我打赌即使像我这样的怪人也会学到一些东西。我做到了。

* * *

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这样，请务必注册，以便每周五将这些简讯直接发送到你的收件箱中！在这里报名:[https://zendev.com/friday-frontend.html](https://zendev.com/friday-frontend.html)