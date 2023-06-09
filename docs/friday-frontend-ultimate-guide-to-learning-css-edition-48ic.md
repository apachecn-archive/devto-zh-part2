# 星期五前端:CSS 版学习终极指南

> 原文：<https://dev.to/kball/friday-frontend-ultimate-guide-to-learning-css-edition-48ic>

我花了 3-4 周的时间收集了大量学习 CSS 的资源，这让我对 CSS 作为一种语言重新感到兴奋。CSS 是一种极其丰富和强大的语言，它让我们能够详细访问世界上最强大的渲染引擎。多神奇啊！？！？

[https://www.youtube.com/embed/L_LTgsOmk4c](https://www.youtube.com/embed/L_LTgsOmk4c)

尽情享受吧！

来自 ZenDev 的 KBall

#### CSS&SCS

##### [学习 CSS 的终极指南](https://dev.to/kball/the-ultimate-guide-to-learning-css-9le-temp-slug-359113)

CSS 是欺骗性的——它看起来很简单，但是里面有太多的东西[(超过 400 个独特的属性](https://dev.to/sadick254/did-you-know-css-has-413-unique-properties-2i24)！)，而且很难搞清楚接下来要学什么。这就是为什么我花了大量的时间来整理这个指南，将 100 多种资源进行了分类和排序，以便于学习。

##### [模式库第一:一种管理 CSS 的方法](https://www.smashingmagazine.com/2018/07/pattern-library-first-css/)

关于使用模式库作为 CSS 底层构建块的精彩介绍。我碰巧知道 ZURB 基金会开源 CSS 框架是从 ZURB 的类似方法中发展出来的。

##### [来自 CodePen 的 CSS 单选按钮样式](https://freebiesupply.com/blog/css-radio-buttons/)

人们拥有风格单选按钮的不同方式的美丽集合。超级光滑的动画，切换，等等。既可以作为可能性的灵感，也可以作为学习指南，因为每个例子都在一个代码笔中，所以你可以确切地看到它是如何完成的。

##### [彻底改变网页布局的 CSS 技巧](https://www.creativebloq.com/features/css-tricks-to-revolutionise-your-layouts)

还有点灵感方面，探索一些现代的 CSS 技巧，如过滤器，剪辑等，以真正使您的网站与众不同。

##### [CSS 绘制 API](https://css-tricks.com/the-css-paint-api/)

现在 Chrome 中的 Paint API(CSS Houdini 的一部分)已经获得了官方支持，我们开始看到越来越多关于如何使用它的文章。这太棒了！希望我们能看到足够的兴趣，让其他浏览器加快步伐，让它工作起来，因为这是当今 CSS 中最令人兴奋的发展之一(即使与网格和自定义属性等其他伟大的特性相比，Houdini 也是最棒的)。

#### JavaScript

##### [《Vue 手册:Vue.js 全面介绍》](https://medium.freecodecamp.org/the-vue-handbook-a-thorough-introduction-to-vue-js-1e86835d8446)

这是我有点希望我写的那些帖子之一。:)一个很棒的 Vue.js 逐步指南，带你从“这是什么？”全面应用。Looooong 帖子，但也有可下载的 PDF 或 mobi，对于那些希望第一次进入 Vue 的人来说，这绝对是一个很好的循序渐进的方法。

##### [2018 年网页组件](https://www.sitepen.com/blog/2018/07/06/web-components-in-2018/)

面向组件的开发比以往任何时候都更受欢迎，但是每个 JavaScript 框架都有自己的组件风格…共同点在哪里？答案是 Web 组件标准。虽然没有太多的框架直接针对 web 组件( [polymer](https://www.polymer-project.org/) 是一个明显的例外)，但我越来越多地看到“编译成 Web 组件”工具作为一种方式，使特定于框架的组件可以在其他环境中共享。这是一篇很好的文章，可以让您了解 web 组件的最新状态。

##### [理解 JavaScript 闭包和作用域链的底层过程](https://scotch.io/tutorials/understanding-the-underlying-processes-of-javascripts-closures-and-scope-chain)

关于 JavaScript 中作用域的很好的教程，以及对闭包的简单介绍。我希望他们在闭包上用更多的例子更深入一点，因为它是一个如此关键和有价值的特性，但是这在作用域上已经足够好了，我认为仅仅为了这一块就值得通读一遍。要更深入地探究闭包，请查看 FreeCodeCamp 上的[这篇文章。](https://medium.freecodecamp.org/lets-learn-javascript-closures-66feb44f6a44)

##### [React 只是 JavaScript](https://medium.com/yld-engineering-blog/react-is-just-javascript-88600553269c)

我的一部分讨厌“React 只是 JavaScript”这句流传的咒语(这是许多类似文章中的一篇)，因为它们往往忽略了工具的价值和现实。当然，React 只是 JavaScript——Vue 也是，Angular.js 也是……而 Angular 和 Dojo“只是类型脚本”。这种框架既忽略了构建系统和 JSX 等工具的真正复杂性，也忽略了人们采用该工具的价值。尽管如此，这篇文章确实很好地揭示了 React 及其工作原理，我认为这是一件非常好的事情。

##### [稀疏阵列中的奇遇](https://remysharp.com/2018/06/26/an-adventure-in-sparse-arrays)

JavaScript 数组有一个很好的特性——它们可以很稀疏，这意味着你可以创建一个很长的数组，但是在你把值放入数组的每个索引之前，它不会占用所有的空间。长度可能是 100，但在你把数据放进去之前，它不会占用 100 个内存槽。也就是说，因为你知道，JavaScript，稀疏数组有一些古怪的行为。这篇文章很好地介绍了他们的行为以及你如何与他们互动。

#### 其他牛气

##### [谷歌新的速度更新在渐进规模上起作用；小的改进很重要](https://www.seroundtable.com/google-speed-update-scale-25980.html)

当谈到搜索结果时，谷歌越来越重视网站速度，本月将有新的更新！显然不同于以前的二进制“慢/快”排名因素，新的更新因素在增量改进。我知道我以此为动力去做一些增量优化！

##### [性能审计:一款 Firefox 开发者工具深度潜水](https://www.sitepoint.com/performance-auditing-a-firefox-developer-tools-deep-dive/)

提高性能的第一个地方是一些审计工具，尽管 Chrome devtools(和 Lighthouse)在 mindshare 仍有巨大优势，但 Firefox Developer Tools 已经取得了很大进展。这是开始了解他们的好地方，主要关注性能。

##### [你可能从未听说过的 15 种 HTML 元素方法](https://hackernoon.com/15-html-element-methods-youve-potentially-never-heard-of-fc6863e41b2a)

这是对一组 DOM api 方法的一次很好的演练。当使用为你做所有 DOM 操作的框架时，这些通常是模糊的，当有一个简单的网站并且你想写一些超级轻量级的原生 javascript 来增加一点交互性时，这些可能是关键。

##### [为什么 GraphQL:优点、缺点&替代品](https://www.robinwieruch.de/why-graphql-advantages-disadvantages-alternatives/)

graphql 的所有原因，它的优点和缺点。好东西。从根本上说，GraphQL 是关于前端如何加载和管理数据的，随着我们将越来越多的复杂性转移到前端，我们需要在该领域有更多的选择。要了解 GraphQL 的另一个优势，请查看 JSParty 第一集的第一部分，我采访了来自 Github 的 Brian Douglas 关于 GraphQL 的内容。

##### [Vim 移植到 WASM](https://rhysd.github.io/vim.wasm/)

好吧，我不知道对于那些不像我一样沉迷于 VIM 的人来说这是不是很酷，但是看看这个！他们将 vim 移植到了 WebAssembly，所以现在你可以在浏览器中使用 VIM 了……与大多数 VIM“端口”或按键绑定不同，它支持一切，包括宏和各种各样的优点。这比在我的终端上运行要慢一点，但是哇…想象一下把它插入一个基于网络的文件系统，比如 google docs 或 dropbox，可能性是令人兴奋的。

* * *

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】