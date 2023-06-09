# 星期五前端:网络是超级灵活的版本

> 原文：<https://dev.to/kball/friday-frontend-the-web-is-super-flexible-edition-f4m>

希望你度过了愉快的一周！本周的文章让我大吃一惊，它强调了我们称之为网络的媒介是多么的不可思议的灵活。

有人用纯 CSS 创建了令人惊叹的插图和动画，一篇文章展示了语义上设置手风琴的 5 种不同的(有效的)方法，以及一些非常令人兴奋的利用 CSS 形状的新工具。此外，还有教程和基础知识的帮助。

星期五快乐！尽情享受吧！

来自 ZenDev 的 KBall

#### CSS&SCS

##### [:空且:空白](https://zellwk.com/blog/empty-and-blank/)

非常好的解释了`:empty`和`:blank`伪选举人是如何工作的，以及一些你可能会想要使用它们的例子。

##### [用火狐形状路径编辑器让你的网页布局从矩形中突出来](https://hacks.mozilla.org/2018/09/make-your-web-layouts-bust-out-of-the-rectangle-with-the-firefox-shape-path-editor/)

我对新的 CSS 形状感到非常兴奋——web 从一开始就是纯矩形的，尽管有矩形的限制，web 设计中所有令人难以置信的多样性还是发生了。突然间，这种情况被打破了，我们看到新的可能性和新的工具出现了，来利用这些可能性。

##### [你的 CSS 特征检测是否足够健壮？](https://keithclark.co.uk/articles/robust-css-feature-detection/)

一个有趣的探索，用`@supports`检测 CSS 特征是多么的脆弱和特殊。由于许多新特性实际上有多种方式来调用它们，仅仅检查浏览器是否支持该特性是不够的，您需要检查您使用的确切语法。

##### [如何创建纯 CSS 插图并制作动画——第 1 部分](https://dev.to/agathacco/how-to-create-pure-css-illustrations-and-animate-them---part-1-1j1k)

在 CSS 中创建插图的逐步指南。让它非常容易理解，并一步一步地分解，加上额外的帖子[第 2 部分](https://dev.to/agathacco/how-to-create-pure-css-illustrations-and-animate-them---part-2-1ao4)和[第 3 部分](https://dev.to/agathacco/how-to-create-pure-css-illustrations-and-animate-them---part-3-3e8a)在动画中分层，然后将这些技术应用到更高级的插图中。

##### [CSS 快速提示:使用大纲](https://pineco.de/css-quick-tip-using-outline/)

对`outline`地产的简短但有启发性的探究。与 border 相似但不同的是，这篇文章解释了你能用它做什么和不能用它做什么。主要用于可访问性/聚焦的目的，但是我也遇到过通过`outline`使用另一个杠杆来控制的设计，使得一些东西可以在 1 elem 而不是 2 elem 中实现。

#### JavaScript

##### [现代 JavaScript 中的提升——let、const、var](https://blog.bitsrc.io/hoisting-in-modern-javascript-let-const-and-var-b290405adfda)

深入探究 JavaScript 中不同类型的变量和类定义的实际情况。如果你不太确定`var`和`let`之间的区别，或者吊装这个词不能马上让你想起什么，请通读一遍。

##### [缓存您的 React 事件监听器以提高性能。](https://medium.com/@Charles_Stover/cache-your-react-event-listeners-to-improve-performance-14f635a62e15)

这是一篇非常有趣的文章，研究了匿名函数作为 React 组件中的事件侦听器对性能的影响。我认为将事件处理程序重构为 react 组件上的命名函数或方法有很多可读性方面的原因，但这也突出了性能方面的原因。

##### [经验教训:用 Webpack 和 React 进行代码拆分](https://hackernoon.com/lessons-learned-code-splitting-with-webpack-and-react-f012a989113)

React 中代码分离组件的可靠介绍和演练。虽然不像 [Vue 的异步组件](https://vuejs.org/v2/guide/components-dynamic-async.html#Async-Components)那么简单，但事实证明它仍然非常简单。

##### [用交叉点观察器 API 建立目录](https://tj.ie/building-a-table-of-contents-with-the-intersection-observer-api/)

我见过的许多关于交叉点观察器的例子都与图像的延迟加载有关，看到它的不同使用方式令人耳目一新。这突出了如何构建一个交互式目录，显示您在页面中的位置。

##### [如何在 Vue 中发出数据:超越 Vue.js 文档](https://dev.to/progresstelerik/how-to-emit-data-in-vue-beyond-the-vuejs-documentation-jbd-temp-slug-7476413)

对事件发射在 Vue 中如何工作的全面概述，展示了几个例子，然后提出了一些反模式。如果您已经非常熟悉事件发射器模式和在组件树中上下传递数据，那么您可能不会学到太多新东西，但是如果您是新手或者不确定如何使用它，这是一本很好的读物。

#### 其他牛气

##### [你怎么给手风琴配乐？](https://www.sarasoueidan.com/blog/accordion-markup/)

关于在网络上创建手风琴的各种方式的语义的有趣讨论。着眼于辅助技术、样式和添加与 JavaScript 的交互性的含义。

##### [手动可达性测试的重要性](https://www.smashingmagazine.com/2018/09/importance-manual-accessibility-testing/)

这既是一个支持手动测试的令人信服的论据，也是一个关于如何开始进行手动易访问性测试的非凡资源，你应该用它来测试易访问性/屏幕阅读器工具的类型。

##### [DevTools(Chrome 70)的新功能](https://developers.google.com/web/updates/2018/08/devtools)

我通常不包括这些浏览器的小发行说明，但偶尔会有一些超级酷的东西吸引我的眼球。在这种情况下，它是 devtools 中的 Live Expression 特性。设置任意 JavaScript，每 250 毫秒更新一次，并显示值。我认为这将对各种类型的调试有很大的帮助，否则你可能会到处使用 console.log。

##### [帮助初学者了解如何在线开通网站](https://css-tricks.com/helping-a-beginner-understand-getting-a-website-live/)

随着我们在科技行业走得更远，我们很容易忘记我们一路走来需要吸收多少概念和术语。这是一个出色的一步一步的解释，说明了进入一个在线网站的所有部分，CMS 是如何工作的，以及如何建立一个本地开发环境。

##### [UX 动画正确使用终极指南](https://uxdesign.cc/the-ultimate-guide-to-proper-use-of-animation-in-ux-10bd98614fa9)

哇哦。我可以看这些动画一整天——这里有如此多的知识，对于每个比较或演示，都有对原理的解释，然后是它的可视化显示。非常非常酷。

* * *

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】