# 星期五前端:客户端性能版

> 原文：<https://dev.to/kball/friday-frontend-client-side-performance-edition-426o>

本周周五的灵感来自于对 JavaScript 性能优化兴趣的复兴。

在 Addy Osmoni 发表了关于“2018 年 JavaScript 的成本”的精彩文章后，人们对如何提高我们发送的 JavaScript 的性能以及减少我们发送的 JavaScript 的数量以缩短首次互动的时间产生了浓厚的兴趣。

这是一个周期性的问题——过去我们非常担心 JS 和 CSS 的权重，然后人们开始去 SPAs，专注于传输更少的标记和更多的数据，现在我们已经兜了一圈，特别是看看全球的移动连接，再次关心我们传输了多少 JavaScript。

这一次，我们并不担心 3 亿人的原始互联网连接，而是为全球下一个 10 亿人带来高性能的网站和应用程序。激动人心！在本周的周五前端灵感视频中了解更多信息:

[https://www.youtube.com/embed/Qm7jyOHvpNc](https://www.youtube.com/embed/Qm7jyOHvpNc)

尽情享受吧！

来自 ZenDev 的 KBall

#### CSS&SCS

##### [创建 Flexbox Flex 容器时会发生什么？](https://www.smashingmagazine.com/2018/08/flexbox-display-flex-container/)

对您声明 flex 元素时所发生的一切进行了出色、深入的介绍。即使你认为自己非常擅长 flexbox，你也几乎肯定会学到一些东西。对我来说，这是“外部展示模型”和“内部展示模型”概念的形式化。

##### [实用 CSS 卷轴抢购](https://css-tricks.com/practical-css-scroll-snapping/)

CSS 滚动捕捉得到了越来越多的支持，并为 CSS 打开了大量的可能性，这在过去需要复杂而有缺陷的 JavaScript 实现。本文介绍了许多潜在的用途，并强调了一些需要注意的事项。

##### [带有 CSS 变量和胡迪尼魔法的简单交互饼状图](https://css-tricks.com/simple-interactive-pie-chart-with-css-variables-and-houdini-magic/)

好吧，所以结果很简单，但是到那的过程严重的让我意乱情迷。一个交互式的动画饼状图(对于不支持`conic-gradient`(几乎所有内容)的浏览器有一个条形图后备)，全部用 CSS、HTML 和一点点 Houdini JS 来制作动画。哇哦。

##### [CSS 中的元素隐藏技巧](https://pineco.de/element-hiding-techniques-in-css/)

对使用 CSS 隐藏东西的各种方法进行了很好的分类，以及它们对可访问性的影响。我喜欢作者将每个方法归结为 3 个关键部分的方式——一个元素是否仍然有框布局，它是否可以被屏幕阅读器访问，以及它是否是可操作的。每种隐藏方法都有不同的答案。

##### [CSS 网格布局:乘&征服——或者说如何实际设计使用网格。](https://medium.freecodecamp.org/css-grid-layout-multiply-conquer-or-how-to-actually-design-using-the-grid-745dfc51e114)

从设计优先的角度对 CSS 网格进行了有趣的介绍。着眼于什么类型的布局对不同类型的网格有意义，以及如何用 CSS grid 来处理这些。也简单地提到了响应。不错！

#### JavaScript

##### [各个击破！为你的温泉懒人加载。](https://medium.com/wolox-driving-innovation/divide-and-conquer-lazy-loading-for-your-spa-32eb63149e76)

超级快速简单——如何使用 webpack 的动态加载，非常简单地为基于 React 或 Vue 的 spa 进行代码拆分。

##### [通过代码拆分减少 JavaScript 负载](https://developers.google.com/web/fundamentals/performance/optimizing-javascript/code-splitting/)

关于在应用程序中进行代码拆分的各种方法的更深入的文章。涵盖共享供应商捆绑包、多入口点、基于组件的拆分，甚至服务人员的缓存。

##### [十五个棱角分明的表演技巧&招数](https://angular-guru.com/blog/angular-performance-tips)

一套工具，为您的性能工具箱，当谈到角度应用。补充了上面的文章，展示了如何在 Angular 中进行代码拆分，以及模板预编译和各种 Angular 特定的技术。

##### [Vue CLI 3.0 来了！](https://medium.com/the-vue-point/vue-cli-3-0-is-here-c42bebe28fbb)

对 Vue CLI 进行了巨大的重写，其中一个重大更新是配置，而无需退出配置(即，您可以为您的应用程序进行自定义配置，而无需放弃从 CLI 自动获取配置更新的能力)。还有一些非常酷的构建组件(自动将 Vue SFCs 构建到 web 组件中？酷！)和一个漂亮的新 GUI。

##### [深度 VuePress 教程:Vue-Powered Docs &博客](https://dev.to/couellet/deep-dive-into-vuepress-craft-a-clean-documentation--blog-4058)

当 VuePress 第一次出现时，它有很多宣传，但没有太多好的教程，而且如何将它用于博客(最常见的静态网站类型 IMO)而不是技术文档(最初的用例)一点也不明显。本教程很好地解释了 VuePress 是什么，如何使用它，以及如何建立一个博客。

#### 其他牛气

##### [2018 年学习网页开发](https://dev.to/kball/learning-web-development-in-2018-2knc)

(偏见警报——我写的这个)2018 年学习 web 开发势不可挡。要学的东西实在太多了。JavaScript 生态系统的发展速度比其他任何系统都要快。有。所以。该死的。很多。成为一名网络开发人员的指南看起来就像飞行中的面条怪物。所以我写了一篇关于我如何决定学什么的帖子。

##### [保护网站使其不易被访问](https://meyerweb.com/eric/thoughts/2018/08/07/securing-sites-made-them-less-accessible/)

一个有趣的亮点是，对于发展中国家和其他互联网边缘地区的人们来说，开车去 HTTPS 是如何让网络变慢的。还指出了我们可以提供帮助的一些方式(基本上，拥抱服务人员)。很值得一读。

##### [这一行 Javascript 让 FT.com 慢了 10 倍](https://medium.com/ft-product-technology/this-one-line-of-javascript-made-ft-com-10-times-slower-5afb02bfd93f)

伟大的性能发现突出了做“正确的事情”的频率也可能是减慢一切的罪魁祸首。挑战在于你永远不知道哪件事是“正确的”——过早地优化会浪费很多时间——所以这种方法是关键:注意问题，深入了解问题所在，然后有选择地优化。

##### [浏览器绘画及网页性能考虑](https://css-tricks.com/browser-painting-and-considerations-for-web-performance/)

性能难题的另一部分——实际上浏览器如何将像素放到屏幕上，改变这些像素的各种方法对性能的影响是什么，一些调查技术，以及`transform`属性的魔力。

##### [WebAssembly:如何和为什么](https://dev.to/bnevilleoneill/webassembly-how-and-why-43bm-temp-slug-1809822)

对 webassembly 的深入介绍，涵盖了它是什么、它看起来像什么、用例是什么，并为您提供了一些快速入门工具。

* * *

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】