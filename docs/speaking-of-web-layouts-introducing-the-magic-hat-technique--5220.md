# 说到网页布局。介绍魔术帽技术🎩✨

> 原文：<https://dev.to/albinotonnina/speaking-of-web-layouts-introducing-the-magic-hat-technique--5220>

你能想出你正在开发的一个应用程序，并想象下面的技术应用到它的一部分吗？那么，你会使用它吗？
**读数大约需要 5 分钟。**
谢，白化星人。

[ [关于 medium.com 的原始帖子](https://medium.com/@albinotonnina/magic-hat-technique-408a3fa590bb)

很久以前(几周前)，我被要求开发一个新产品。*“用 UI & FE 开始 solo，这是绿地，随意实验。”*
我想我的回答，比如“好吧”，有点言过其实。

[![](../Images/f37ed465143925d321bde1f757bd5e33.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yFPPNgfE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/twasn9517wnjxltc71a8.gif)

可悲的是，几个漫长的星期后，我不知道如何为这个应用程序建立一个界面。

我说好的，*想想*，我们需要一个页眉，我们需要一个页脚，我们需要一个侧边栏，这就是我所知道的。然后，我们将开始制作页面，我们将把重要的东西放在剩余的空白区域，你知道，应用、核心、果汁、T2 等等用户应该玩的东西。

就像下图一样，对吗？

[![This is my canonical idea of a web app](../Images/ddefa71b5d9e792dd1b312c2be79a5a8.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--LL10pe12--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/5760/1%2A6DpJPOVyaAkfDsFPhAZdig.png) *这是我对一个网络应用的典型想法*

我对自己说:**不！不对！我已经把自己关在笼子里**了，而且我几乎还没有做原型！我说，这些信息流太复杂了，无法装进这些 *A4/Letter* 冻结的框架里。我要花很长时间来计算这里的用户流量。让我们换个角度。**让我们在这件事上全力以赴！**这次我大声说了出来(当时我有点激动)

我想让复杂的东西看起来非常简单。我想**给用户一种强烈的控制感**。我想要一个*智能*应用程序，但我不确定我这样做是否正确。

[![](../Images/9b2e025cd091844a6c8aa379eba90ec3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zZDGqL4R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3okj7pt8dy1hlj6rtzkc.gif)

## 拖延救援！

显然，在我和自己激烈的头脑风暴会议之后，我正看着我的手机享受一个完全值得的休息。
你知道，与我为网络应用程序设计的界面相比，移动用户界面看起来非常清晰。那里的 UX 恰到好处，认知负荷低，动作只是 **yummy** 。

那和**反应**，这就是**魔法帽技术**的这个想法的来源。还有很多能量饮料。
的**陈述性的**、**功能性的**、**反应性的**方面，大多是和**模块化的**。

## 🖼的 MUV

我得到了一个**最小可用视图**的图像，一个 UI，它只做一件事，孤立地，几乎纯粹地，就像我们在代码中做的一样。用户可以集中精力，专心工作的东西。类似 React 组件的东西！桶中的智能砖块做一件事，你，*或者我说的用户*，可以把它们放在一起。

[![](../Images/fbbc41fdcc10018fea99b63d630983ce.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xLs3wa_q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hxm286hyi5fcsg8330bg.gif)

> # and so on!

实际上，两个 muv 可能比一个更好，如果它们以某种方式互补的话。不是三个，肯定不是四个，那对我的大脑来说太多了。

[![Two MUV (Minimum Usable Views), totally made up acronym by the way.](../Images/4e4faabea2830bfa69553dfc3251cd52.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--HugVZu96--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f82r2edhgugrmpso2rzk.gif) *两个 MUV(最小可用视图)，完全是顺便编造的缩写。*

看起来像一个[窗口向下钻取](http://designinginterfaces.com/patterns/one-window-drilldown/) UI 模式，但是有两个，咳咳，muv。😎

> 当一切都在一个屏幕或窗口上时，每个阶段的选项都很清楚，用户知道他们不需要将注意力集中在其他任何地方。
> [帕特森](http://www.pattron.net/Pattern.aspx?Id=81)

非常有利于用户的认知负荷:屏幕内没有太多的东西，只是你想让用户关注的东西。

每个 **MUV** 可以并且应该被设计成几乎完全独立，就像一个**黑盒**:东西进来，数据出去，就像*声明意图*:用户期望在下一帧做*那个*。

是的，因为，就像[单链表](https://github.com/albinotonnina/javascript-data-structures-and-algorithms/tree/master/src/dataStructures/SinglyLinkedList)，**每个 MUV 设定下一步**做什么。

> 单链表是数据元素的线性集合，称为节点，通过指针指向下一个节点。它是由一组节点组成的数据结构，这些节点共同代表一个序列。在最简单的形式下，每个节点由数据和对序列中下一个节点的引用(换句话说，链接)组成。
> [维基百科](https://en.wikipedia.org/wiki/Linked_list#Singly_linked_list)

[![](../Images/a9b486472bcdd5d3aeb56fe7f2184104.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qURb2iJq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/p099s2te6tbombw8auzi.gif)

> # The thing is, at any given point in time, only two related user interfaces are displayed on the screen.

右视图是用户希望在左视图中发生的事情，*是先前左视图的右视图，是先前左视图的右视图…*
*[……]
[……]
..ich 是第一种观点的正确观点。*

## 见缝插针

自然，像这样的技术真的依赖于运动。
[《运动中的 UX 宣言》](https://medium.com/ux-in-motion/creating-usability-with-motion-the-ux-in-motion-manifesto-a87a4584ddc)是必读文章。
在它描述的 12 条原则中，我认为最适用的是:

3) **养育**:在与多个对象交互时创建空间和时间层次关系。

6) **遮蔽**:当效用由对象或对象组的哪一部分被显示或隐藏来决定时，在界面对象或对象组中创建连续性。

## 对了为什么这个名字，魔帽术？

所以我可以同时使用这两个表情符号🎩✨:就像这样:在一个视图中，用户可以选择下一个要显示在屏幕上的东西。差不多就是它了。

## 🏖代码沙箱

首先，React 中的一个基本演示向*展示了机制*。

[![Edit React Magic Hat example](../Images/0b3f0135583496627e3621355d8e9248.png)T2】](https://codesandbox.io/s/r4v7onrpop)

## 🎡实际演示

下面这个是我整理的一个小 app。我希望它能让你明白。从技术的角度来看，每一个 MUV 和 T2 都是独立的，所以开发这样一个应用是非常简单的，因为你是孤立地开发每一个用户界面的。UI 中的*管道*是通过实现这种技术来完成的(我在 React 中做过一个)
视图顺便得到它们想要的空间，并且它们可以按照你喜欢的任何方式来设计。我喜欢让我的*弯曲。我喜欢 flex。和 React 门户。*

> # [T2】🎩https://albinotonnina.github.io/demo-magic-hat/](#httpsalbinotonninagithubiodemomagichat)

## 优点？

想象一下这可以扩展到什么程度。添加一个特征(一个或多个 muv)不会破坏任何布局。你只需*接入*新的 MUV 并添加*链接*就可以从任何其他 MUV 打开它们。

对于用户来说，如果你仔细想想，这应该是很自然的。这与带有后退和前进按钮的浏览器体验没有什么不同。它实际上也是这样工作的。**是用户*创造了*自己的*路径*。这个路径将是用户选择打开的 muv 列表，也是一个准备好被持久化或跟踪的列表。**

## 🛠《反应库》

如果没有一个 UI 框架，比如 React、Vue 或者下一个，这是我做梦都想不到的。

> # Introduction to [Reaction-Magic-Hat](https://github.com/albinotonnina/react-magic-hat)

### 特性

⚡🚀非常快。60+ FPS 硬件加速 CSS 变换，使用[翻转技术](https://aerotwist.com/blog/flip-your-animations/#the-general-approach)。流畅而富有表现力。

🎈超轻，只有 6.5kb，没有依赖性。

🧘‍‍Flexible，有状态或无状态(你得到两个组件)和无用户界面，你做布局和样式。

🎛高度可配置，希望你喜欢这个 API！

## 结论

这是一种技术，目的是通过限制屏幕上的 UI 数量，支持类似于运行时的链接列表，即所谓的 **MUVs** (最小可用视图)，来减少*完成涉及信息处理的任务所需的脑力总量，简而言之就是**认知负荷***

我不知道使用这种技术你能做多少应用程序，也不知道前面的句子对你是否有意义，但是到目前为止，我每次用它做实验都很满意。

我们绝对不应该停止实验，停止从神奇的怪帽子里拿出东西🎩✨

我叫阿尔比诺·汤妮娜，我是伦敦 Workshare 公司的一名开发人员。
我欠这家公司很多，我在那里学到了我还有很多东西要学，永远。

你可以在 [Twitter](https://twitter.com/albinotonnina) 或 [GitHub](http://github.com/albinotonnina/) 上找到我，或者你可以查看[我的网站](http://www.albinotonnina.com)。