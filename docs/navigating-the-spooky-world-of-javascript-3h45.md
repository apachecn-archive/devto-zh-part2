# 在怪异的 JavaScript 世界中导航

> 原文：<https://dev.to/aspittel/navigating-the-spooky-world-of-javascript-3h45>

JavaScript(以及一般的前端开发)可能会让人感到害怕，因为太多很酷的事情发生得太快了。语言本身在进化，围绕它的生态系统也在进化。从 React、Vue、Angular 甚至 Hyperapp 这样的框架以及它们快速变化的 API，到围绕它们的捆绑系统，再到语言本身，JavaScript 都在快速发展，很难跟上。这些工具在五年前几乎没有。像[这篇](https://hackernoon.com/how-it-feels-to-learn-javascript-in-2016-d3a717dd577f)这样的博客文章概述了学习现代 JavaScript 并及时了解最新动态有多难。

在 2015 年混乱的高峰期，我开始使用 JavaScript，此前我只从事数据分析脚本和计算机科学课程，从那以后，我有幸教了数百人这门语言。因此，我将讲述一大堆关于我的 JavaScript 旅程的故事——以及我如何决定教什么——希望你会比我犯更少的错误，我们可以让 JavaScript 的世界不那么可怕😈！

## 如何面对我们对 JavaScript 的恐惧？

### 关注基本面

这门语言的基础永远不会发生巨大的变化，所以对它的来龙去脉了如指掌只会对你有所帮助。

特别是在 JavaScript 中，了解一些中间概念，如调用栈、上下文、范围、原型链、高阶函数、异步编程和事件循环是非常重要的，将有助于您进一步使用该语言。

我也很欣赏这个评论:

[![maxwell_dev profile image](img/87963d1020278f08c6b6b01a37674b6f.png) ](/maxwell_dev) [ Max Antonucci ](/maxwell_dev) [ ![twitter](img/82aa32a73d0a2eadd783a8a531ea2cc3.png) ](https://twitter.com/Maxwell_Dev) [![github](img/029e4450541d5c4819d89a83d5315060.png)](https://github.com/maxx1128) [<time datetime="2018-10-30T15:31:40Z">Oct 30 '18</time>](/maxwell_dev/comment/6enk)

我同意让更多的 JS 基础知识派上用场。我看到 JS 框架具有乘数效应——你事先知道的 JS 越多，JS 框架的好处就越多。

最近的一个例子是我在 Ember 框架上的工作。该框架给出了管理组件状态及其“控制器”中的相关数据的良好模式但是基本的 JS 知识帮助我更好地看到这些控制器的局限性。一位同事使用导入/导出、JS 类和面向对象编程的组合来更好地抽象和管理一些复杂的数据，从而更多地分割代码并使其可重用。标准 JS 被用来更好地利用这个框架。

非常了解基础知识会让框架更有帮助，也更容易学习！实际上，这些框架和库只是 JavaScript 它们并不神奇！

事实上，你的项目可能永远不需要使用框架——GitHub 运行在普通的 JavaScript 上，而 Hacker News 运行在未混合的 JS 总数的 150 行上。再加上你的站点权重会更轻！

我很欣赏丹·阿布拉莫夫的[你可能不需要 Redux](https://medium.com/@dan_abramov/you-might-not-need-redux-be46360cf367)——我认为更多的库和框架应该让他们的联合创始人明确表示他们的工具并不总是适合这项工作的工具！这是事实。

### 发亮物体综合症

闪亮物体综合征是指当你看到一个新的事物时，立刻想放下一切，专注于那个闪亮的物体。在技术领域，这可能意味着每当一个新的库出现时，你都要重写你的应用程序。

[![](img/eb99b50ce7afa77676b0bd285c312e1d.png)T2】](https://i.giphy.com/media/CK0gcoAFZZ3G/giphy.gif)

我对此深感内疚——我以前每周都会写关于用新的库或框架编写应用程序的博文。我还在工作中使用过 JQuery、Angular.js、Angular、Vue、Elm 和 React。但是，这不应该是正常的，也不应该被鼓励。

我认为我最好的发光物体综合症故事是当我在一个团队工作时，他们使用的是一个中文库，它的文档还没有完全翻译成英文。我记得我运行谷歌翻译试图解决这个问题。如果有人想知道的话，谷歌翻译对技术文档来说不是很好。虽然这个库看起来很酷，但是如果使用一个我们说的语言的库会好得多。

那么我们来谈谈如何克服“发光物体综合症”

[![](img/8357fe816901c37c69e7aae98a5f17e6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XaPiujT6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pbs.twimg.com/media/Ce5nYp0W4AAxp5Z.jpg)

#### 你不需要什么都知道

很大程度上是由于冒名顶替综合症，许多开发人员觉得有必要了解所有事情——但这甚至是不可能的！要知道的事情太多了，没有人知道所有的事情！不断提醒自己这一点，不要觉得有必要知道所有的事情——即使这很难。

引用丹·阿布拉莫夫关于新 React Hooks API 的话:

> 钩子是一个实验性的反应建议。你现在不需要了解他们。

[(src)](https://medium.com/@dan_abramov/making-sense-of-react-hooks-fdbde8803889)

所有事情都是如此——即使感觉上可能不是这样。

#### 重深度轻广度

一旦你了解了一个框架或库，那么转移到下一个就容易多了。如果你理解了组件架构，你已经学习了 React、Angular 和 Vue 的四分之一了！你不需要知道他们所有人。当然，它们都很酷，有不同的功能，但你可能只需要一个。

#### 不要马上迁移到最新的东西上

此外，当你为你的项目选择一个堆栈时，不要成为第一个采用者。当然，这可能很酷，你可能会因此写一篇博文，而且可能会让你的一些代码变得更容易。但是——然后你被库中的某个 bug 卡住了，在它被修复之前，你不能前进。或者你正在尝试做的事情的文档是不存在的，除了专家级的开发人员之外，让任何人加入你的团队是不现实的。

让别人先找到漏洞；然后你就可以开始使用这个很酷的新工具了！

此外，保持进步和为应用程序创建现代界面也很重要，但如果您的堆栈仍然为您工作，就没有必要从头开始更改或重写它。React *刚刚*在谷歌搜索中超过了 JQuery[(src)](https://www.youtube.com/watch?v=kz3nVya45uQ)，尽管 JQuery 在 JavaScript 世界中可能感觉像是古老的历史！

## 明智地选择

因为你不能学习或使用每一个新出现的框架或库，你还必须弄清楚如何明智地选择你的技术。当你决定在一个项目中使用什么工具或学习什么工具时，有几个关键问题:

*   用的好吗？如果它背后有一个社区，那么它更有可能有贡献者和频繁的功能和 bug 解决迭代。而且，更有可能有持久力。
*   项目背后的社区是谁？它是一家大公司吗？他们有行为准则吗？他们是如何对待新用户的？
*   是不是经常更新？你不想被没有被修复的错误所困扰。
*   是否有频繁的突破性变化？您不希望必须进行主要的代码更新来保持工具最新。此外，这可能会使研究工具变得困难。看着你又瘦又瘦。
*   什么特性使这个工具比其他工具更好？它有什么额外的功能吗？或者解决一些其他工具不能解决的问题？
*   适合你的团队吗？例如，如果你的团队大部分是初级开发人员，那么这个框架有好的学习资源和文档吗？如果你的程序员大多是函数式的，那么这个工具适合这个范例吗？
*   文档好吗？这对于能够轻松使用该技术非常重要。
*   代码看起来怎么样？代码库看起来写得好、可维护吗？
*   图书馆好吗？

这里没有唯一正确的答案——如果这项技术对你的团队有效，那么不管一些思想领袖在 Twitter 上说什么，它都是正确的解决方案。

## 如何在 JavaScript 世界中保持最新

好的——既然我们已经讨论了发光物体综合症——你怎样才能以一种合理的方式保持最新的更新呢？

*   在 Twitter 上关注 JavaScript 人——比如 [@dan_abramov](https://twitter.com/dan_abramov) 、 [@addyosmani](https://twitter.com/addyosmani) 、 [@getify](https://twitter.com/getify) 、 [@wesbos](https://twitter.com/wesbos) 和[@ Sarah _ edo](https://twitter.com/sarah_edo)——这通常是了解新技术的最快方式。
*   新闻来源——我关注 [dev.to](https://dev.to) 、 [CSS 招数](https://css-tricks.com/)、 [Smashing Mag](https://www.smashingmagazine.com/) 、 [JavaScript Weekly](https://javascriptweekly.com/) 作为我的主要资源！

## 其实没那么毛骨悚然！

在结束这篇文章之前，快速检查一下现实，JavaScript 实际上并没有以光速前进。React、Vue 和 Angular 已经存在了四年多，ES6 也是如此，这可能是 JavaScript 有史以来最大的一次变化。实际上并不是每周都有新的流行框架。即使有，你也不需要学习这个框架。无论如何，技术的优秀部分通常最终会被整合到它们的竞争对手中——看看 React 是如何从 Angular.js 继承而来，然后 Angular 又从 React 继承而来 Vue 从两者中吸取了经验！

此外，我认为 JavaScript 和前端开发总体上正在发展，这很好——为了使开发更容易和用户体验更好，它需要发展。

希望这篇文章能让 JavaScript 的世界少一点怪异，多一点可及！

## 额外收获:我现在对 JavaScript 非常感兴趣

*   [web 组件](https://dev.to/aspittel/building-web-components-with-vanilla-javascript--jho)
*   [Hyperapp](https://dev.to/aspittel/functional-programming-in-javascript-done-right-with-hyperapp-570f)
*   [渐进式网络应用](https://dev.to/ben/what-the-heck-is-a-progressive-web-app-seriously-923)
*   [CSS 胡迪尼](https://css-tricks.com/interactive-introduction-to-css-houdini/)

## 保持联系

*   [推特](https://twitter.com/aspittel)
*   [简讯](https://mailchi.mp/b4216331e284/zen-of-programming)
*   [GitHub](https://github.com/aspittel)

为了将来的读者，这是为万圣节演讲而写的，因此有了这个怪异的主题！