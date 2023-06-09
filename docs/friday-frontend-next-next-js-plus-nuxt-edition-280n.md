# 周五前端:Next Next (js) plus Nuxt 版

> 原文：<https://dev.to/kball/friday-frontend-next-next-js-plus-nuxt-edition-280n>

希望你这一周过得愉快！本周有很多有趣的东西，但特别想强调一下关于 Next.js 和 Nuxt.js 的文章，这两个框架分别位于 React 和 Vue 之上。

这两个框架都体现了一组前端架构决策(Nuxt.js 文章明确探讨了这些决策)，并且是一个增长趋势的一部分:围绕顶级 javascript 框架创建专门的生态系统和“黄金路径”。这些“更高级”的框架减少了构建“同类最佳”应用程序的障碍，并且使用起来非常有趣。

星期五快乐！尽情享受吧！

来自 ZenDev 的 KBall

#### CSS&SCS

##### [简介没用. css](https://css-tricks.com/introducing-trashy-css/)

这是一个非常漂亮的主题化 CSS 库，用于突出 HTML 文档的语义结构。也是一个 bookmarklet，你可以用它来禁用当前的 CSS 并把它放在适当的位置。我认为这对于调试/突出可访问性问题非常有用，也是一个有用的学习工具。

##### [使用 SVG 进行媒体查询](https://www.sitepoint.com/using-svg-with-media-queries/)

在 SVG 中使用媒体查询可以让您做一些令人惊奇的事情——创建有响应的图像。图像不仅可以随着您的缩放完美地调整大小(SVG 是矢量图像)，而且实际上可以根据它们的大小改变哪些特征是可见的。我喜欢！

##### [刷新 CSS 书签 v2](http://lea.verou.me/2018/09/refresh-css-bookmarklet-v2/)

超级简单的 bookmarklet，用于刷新当前页面的样式表。Ie 用于不在自动刷新环境中工作时的开发。我用基于 webpack 的自动刷新系统做了大部分开发工作，但当我在我的站点(使用 Jekyll)上工作时，我不这样做，这是一个比做整个页面刷新好得多的流程。

##### [如何在 CSS 中使用 2D 变换函数](https://www.sitepoint.com/how-to-use-2d-transformation-functions-in-css/)

CSS 中所有不同的转换函数的概述。平移，旋转，倾斜，你能想到的都有。甚至加入了一点矩阵变换的乐趣。

##### [Flexbox:那个柔性盒子有多大？](https://www.smashingmagazine.com/2018/09/flexbox-sizing-flexible-box/)

Rachel Andrew 和 Smashing Magazine 在解释各种 CSS 功能的基本“如何”方面做了非常出色的工作。本文深入探讨了 flexbox 背后的尺寸算法，解释了浏览器如何确定 flexbox 元素的尺寸。

#### JavaScript

##### [来自 Nuxt.js 的 7 个前端架构教训](https://dev.to/kball/7-frontend-architecture-lessons-from-nuxtjs-3a16)

(偏见警报——我写的这个)。在我几周前发送的调查中，我从你们那里听到的最重要的事情之一就是对前端架构的更多资源感兴趣。这篇文章是我试图满足这一需求的第一篇文章之一——研究 Nuxt.js 框架所采用的架构选择，以及这些选择的好处。

##### [Next.js 7](https://nextjs.org/blog/next-7)

虽然 Nuxt.js(上图)是针对 Vue 的，但 next . js(React 的 SSR 框架)的最初灵感刚刚达到了另一个里程碑。我喜欢框架通过动态导入继续标准化超级简单代码的方式。这个版本中还有其他一些好的增量更新。

##### [评估任何新的 JavaScript 库时需要考虑的 12 件事](https://medium.freecodecamp.org/the-12-things-you-need-to-consider-when-evaluating-any-new-javascript-library-3908c4ed3f49)

在前端世界这样复杂且快速发展的生态系统中，最困难的事情之一是决定学习和使用什么库和工具。这篇文章很好地列出了做决定时使用的 12 个不同的标准。

##### [学习 JavaScript:通过共享调用，参数传递](https://blog.bitsrc.io/master-javascript-call-by-sharing-parameter-passing-7049d65163ed)

有点密，但是很重要的话题。理解数据如何在 JavaScript 应用程序中流动，以及数据被复制到哪里，是我见过的许多初级开发人员犯的错误之一。当你调用一个带有常规值而不是对象或数组参数的函数时，这就深入到了幕后发生的事情。

##### [采访自 JSConf](https://changelog.com/jsparty/43)

(偏见警告——我做了这些采访)。我通常不会在这里放太多我自己的东西，我也知道播客链接比一些链接更没意思，但我听到这些我不得不分享的内容时非常兴奋。我和 JSConf 的人进行了一些超级酷的采访。我们，包括干代码的重要性，我们对软件使用的隐喻，拉丁美洲的 JavaScript 社区，如何在大公司倡导现代技术堆栈，以及培养导师。通过这些对话，我学到了很多东西，当我回去再听一遍的时候，我学到了更多。抄本也可用，所以你不必听播客。

#### 其他牛气

##### [与工人一起彩色骑车](https://daverupert.com/2018/09/color-cycling-image-pixels-with-workers/)

这是一个非常有趣的循序渐进的故事，试图在画布标签中创建一些动画(通过像素移位)，遇到性能挑战，并通过服务人员将计算卸载到后台线程。我真的很想知道，用 SVG 过滤器以较低的性能成本可以达到多少效果。

##### [长宽比媒体元素和内部尺寸](https://css-tricks.com/aspect-ratio-media-elements-and-intrinsicsize/)

概述了一个处理媒体元素(如图像)纵横比的本地方法的提案。这绝对是我们需要的东西，看起来它现在正在 Chrome 中开发。酷！

##### [怎样才算一个好的前端开发者？](https://css-tricks.com/what-makes-a-good-front-end-developer/)

CSS Tricks 的 Chris Coyier 提出了这个问题，并收集了该领域许多杰出人士的答案。我喜欢“同理心”这个词出现多次——我认为这是任何开发角色的关键，但尤其是前端，我很高兴看到它被强调。

##### [HTML 元素，联合起来！结合元素的类似伏特龙的力量。](https://css-tricks.com/html-elements-unite-the-voltron-like-powers-of-combining-elements/)

作为一个从后端起步并逐渐向前端发展的人，我不断发现我的知识中有趣的漏洞，而那些一开始就专注于前端的人可能没有。比如这些超级酷的语义 HTML 元素。有一个元素专门用来表示键盘输入的文本？整洁！

##### [用 Firefox Reality 探索身临其境的网络。现可用于 Viveport、Oculus 和 Daydream](https://blog.mozilla.org/blog/2018/09/18/firefox-reality-now-available/)

这非常有趣——一个专门为虚拟和增强现实耳机设计的网络浏览器。感觉这仍然是一个未开发的领域，有一些有趣的实验和游戏，但真的有很多人仍然试图弄清楚 VR 将如何工作，以及什么是杀手级应用。将此作为体验中的发现途径可能有助于加快探索过程。

* * *

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】