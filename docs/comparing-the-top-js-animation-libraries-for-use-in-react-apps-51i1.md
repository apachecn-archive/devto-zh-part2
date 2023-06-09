# 比较 React 应用程序中使用的顶级 JS 动画库

> 原文：<https://dev.to/bnevilleoneill/comparing-the-top-js-animation-libraries-for-use-in-react-apps-51i1>

[![](img/9a68719eb7759ebc35757c6d32f050bb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UHymD7w1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ap6A_nE4_qNjEtDffP6IA4A.jpeg)

你可能已经注意到动画在网络上非常流行。针对运动爱好者的教程、案例研究和图书馆并不缺乏。这是开发的一个方面，可以激发、增强、愉悦、激励和打动用户。

在本文中，我选择了五个 JavaScript 动画库，并从商业和开发的角度剖析了它们的性能。

而不是问“我用什么来构建 X？”，我探讨了以下问题:

*   这个项目维护的怎么样？
*   一个团队容易接吗？
*   语法是什么样的？

检查有助于为项目和业务目标提供解决方案的其他方面，例如:

*   成本有关联吗？
*   是开源的吗？
*   仅限会员？
*   有附加产品吗？
*   社区是什么样的？

这些细节有助于企业和团队做出明智的决策，有望满足所有人的需求。

### **是什么造就了一个动画库？**

动画库是创建动画的工具集，并明确允许自定义支持和创建。

这些库还必须提供动画对象的方法，允许控制对象运动的工具，以及对初学者的方向控制。它还可以迎合特定能力的口味，如*反应式*动画。

由于我对每个图书馆的评分是主观的，你的观点可能与我不同。如果一个库为你、你的项目和你的商业目标服务，那才是真正重要的。让我们开始吧。

### **青绿**

文档: **B+** ，特性: **A，**语法: **B+，**社区&支持 **:** **A+，稳定: **A****

[![](img/dbbf81d8c8a535a76ff63c9e9d58e876.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--F-32mejw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AoMzgiPe50QByX6pjumn1gQ.png)

GreenSock (GSAP)是网络动画师的首选 JavaScript 库之一。您可以创建各种令人惊叹的效果，包括那些需要 SVG 支持的效果。

GreenSock 的主要方面是开放源代码，允许您自由使用 TweenMax (113 KB minified)、TimelineLite (12.6 KB minified)、TimelineMax (20.4 KB minified)和 TweenLite (27.5 KB minified)，并且在没有开销成本的情况下满足广泛的需求。如果您对文件大小有所顾虑，请务必阅读这篇名为“[千字节难题](https://greensock.com/kilobyte-conundrum)”的文章

还有一系列方便的插件来帮助扩展 GSAP 功能，例如:

*   **绘制 SVG** —逐渐显示/隐藏 SVG 笔划
*   **MorphSVG** —将任何 SVG 形状变形为任何其他形状
*   **ScrollTo** —动画显示滚动位置。与[卷轴魔法](http://scrollmagic.io)一起工作
*   投掷道具(throw props)—利用物理原理平稳地将任何物品滑行至停止位置。
*   **贝塞尔曲线** —沿贝齐尔曲线制作属性动画。

像 Draggable 和 SplitText 这样的附加组件也可以用来补充您可以随意使用的众多插件。这是格林斯托克产品系列的完整列表，供进一步调查。
[https://codepen.io/chrisgannon/embed/WvJMXP?height=600&default-tab=result&embed-version=2](https://codepen.io/chrisgannon/embed/WvJMXP?height=600&default-tab=result&embed-version=2)
就无处不在而言，GSAP 在大约 4，000，000 个网站上使用，并且已经存在**很长时间了**。这意味着…

*   更熟悉，更多工作，更少培训
*   使用次数越多，漏洞出现和修复的机会就越多
*   承诺的长期记录

总的来说，绿石社区相当活跃。有太多的演示、文章和教程可用，社区在 Slack workspaces 中非常活跃，如工作中的[动画](https://damp-lake-50659.herokuapp.com)、[堆栈溢出](https://stackoverflow.com/questions/tagged/greensock)、[成员论坛](https://greensock.com/forums)和 Twitter。文档写得很好，语法易于理解和表达。如前所述，GSAP 的某些部分是开源的，而插件和实用程序将是[成本](https://greensock.com/club)。

如果您从事 motion 工作，并且希望有一个涵盖许多不同需求的库，提供可靠的社区支持，提供具有易读语法的编写良好的文档，并且始终保持最新；格林斯托克是一个伟大的选择。

*   网址:[https://greensock.com](https://greensock.com/)
*   文件:[https://greensock.com/docs](https://greensock.com/docs)
*   外挂:[https://greensock.com/docs/Plugins](https://greensock.com/docs/Plugins)
*   插件/实用程序:[https://greensock.com/docs/Utilities](https://greensock.com/docs/Utilities)

### **GSAP**反应动画手记

用 GSAP 构建动态环境当然可以通过使用 [ticker](https://greensock.com/docs/TweenLite/static.ticker) 或 [ModifiersPlugin](https://greensock.com/modifiersPlugin) 来完成，如果你的项目需要这种能力的话。

以下是一些使用 GSAP 展示动态运动的附加示例:

*   [https://codepen . io/OSU Blake/pen/65 CDC 21 ce 5c 49 da 45709 ff 9d 09 d0a 754](https://codepen.io/osublake/pen/65cdc21ce5c49da45709ff9d09d0a754)—LERP 框架独立。
*   [https://codepen.io/osublake/pen/dmPyaw](https://codepen.io/osublake/pen/dmPyaw/)—在每一帧上使用 LERP 的指数插值
*   [https://codepen.io/osublake/pen/PPmJpL](https://codepen.io/osublake/pen/PPmJpL)—简单的加法动画
*   [http://codepen.io/osublake/full/zvpEMg](http://codepen.io/osublake/full/zvpEMg/)—加法动画
*   [https://codepen.io/kvndy/pen/YyXLWp](https://codepen.io/kvndy/pen/YyXLWp)—加法关键帧混合
*   [https://codepen.io/osublake/pen/BLOoOP](https://codepen.io/osublake/pen/BLOoOP?editors=0010)—图像平移
*   [https://codepen.io/osublake/pen/XEJMWE](https://codepen.io/osublake/pen/XEJMWE)—屏幕大小:TweenMax + CSS 变量
*   [https://codepen.io/osublake/pen/EEaPKe](https://codepen.io/osublake/pen/EEaPKe?editors=0010)—屏幕大小:修改工具

### **动漫. js**

文档 **: C，**特性 **: B** ，语法: **B，**社区&支持 **: C，**稳定性: **B**

[![](img/59c33da5aa60d0736caa3da8f406b021.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xWcc8xfT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AOBE14NXuAdHGS9YOpVFq2Q.png)

Anime 是一个轻量级(6.2 KB minified)，开源的 JavaScript 动画库。

它可以处理 CSS 属性、单个 CSS 转换、SVG 或任何 DOM 属性以及 JavaScript 对象。它的显著特点是能够链接的关键帧，同步多个实例的时间轴方法，播放控制，单个 CSS 变换控制，多个动画目标的单个值，SVG 路径/线/变形和大量的缓动功能。

它的语法是大家熟悉的，包括当今许多动画库中使用的链接方法。时间轴也很容易实例化，使用对象文字方法控制属性是另一个熟悉的 cowpath。

随着 2016 年 6 月 27 日的[公开发布，这无疑是一个处于起步阶段的项目。然而，根据 GitHub pull 请求，动漫正在积极开发中(这是一件好事)。对于社区支持，我建议在进入堆栈溢出之前使用项目的问题跟踪器，以获得快速支持的最佳机会。](https://github.com/juliangarnier/anime/commit/e27c0a6d1d56291d457f71bd112575dbb7600d74) [https://codepen.io/dennisgaebel/embed/aqXOYz?height=600&default-tab=result&embed-version=2](https://codepen.io/dennisgaebel/embed/aqXOYz?height=600&default-tab=result&embed-version=2)
说到文档，要做好读代码的准备。动漫的文档很难理解，因为它缺乏对演示的解释。我还发现事件控制缺乏您通常在其他库中看到的那种复杂性。

浏览器支持非常好，包括 IE10–11 和 Edge 作为基准测试。但是，对于 iOS 或 Android，没有什么值得注意的。还有一个[压力测试](https://codepen.io/juliangarnier/pen/JKEgpO)也可以用来展示性能。

如果你需要一个文件很小的库，适合简单的时间轴序列，适合 SVG(笔画，变形，变换等)。)，是 100%开源的，而且有大家熟悉的语法，那动漫就适合你。

*   网址:[http://animejs.com](http://animejs.com/)
*   文件:[http://animejs.com/documentation](http://animejs.com/documentation/)

### **Mo.js**

文档 **: C，**特性 **: B** ，语法: **B** ，社区&支持: **C，**稳定性: **C**

[![](img/ef6b8909b50a2c79f7a8410df53b4a3e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dZXMNcfL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AxhppBW642BRuHYIXx-iDcg.png)

Mo.js 是一个开源项目，可以通过 npm 或 CDNJS 安装。它声称快速、可靠、经过单元测试、模块化、健壮，并且有一个简单的 API 所有这些都有助于减少大文件的开销。

我发现这些文件有点混乱。它们并不总是包含对其他演示中使用的方法的解释，所以您必须努力寻找符合您需求的答案。我还努力寻找一个关于简单运动工作的用途(这个项目的一些方面在如何做普通动画任务方面还不清楚)。当然，你也可以用 Mo.js 做出一些非常酷的效果，比如这个气泡布局:
[https://codepen.io/sol0mka/embed/yNOage?height=600&default-tab=result&embed-version=2](https://codepen.io/sol0mka/embed/yNOage?height=600&default-tab=result&embed-version=2)
经过对 GitHub repo 的检查，这个项目没有太多的贡献者，而且最近也没有发布更新。该项目最近的承诺在 2016 年年中至 2017 年年中之间，但此后一直没有太多行动。

我从可靠的消息来源得知，由于创建者的忙碌生活，这个图书馆已经停滞不前，所以这个图书馆的未来是非常开放的。我建议避开它，因为支持将是暗淡的，社区是小的，图书馆的未来方向是不确定的。

*   URL: [http://mojs.io](http://mojs.io/)
*   docs:[https://github . com/Lego mushroom/mojs/blob/master/API/readme . MD](https://github.com/legomushroom/mojs/blob/master/api/readme.md)
*   附加功能:[https://github.com/legomushroom/mojs-player](https://github.com/legomushroom/mojs-player)https://github.com/legomushroom/mojs-curve-editor[T3】](https://github.com/legomushroom/mojs-curve-editor)

**pop motion**
T3】文档 **: D，**特性 **: C** ，语法: **C** ，社区&支持: **B，**稳定性: **B**

[![](img/56208b9c0c399de9810925ad6fcbfd39.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Bzx8PTBu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AV69F0DP6GWAx1e4IYbpJmg.png)

Popmotion 是一款 11.5 KB 的瑞士军刀，适用于旨在成为乐高积木的动画师，并附带有混合、可拖动、姿势、反应和可旋转的软件包。一个功能性的，*反应性的* JavaScript 动作库，提供了补间的方法，然而，文档可能是一个很难破译的；由于 styler 的解释含糊而简短，因此很难让人理解。

您也应该能够自如地使用 ES6 编写，因为所有的演示和代码示例都是以这种方式编写的。这个库的整体语法确实令人困惑，文档的措辞也很复杂，需要对项目内外有深刻的理解。因为你很可能需要很好地理解这个库是如何编写的，如果你很急的话，可能需要一些时间来学习。
[https://codepen.io/popmotion/embed/LeZvXB?height=600&default-tab=result&embed-version=2](https://codepen.io/popmotion/embed/LeZvXB?height=600&default-tab=result&embed-version=2)
[https://codepen.io/popmotion/embed/paXyRE?height=600&default-tab=result&embed-version=2](https://codepen.io/popmotion/embed/paXyRE?height=600&default-tab=result&embed-version=2)
pop motion 的创建者是[希望 Pose](https://github.com/Popmotion/popmotion/tree/master/packages/popmotion-pose) 的工作将通过提供一个声明性的、特定于 DOM 的层来解决我的主要问题，这将使动画变得超级简单。Popmotion 当然具有基于鼠标位置的反应式动画功能，以及任何不基于持续时间的功能。大多数开发人员会发现学习曲线要求很高。理论上这听起来不错，但是当你真正开始尝试编写代码时，它会令人惊讶地繁琐，并且需要手工编写许多方面。如果你想要一个反应式的动作库，体积小，100%开源；Popmotion 是给你的。

如果你需要支持，可以通过 **#popmotion** 频道和 GitHub 的问题跟踪器，在[工作松弛期](https://damp-lake-50659.herokuapp.com/)的动画工作空间中联系。

如果你想要一个专注于反应式动画的库，100%开源，并且愿意预先做一些艰苦的开发工作，那么 Popmotion 就是为你准备的。

*   网址: [https://popmotion.io](https://popmotion.io/)
*   文件:[https://popmotion.io/learn/get-started](https://popmotion.io/learn/get-started/)

### **速度**

文档 **: B+，**特性 **: B+** ，语法: **B+** ，社区&支持: **B+，**稳定: **B**

[![](img/dfced5e4b55e051bb1e17e7a890c1eb6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8JwzLXln--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AcOXabt0u_0WE44NFrX7KiA.png)

熟悉 jQuery 的人会觉得 Velocity 如鱼得水。Velocity 是一个动画引擎，与 jQuery 的 **$具有相同的 API。animate()** 。不管有没有 jQuery，它都可以工作。它具有彩色动画、变换、循环、缓动、SVG 支持和滚动功能。

如果你决定将 jQuery 和 Velocity 一起使用，只需要替换掉 **$就可以了。**同 **$。速度**。它可以在任何地方工作，包括支持早至 IE8 和 Android 2.3。还有很多放松选项可供选择，包括弹簧物理。
[https://codepen.io/tommiehansen/embed/netCu?height=600&default-tab=result&embed-version=2](https://codepen.io/tommiehansen/embed/netCu?height=600&default-tab=result&embed-version=2)
目前，根据 GitHub 提交，该项目仍在积极开发中。这是一个已经上市很长时间的库，被广泛使用，并且非常有名。Velocity 旨在通过提供的性能测试，从一开始就让您相信它的真正性能。如果你发现自己被困住了，也有大量的文章，包括视频和教程/书籍。如果你是 ScrollMagic 的用户，你会很高兴听到它与 Velocity 配合得很好。对 SVG 的支持也很棒。

如果您需要一个库，其中的文档可读性好、语法友好、支持或不支持 jQuery、有长期的跟踪记录、有丰富的教学材料资源、有良好的社区支持，那么 Velocity 就是您的理想之选。

*   网址:[http://velocityjs.org](http://velocityjs.org/)
*   文件:[https://github.com/julianshapiro/velocity/wiki](https://github.com/julianshapiro/velocity/wiki)
*   外挂:[http://velocityjs.org/#uiPack](http://velocityjs.org/#uiPack)http://velocityjs.org/#vmdT2[http://velocityjs.org/#ember](http://velocityjs.org/#ember)

### **离别的思念**

虽然从长远考虑很重要，但在做决定前征求团队反馈也同样重要。如果你今天选择了一个极简主义的图书馆，因为它在技术上满足了你在那个特定时刻的需要，你可能不会计划 6 个月后当你需要添加一些更雄心勃勃的东西时会发生什么。

如果你使用的库缺乏吸引力或支持，从长远来看可能会适得其反；尤其是当你遇到浏览器漏洞的时候。一个长期的记录有助于企业感到安全，但是一个有很好的文档记录，易于阅读的语法，强有力的支持和良好的记录会让双方都满意。让我在评论中听到你的想法，并祝你快乐！

*特别感谢 Animation At Work Slack 社区为这个话题贡献的时间和观点。*

**附加库&参考文献**

*   SnapSVG[http://SnapSVG . io](http://snapsvg.io/)—Adobe 的 JavaScript SVG 库
*   just Animate[https://just-Animate . github . io](https://just-animate.github.io/)—补间引擎
*   tween js[https://createjs.com/tweenjs](https://createjs.com/tweenjs)—一个用于 HTML5 和 JavaScript 属性的补间和动画的 JavaScript 库。独立工作或与 EaselJS 集成。
*   sequence JS[https://www.sequencejs.com](https://www.sequencejs.com/)—一个 JavaScript 库，提供了一个响应式 CSS 框架，用于创建独特的滑块、演示、横幅和其他基于 step 的应用程序。
*   感人的书信[http://tobiasahlin.com/moving-letters](http://tobiasahlin.com/moving-letters)
*   变形[https://github.com/notoriousb1t/polymorph](https://github.com/notoriousb1t/polymorph)—变形 SVG 路径
*   path . js[https://github.com/SamKnows/path.js](https://github.com/SamKnows/path.js)—一个用于在 SVG 路径之间变形的库
*   冗长的[https://github.com/shshaw/lengthy-svg](https://github.com/shshaw/lengthy-svg)—CSS 变量中 SVG 形状长度的微库
*   [https://github.com/googlearchive/flipjs](https://github.com/googlearchive/flipjs)—[翻转动画](https://aerotwist.com/blog/flip-your-animations)的助手库。
*   [https://github.com/davidkpiano/flipping](https://github.com/davidkpiano/flipping)—一个很小的库(和适配器的集合)，用于轻松实现翻转转换。
*   TweenRex[https://github.com/tweenrex/tweenrex](https://github.com/tweenrex/tweenrex)——一个反应式推特引擎
*   网络动画工具箱[https://we b-Animation . github . io](https://web-animation.github.io/)—牛逼工具&书籍&文章&网络动画视频

1.  *反应式* —“反应式动画”是一种涉及离散变化的动画，由于事件(*Conal Elliot/Paul Hudak 1997*)或基于用户事件/输入和不同输入/事件值发生的变化。反应式库一般不会提前“思考”。【https://www.youtube.com/watch?v=lTCukb6Zn3g】T4。[http://slides.com/davidkhourshid/flipping#/42](http://slides.com/davidkhourshid/flipping#/42)* * * * # Plug:[log rocket](https://logrocket.com/signup/)，一款用于网络应用的 DVR

[![LogRocket Dashboard Free Trial Banner](img/0abf868fe5ccbed99d71cb8d9e81ed98.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6FG5kvEL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/blog.logrocket.com/wp-content/uploads/2017/03/1d0cd-1s_rmyo6nbrasp-xtvbaxfg.png%3Fresize%3D1200%252C677%26ssl%3D1)

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

比较 React 应用中使用的顶级 JS 动画库的帖子[首先出现在](https://logrocket.com/blog/comparing-the-top-js-animation-libraries-for-use-in-react-apps-2/)[日志博客](https://blog.logrocket.com)上。