# 星期五前端:几乎准时版

> 原文：<https://dev.to/kball/friday-frontend-almost-guten-time-edition-j9>

你好，来自充满火灾的加州，我们在那里已经呼吸了一周的烟雾，如果你们有一些要送的话，我们真的需要一些额外的雨水。:)

Wordpress 5.0 公开发布日期(将包括新的基于 React 的 Gutenberg 编辑器)定于下周一。这意味着世界上 30%的网站所使用的平台将会跃入现代 JavaScript。(我的意思是真的，wordpress 的当前版本使用了类似 jquery 1.12 的东西，下一个版本正在向前跳跃以做出反应……)。预计会有混乱，预计会有反弹(下面重点介绍了其中的一些)，但也预计前端开发人员会有大量的机会。

同样在本周的时事通讯中，一些超级酷的 CSS 文章(我喜欢深入 CSS 和网络性能)一组 JavaScript 基础知识，以及一些关于新的/最近的 React 特性的内容。尽情享受吧！

最佳，

来自 ZenDev 的 KBall

#### CSS&SCS

##### [CSS 中的“C”:级联](https://css-tricks.com/the-c-in-css-the-cascade/)

如果你仍然觉得你在努力理解为什么某些 CSS 属性没有像你期望的那样显示出来，那么你很有可能还在纠结这个问题。不丢人，我花了很长时间才真正掌握。这篇文章做了很好的工作，一步一步地分解它，并且在结尾有一个很棒的视觉指南给那些视觉学得更好的人。

##### [CSS 和网络性能](https://csswizardry.com/2018/11/css-and-network-performance/)

深入探讨了如何放置 CSS、它如何与脚本加载交互等对性能的影响。引人入胜，充满了可行的建议。

##### [五年后的国情咨文](https://evilmartians.com/chronicles/five-years-of-postcss-state-of-the-union)

将 PostCSS 描述为构建 CSS 工具的框架是贴切的；PostCSS 是 CSS 的 Babel(碰巧在 Babel 出现之前就存在了)，这是用它构建的各种工具的一个很好的展示。它已经从 autoprefixer 走了很长的路(尽管这仍然是一个很好的用例！)

##### [带自动调整和最小最大值的网格平铺布局](https://gedd.ski/post/tile-layouts/)

超级酷的探索，如何容易地创建一个多种多样的反应瓷砖布局使用 CSS 网格自动适应和最小最大。另一个例子说明了通过媒体查询的响应已经变成了“旧的”做事方式——新的工具更加强大。

##### [编辑布局、浮动和 CSS 网格](https://v6.robweychert.com/blog/2018/11/css-grid-editorial-layouts/)

有了 CSS Grid 固有的所有功能，以及展示它使事情变得更好的文章和教程，这是对它没有很好处理的一个用例的有趣探索:在坚持网格对齐的同时在图像周围浮动文本。作者很好地解释了这种情况，老式的解决方案，并介绍了如何用 grid 处理它。还有一个对未来 CSS 特性的额外参考，这将使解决方案变得更好。

#### JavaScript

##### [成为 JavaScript 高手要学的 10 件事](https://dev.to/treyhuffine/10-things-to-learn-on-the-way-to-become-a-javascript-master-2m17-temp-slug-3031594)

这是对 JavaScript 概念和技术的一个很好的总结。不会教你其中的任何一个，但是会给你一个高层次的概念，这个概念应该足够 1。决定你是否已经熟悉。谷歌上找一些资源学习一下吧。

##### [了解异步 JavaScript —事件循环](https://blog.bitsrc.io/understanding-asynchronous-javascript-the-event-loop-74cd408419ff)

对 JavaScript 中异步如何工作的精彩解释。事件循环是我们考虑如何在 Node 中开发的核心部分，但也是基于浏览器的方法的关键部分。如果您曾经想知道为什么这么多框架都有某种“nextTick”函数，或者看到 setTimeout 的值为 0 或 1 的代码，并且想知道它到底在做什么，您可能会从阅读本文中受益。

##### [完整的 JavaScript 手册](https://medium.freecodecamp.org/the-complete-javascript-handbook-f26b2c71719c)

学习 JavaScript 的大量百科资源。声称遵循 80/20 规则，在 20%的时间内覆盖 80%的 JavaScript 我不知道如何评价，但它在一篇文章中覆盖了大量令人印象深刻的语言。

##### [用获取 API](https://medium.com/@Charles_Stover/react-suspense-with-the-fetch-api-a1b7369b0469) 对悬念做出反应

这既是对 React 悬念如何工作的有趣探究，也是如何包装现有 API 以很好地使用它的示例。

##### [反应过来的钩子简介](https://www.fullstackreact.com/articles/an-introduction-to-hooks-in-react/)

一步一步地开始使用新的实验性 Hooks API。涵盖了从动机到编写定制钩子的整个故事，并探讨了如何为钩子编写测试。

#### 其他牛气

##### [WordPress 5.0 的新功能(如何准备古腾堡)](https://kinsta.com/blog/wordpress-5-0/)

考虑到所有的担忧，事实上大量的人仍然讨厌它，并且可访问性的故事很糟糕，看起来 Gutenberg 要出货了。目前的预定发货日期是本月晚些时候，如果他们错过了截止日期(可能)到一月份，还有一个后备计划。这对任何使用 WordPress 的人来说都是一件大事，也可能为前端开发人员打开一堆可能性。看看这个。

##### [古滕、伯格和丑陋的](https://blog.samuellevy.com/post/56-the-guten-the-berg-and-the-ugly.html)

哇哦。这篇文章的评论会让你感受到 Wordpress 世界中关于 Gutenberg 的讨论的味道。两极分化很严重。这篇文章很好，因为它提供了对古腾堡相对中立的评价，没有回避其缺点，但也承认许多好的方面，并对未来的选择保持直言不讳的务实态度。

##### [用 Vue 和 SVG 构建圆环图](https://css-tricks.com/building-a-donut-chart-with-vue-and-svg/)

自从我强调 JS-framework-SVG 操作的文章以来，已经有一段时间了，我以前做过的大多数文章都展示了与 SVG 结合使用 React 是多么强大。事实证明，Vue 同样强大；这很好地展示了使用 Vue 创建交互式 SVG 是多么简单。

##### [介绍 GraphQL 基金会](https://medium.com/@leeb/introducing-the-graphql-foundation-3235d8186d6d)

这对于那些希望长期在 GraphQL 之上构建的人来说是有希望的——一个独立的基金会正在 Linux 基金会下建立，为项目的持续管理提供一个中立的家。很高兴看到脸书将它的一些项目公开给真正的社区所有……尽管我想知道为什么 GraphQL 可以完全独立，而 React 却不能。

##### [网。开发](https://web.dev/)

在最近的 Chrome 开发者峰会上，谷歌推出了一个新页面，将灯塔式的网站审计与教育资源结合起来，帮助你从 4 个方面提高网站的性能:速度、可访问性、最佳实践和 SEO。

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】