# 星期五前端:版面设计版的未来

> 原文：<https://dev.to/kball/friday-frontend-future-of-layout-design-edition-3gak>

希望你度过了愉快的一周！本周的星期五前沿有一些非常有趣的文章，强调了新的 CSS 规范是如何打开设计的可能性的。

首先，有一个很棒的视频讲述了实现容器查询的挑战，以及我们已经拥有的其他工具如何解决许多相同的问题。然后是[对 CSS 形状实现的未来圆形有机布局的愿景](https://www.smashingmagazine.com/2018/09/css-shapes/)。然后，在其他方面，有一个[迷人的探索](https://matthewstrom.com/writing/designing-with-code.html)，用现代 CSS 设计代码。

当然，在这个过程中，还有 JavaScript 和其他一些有益的东西。

星期五快乐！尽情享受吧！

来自 ZenDev 的 KBall

#### CSS&SCS

##### [解决今日集装箱查询](https://vimeo.com/286945944)

对于设计师和前端开发人员来说，容器查询已经在“我希望我们可以拥有它”的列表上很长时间了。在这个引人入胜的演讲中，来自 Edge 团队的 Greg Witworth 讲述了为什么它们很难实现，然后向我们展示了我们如何解决几乎所有的问题，这些问题是容器查询用其他现代浏览器工具和 API 可以解决的。

##### [重新审视 CSS 形状](https://www.smashingmagazine.com/2018/09/css-shapes/)

这个超级酷！！！不过要确保你使用的是最新最好的火狐版本，因为大多数演示甚至在 Chrome 上也无法运行。我迫不及待地想让这些选择变得更广泛…我们当前的互联网将会变成一个美丽的曲线和多面的体验。

##### [OpenType 特性的完整 CSS 演示](https://sparanoid.com/lab/opentype-features/)

作为一个不太懂排版的人，我不知道字体有这么多不同的特性……更不知道你可以用一个所有浏览器都支持的`font-feature-settings`属性来操纵它们。哇！

##### [21 CSS 动画示例](https://freefrontend.com/css-animation-examples/)

你可以用 CSS 制作不同类型的动画，每一个都有一个显示动画的 gif，一个兼容浏览器的列表，以及一个代码演示的链接。

##### [如何用 CSS 创建便于打印的页面](https://www.sitepoint.com/css-printer-friendly-pages/)

打印网页对我来说总是有点冒险。这最终会看起来像在浏览器中一样吗？我能得到我需要的信息吗？我认为原因是许多 web 开发人员完全不知道或者不关心他们网站的打印样式。一些框架在开箱即用的情况下做“正确的事情”做得很好([我记得在 ZURB Foundation](https://github.com/zurb/foundation-sites/issues/9250) 中有一个关于如何正确做这件事的长时间讨论)，但在很大程度上这取决于开发人员。如果你的网站是你认为人们可能想要打印的东西，你应该让你的用户学会如何去做。

#### JavaScript

##### [为 React 渲染缓存](https://css-tricks.com/render-caching-for-react/)

这是一个非常有趣的演示，演示了如何非常简单地设置 React 页面的本地存储缓存。它是为那些不能完全使用 SSR 的人提供的一种替代方案，但我认为它有一些非常有趣的可能性，可以优化多次使用您的应用程序的任何人的慢速网络连接的性能。

##### [将 React 和 Vue 生态系统与现实世界的 SPA 进行比较](https://vuejsdevelopers.com/2018/09/04/comparing-vue-and-react/)

这是一个有趣的演练，展示了 React 和 Vue 中的并行等效代码。作者公开表示对 Vue 更有经验，但是在两个框架上都花了一些时间，我认为他确实在为每个框架编写相对惯用的代码。如果你已经尝试过其中一种，并且对另一种有什么不同感到好奇，或者你仍然在为你想学的东西下决心，这篇文章将会有所帮助。

##### [JS 举例:JavaScript 数组切片的 8 种不同用法](https://dev.to/kball/js-by-example-8-distinct-uses-of-javascript-array-slice-38m3)

(Bias alert——我写的这个)JavaScript 数组切片方法是 JavaScript 语言中最强大、最常用的内置方法之一。随着 React 和其他面向功能的 JavaScript 实践的兴起，它变得更加重要。本文通过一系列越来越复杂的例子来讲述 slice 的工作原理以及如何使用它。

##### [JavaScript 2018 即将推出:异步生成器，更好的正则表达式](https://thenewstack.io/whats-coming-up-in-javascript-2018-async-generators-better-regex/)

看看每个人最喜欢的语言正在进行的改进。虽然异步生成器很吸引人，但我实际上对正则表达式的改进更感兴趣。我每天都使用正则表达式，JavaScript 的 regex 引擎仍然落后于许多服务器端语言。

##### [Vue.js 范围的样式 vs CSS 模块](https://www.netguru.co/codestories/vue.js-scoped-styles-vs-css-modules)

看看 Vue 如何使在一个文件组件中应用软作用域(组件及其子组件)或硬作用域(纯组件内)的样式变得非常容易。

#### 其他牛气

##### [浏览器多样性的生态影响](https://css-tricks.com/the-ecological-impact-of-browser-diversity/)

关于测试(和使用)多种浏览器的重要性的有趣而有价值的观点。此外，必须爱生物启发的插图。我不确定我以前是否见过将浏览器比作活细胞。:)

##### [前端大师训练营](https://frontendmasters.com/bootcamp/)

这不适合你们大多数人，但如果你在这个列表上是因为你渴望学习前端(而不是目前使用它并试图改善)，或者你知道有人真的想进入这个领域，这可能是一个很好的选择。Frontend Masters 提供了一个为期两周的训练营，由两位优秀且经验丰富的老师指导你学习 HTML、CSS 和 JavaScript，费用很低，只有 10 美元(反正会捐给非营利组织)。如果你知道有人想要学习，把这个链接发给他们！

##### [用代码设计](https://matthewstrom.com/writing/designing-with-code.html)

一个迷人的探索，如何在 HTML 和 CSS 中做设计工作可以导致意想不到的发现和美丽的设计。CSS grid 的新功能使 web 在功能上几乎达到了 Sketch 这样的工具，而浏览器呈现事物的方式的流动性会带来新的发现和更有机的感觉。

##### [那么《巴别塔 7》有什么新内容呢？](https://blog.bitsrc.io/so-whats-new-in-babel-7-ea97cb984ef0)

对巴别塔 7 中新特性的详细分析。我认为我注意到的最大的事情之一是强调为其他工具创建更强大的集成技术。babel 越来越多地由其他工具配置，而不是由最终开发人员配置，核心团队似乎正在拥抱未来。对于个人开发者来说，这应该最终转化为更少的时间去考虑巴别塔和更强大的工具。

* * *

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】