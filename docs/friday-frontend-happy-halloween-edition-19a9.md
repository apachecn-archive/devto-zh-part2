# 周五前端:万圣节快乐版

> 原文：<https://dev.to/kball/friday-frontend-happy-halloween-edition-19a9>

如果你有孩子，你可能非常清楚万圣节就要到了。(我五岁的孩子一直在问“是下周吗？”这个月的每个星期)。这是一段有趣的时光，从很多方面来说，也是假期的开始，先是感恩节，然后是圣诞节。我很期待，希望你也有一些计划！

本周的内容有很大的组合。我们可能对 JavaScript 部分的 React 文章有点太多了，但是最近这个行业也是如此。:)还有一些很棒的 CSS 文章(和一个非常令人兴奋的纯 HTML+CSS 游戏)，以及一个关于扩展 web 字体可用性的超级令人兴奋的公告。尽情享受吧！

最佳，

来自 ZenDev 的 KBall

#### CSS&SCS

##### [用 CSS 属性选择器拼接 HTML 的 DNA](https://www.smashingmagazine.com/2018/10/attribute-selectors-splicing-html-dna-css/)

这是一篇很棒的文章，讲述了如何使用 CSS 来基于 HTML 属性中的值进行定位。基于链接属性、标题等的样式。非常酷！

##### [用选择器使用特征检测、条件和组](https://css-tricks.com/using-feature-detection-conditionals-and-groups-with-selectors/)

CSS 的一个伟大之处就是它的弹性。较新的功能经常可以被使用而不用担心或任何显式的功能检测，因为旧的浏览器会忽略它们。然而，有时您想要合法地检查支持。本文将介绍一些实现这一点的方法，既有使用`@supports` at-rule 的方法，也有一些选择器技巧。

##### [CSS 布局食谱](https://developer.mozilla.org/en-US/docs/Web/CSS/Layout_cookbook)

我非常喜欢烹饪书，因为它是分享模式和可重用代码片段的一种方式。这个很棒，因为它完全是纯 CSS，不依赖任何框架或任何东西。需要实现一个卡片，一个粘滞的页脚，还是一些面包屑？看看这些例子。这也是为 MDN 做贡献的绝佳起点。

##### [可维护 CSS: Order 键](https://pyx.space/post/keys-to-maintainable-css-order)

我是 CSS 财产订购战的平民幸存者；在关于 CSS 属性的正确排序方式的讨论中，我实际上没有站在任何一方，事实上，我发现这对我个人来说没有太大的影响。也就是说，我知道许多设计师和开发人员恪守他们的排序规则，他们中的大多数更喜欢遵循本文中建议的方法。

##### [矿:没有 JS，只有 CSS 的冒险游戏](https://codecampanion.blogspot.com/2018/10/the-mine-no-js-css-only-adventure-game.html)

这真是令人震惊。一个完整的互动游戏，只用 HTML 和 CSS 构建。好吧，这可能不是真的——作者用 HAML 和 SCSS 来生成 HTML & CSS。😉但是，最终的结果仍然是没有 JS 的纯 HTML & CSS，这是一个完全怪异的冒险游戏。非常非常酷。这篇文章解释了它背后的思想，但是如果你想直接跳到游戏[中去](https://codepen.io/ashishkhuraishy/full/rqQxyX)吧。

#### JavaScript

##### [React 16.6:React . memo()用于功能组件渲染控制](https://scotch.io/tutorials/react-166-reactmemo-for-functional-components-rendering-control)

简短但有趣，突出了 React 16.6 中的一个新特性，允许您在重新渲染父组件时减少重新渲染子组件的数量。

##### [如何在 React 应用中应用固体原理](https://blog.usejournal.com/how-to-apply-solid-principles-in-react-applications-6c964091a982)

这是一篇关于如何思考组件架构和分解组件的好文章。它的所有例子都使用了 React，但我认为这里的原则实际上可以应用于任何面向组件的架构，不管你使用的是哪种框架。

##### [angular mix 2018 顶级外卖](https://auth0.com/blog/top-takeaways-from-angularmix-2018/)

这是一个有趣的角度看世界的走向。根据我在生态系统中听到的情况，crunchbase 的使用数据对我来说似乎有点偏离，但我不能调查他们的数据，因为来源是在付费墙后面。:(不管怎样，这显示了框架中正在发生的一些有趣的事情，以及即将到来的事情。

##### [理解反应过来的渲染道具和道具](https://blog.bitsrc.io/understanding-react-render-props-and-hoc-b37a9576e196)

React 中两个非常常见和强大的技术的伟大解释者。我特别欣赏的是，他们不仅谈到了好处，还提出了一些关于如何不使用这些模式的警告。

##### [每个 JavaScript 开发者都应该知道的 33 个概念。](https://github.com/leonardomso/33-js-concepts)

这是一个关于一系列 JavaScript 主题的很好的资源索引，从非常基础的语言元素(`==`和`===`的区别)到高级概念，如 JavaScript 设计模式、局部应用和 currying。

#### 其他牛气

##### [WebAssembly 的后 MVP 未来:卡通技能树](https://hacks.mozilla.org/2018/10/webassemblys-post-mvp-future/)

这是一个超级有趣的关于 WebAssembly 的各种改进计划的阅读，林·克拉克惊人的卡通技巧也应用于使其可视化。

##### [Typekit 是土坯字体](https://theblog.adobe.com/typekit-is-adobe-fonts/)

这是一个巨大的公告。Typekit 是最大的(也是最容易使用的)网络字体库之一，但它长期以来一直需要独立订阅。不久前收购了 Typekit 的 Adobe 公司刚刚宣布，他们将向任何一名 Creative Cloud 成员开放 Typekit 的所有字体。适用于桌面和网络。鉴于大多数设计师至少为一些创意云付费，这将使网络字体更加流行。

##### [第三方脚本的硬成本](https://daverupert.com/2018/10/hard-costs-of-third-party-scripts/)

我认识的大多数开发人员在涉及到包含第三方脚本时都属于两个阵营中的一个:“嗯，这没什么大不了的”，或者“我讨厌他们，但营销坚持”。如果你属于第一个阵营，这可能有助于改变你的想法，如果你属于后者，这可以给你一些具体的东西来寻找，衡量，并强调你的情况。

##### [你用错了<em>](https://logrocket.com/blog/youre-using-em-wrong)

好吧，这可能有点学究气，但我认为，随着我们进入越来越多的网络内容将由语音设备阅读的世界，弄清楚语义是值得的。虽然文本中的`<i>`和`<em>`可能看起来相同，但语义却大不相同，这可能会导致语音设备的不同处理方式。

* * *

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】