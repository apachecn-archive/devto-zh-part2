# 星期五前端:CSS 动画点播版

> 原文：<https://dev.to/kball/friday-frontend-css-animations-on-demand-edition-5bmg>

抱歉，本周没有视频——在客户工作、睡眠问题和打电话请病假的儿童保育之间，我没有时间。也就是说，我对本周的一个链接感到非常兴奋。Animista 是一个用于探索和创建基于 CSS 的动画的 GUI。您四处摸索，查看动画，调整参数，然后导出 CSS 动画代码，准备集成到您的项目中。多酷啊！？！？

星期五快乐！尽情享受吧！

来自 ZenDev 的 KBall

#### CSS&SCS

##### [特异性在:not()，:has()，and :matches()](https://meyerweb.com/eric/thoughts/2018/06/05/specificity-in-not-has-and-matches/)

这绝对让我犯过错误。伪类的特殊性遵循一些简单的规则，但是除非你知道这些规则，否则你很容易期望一件事情，却遇到完全不同的事情。这是一篇简短而甜蜜的文章，会让你明白。

##### [提升你的 CSS 选择器技能](https://dev.to/bnevilleoneill/level-up-your-css-selector-skills-5ha1)

全面介绍 CSS 选择器选项，包括关系选择器、各种类型的通配符选择器(开头为、结尾为等)以及高级伪选择器。绝对值得通读和一些实验。

##### 

这不属于“好吧，这教我如何做某事”的范畴，而更属于“天哪，这怎么可能？”类别。安全研究人员使用 css 混合模式和时间计算来重建 iframes 中的内容，并绕过安全问题。听起来它已经被修复了，但是我从来没有想过要用 CSS 来修复它。:P

##### [Animista: CSS 动画点播](http://animista.net/)

这是一个非常酷的交互式图形用户界面，它可以让你玩和调整动画，在你使用它们的时候看到它们，然后点击获得 CSS 代码，然后你可以将它应用到你的项目中。太棒了。

##### [用 CSS 过渡和动画改变渐变的状态](https://css-tricks.com/the-state-of-changing-gradients-with-css-transitions-and-animations/)

我见过的第一个利用 [CSS Houdini](https://www.smashingmagazine.com/2016/03/houdini-maybe-the-most-exciting-development-in-css-youve-never-heard-of/) 来实现即将到来的 CSS 特性的多填充的例子。渐变的 CSS 动画只在 IE 和 Edge 中受支持(想象一下！)，但 Houdini 允许你将它们添加到基于 webkit 的浏览器中，如 Chrome 和 Safari。有什么问题吗？在这些浏览器中，Houdini 仍然在一个功能标志后面，默认情况下没有打开，在 Firefox 中也完全不支持。但是很快，很快...

#### JavaScript

##### [与 React 共事一年后，我学到的最重要的经验](https://medium.freecodecamp.org/mindset-lessons-from-a-year-with-react-1de862421981)

很好的大图概述——少一些你将要阅读来学习如何在 React 中做某事的内容，多一些你将要阅读来学习如何学习 React 的内容。:)重点是宏观原则，以及一个额外的概念学习列表。

##### [提取一个 React JS 组件并发布到 NPM](https://codeburst.io/extracting-a-react-js-component-and-publishing-it-on-npm-2a49096757f5)

简单但有用的演练如何提取一个项目的组件到它自己的包中，并在 NPM 上发布它。以 React 为例，但同样的过程也适用于 Vue、Angular 或任何真正的组件化框架。

##### [好事多磨](https://robots.thoughtbot.com/good-things-come-to-those-who-await)

一个简单、易读的新 async/await 语法演示。我认为这篇文章在向我展示 async/await 本质上是承诺之上的语法糖方面做得比我读过的关于这种语法的任何其他文章都好。如果你觉得自己理解了承诺，但还没有真正投入到 async/await 中，那么这篇文章正适合你！

##### [2018 年你应该知道的 11 个 Javascript 实用程序库](https://blog.bitsrc.io/11-javascript-utility-libraries-you-should-know-in-2018-3646fb31ade)

我已经在使用其中的一些了(嗨，你好，lodash & moment)，但是天哪，经历这些让我对一些新的工具感到兴奋！比如拉姆达？内置不变性的 JavaScript 功能&函数 currying？我会尽快找个借口把它带进我的项目里！

##### [2018 年学习 Vue.js 最佳资源](https://vuejsfeed.com/blog/best-resources-to-learn-vue-js-in-2018)

关于学习 Vue.js 的学习资源的很好的综述。Vue 已经成为我的 JavaScript 密集型项目的首选框架，我强烈推荐给那些还没有深入研究框架的人。它拥有 React 和 Angular 的所有功能，但更容易学习，尤其是对于那些来自更多设计或 HTML/CSS 背景的人来说。

#### 其他牛气

##### [微软+ GitHub =赋能开发者](https://blogs.microsoft.com/blog/2018/06/04/microsoft-github-empowering-developers/)

本周爆出的重大科技新闻是微软正在收购 github。这立即引起了一些反弹，竞争对手 GitLab 利用这一点，在 twitter 上发起了#movingtogitlab 标签，并在声明发布后看到[大量增加的进口](https://twitter.com/gitlab/status/1004143715844124673)。也就是说，我对微软在过去几年中对开源和开发者体验(hello VS Code)的奉献精神印象深刻，他们肯定没有像 LinkedIn 那样破坏最近的收购，所以我不认为有任何值得担忧的主要原因。

##### [打造响应式形象](https://medium.com/9elements/building-a-responsive-image-e4c6229fa1f6)

这是我在过去玩过的东西，但从来没有在一篇关于之前响应图像的文章中强调过，这些图像实际上根据放置它们的容器的大小改变了哪些特征是可见的！因为 SVG 很棒，可以让你在里面写媒体查询！(附言:想要一本将 SVG 作为代码操作的入门书吗？在 Skillshare 上查看我的课程！)

##### [ML in JS...良好的...什么事？](https://changelog.com/jsparty/28)

JSParty 播客的最新一集(为那些不喜欢听的人提供完整的抄本)。偏见警告:我是这一集的小组成员，但无论如何，我认为这是一次令人敬畏的谈话，你可能会很喜欢。我们讨论了 JavaScript 中的机器学习，你可能想用它来做什么特定的应用(与一般的 ML 不同)，但也误入了一系列其他主题，包括艺术的定义(如果一个 ML 模型制作了它，它是艺术吗？)和软件开发的伦理。

##### [复杂的邪教](https://alistapart.com/article/cult-of-the-complex)

这篇文章有点“滚出我的草坪”的感觉，但我认为其中也有一些重要的想法。人们太容易被最新最棒的东西吸引，而忘记了最好的解决方案往往是最简单的。与这个概念相关的是，如果你还没有读过蒂姆·伯纳斯·李的[设计原则](https://www.w3.org/DesignIssues/Principles.html)，这是网页基本设计的基本指导原则，你一定要看一看。特别值得一读的是最小功率原理背后的论证。

* * *

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这样，请务必注册，以便每周五将这些简讯直接发送到你的收件箱中！在这里报名:[https://zendev.com/friday-frontend.html](https://zendev.com/friday-frontend.html)