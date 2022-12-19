# 创建自己的虚拟 DOM 来理解它(第 1 部分)

> 原文：<https://dev.to/aibolik/create-your-own-virtual-dom-to-understand-it-part-1-3g4e>

在这篇文章中，我想回顾一下我创建自己的“虚拟 DOM”的经历。听起来太雄心勃勃了？可能吧，但它没有你想象的那么复杂。正如标题所述，当你创建自己的应用程序时，这将是有意义的，而不是阅读成千上万的相关文章。

*最初，我受到了[斯蒂芬·朱迪斯](https://medium.com/u/c3a0319fab53)在 Web Rebels 2018 上的演讲的启发，所以可以随意看看它 [*这里*](https://youtu.be/kghSjWmNJyQ) 。*

虚拟 DOM 的概念在 2013 年[发布](https://reactjs.org/blog/2013/06/05/why-react.html)时开始流行。因为这个概念，ReactJS 是构建 UI 的超快速库之一。我会试着用几句话解释一下，然后我们会开始写自己的。

> 虚拟 DOM 是 DOM 作为对象的表示。当应用程序的状态发生变化时，新的虚拟 DOM 将与 DOM 进行比较(应用**)并且仅反映变化，而不会导致 DOM 的完全重新呈现。**

 **好的，这是一个我们如何创建虚拟 DOM 的计划。

1.  创建 *hyperscript* 函数来呈现 DOM——这有点像 [JSX](https://reactjs.org/docs/jsx-in-depth.html)
2.  使用 hyperscript 创建简单的应用程序
3.  让我们的应用程序动态化，并学习渲染虚拟 DOM
4.  实现 diffing 算法来看看虚拟 DOM 的威力

### HyperScript 实现

如果你曾经和反应堆一起工作过，你可能知道什么是 JSX。它可以是另一个讨论的主题，但很快它将“类似 HTML”的语法转换为 JavaScript 函数调用，在 React 中它被转换为 React.createElement。因此，在这一步，我们的目标是*重新创建*这个令人敬畏的函数。

通常，它是创建虚拟 DOM 的构建块。然而，在这一步，我想强调我们如何用它来构建简单的 DOM，在接下来的步骤中，我们将开发它来制作虚拟 DOM。

该函数的输入有:*节点类型、属性(又名属性)、子节点。*下面是这个函数的一个简单实现:

<figure>[![](../Images/b7e5cd929111d3f3b981eda33b40d9fa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D23Fi7Ms--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Av7t7-08nG5TGJFSD0aaqVw.png) 

<figcaption>实现**的 hyperscript** 函数，即构造 DOM 给定 nodeName、attrs 和 children</figcaption>

</figure>

首先，它简单地用 *nodeName 创建 DOM 元素。*其次，设置它的属性，最后一步是添加子节点并检查文本节点。

> 注意:实际上，我们可以更进一步，递归地渲染孩子，但是为了简单起见，我把这个留到后面。

下面是它的使用方法(从现在开始我们将使用 ***h*** 代替 ***hyperscript*** ):

<figure>[![](../Images/632115de6a0318af5a29c8f9c23f1e4d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mKG7HYVH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/500/1%2AdhtTJ-mXbjzWj6dsxvgfyQ.png) 

<figcaption>渲染 h1 元素的简单函数</figcaption>

</figure>

<figure>[![](../Images/858f285e19c1131c05bfc14dc7d4f9d8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NZ0Oecu0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/544/1%2Ayh4ESw-DoyojIG6KpY2BJw.png) 

<figcaption>调用 App()函数的结果</figcaption>

</figure>

#### 使用 Hyperscript 创建应用程序

好了，现在我们可以在 Hyperscript 的帮助下创建一个简单的应用程序。让我们创建一个比前一步更复杂的应用程序。这里是我们更新的 ***App*** 功能。

<figure>[![](../Images/471be18ccf7dbba734b15da864088c6b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WzcTbHhf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/984/1%2AAf-dWCO9YL2_2I37m-AEgA.png) 

<figcaption>【复杂应用】同</figcaption>

</figure>

当**应用**被执行时，它创建一个 *div，*和两个子元素:一个呈现 H1 标题，第二个呈现无序列表。注意，我们将 ***道具*** 传递给我们的函数，并将**道具.列表**呈现为无序列表。让我们添加更多的渲染魔法:

<figure>[![](../Images/4316ba7879bead5d9989d46b26c418b1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ubQ8AOxL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AoarZn_WhFoBFQh1LghDUcA.png) 

<figcaption>用状态</figcaption>

</figure>

渲染 app

一般来说，我们只想将 **App** 函数的输出(即有效的 DOM)呈现到文档主体中，给出包含表情列表的状态。

<figure>[![](../Images/9ec13da6cf9073c3d72671c9c34d90f5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MiHAOtkj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/577/1%2Ag0mDXTJMoiVsJ4dPtjXK0A.png) 

<figcaption>我们工作的结果</figcaption>

</figure>

没那么难。是吗？让我们添加一些动态内容，每 1 秒钟添加一个随机表情符号，这样我们就可以看到我们的应用程序是如何呈现的。

[![](../Images/9fb4156b9f80d646997b7c56c07332bb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--z7csFya9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/856/1%2AgpnljAXNujX8Lkl1JRTEUw.png)

<figure>[![](../Images/7bed56723f7c1422d5bebbd14ddadcef.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7hngij_X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/680/1%2AltA2Y6YhMq8y2s5Kz39C0g.gif) 

<figcaption>DOM 每 1 秒完全重新渲染一次</figcaption>

</figure>

#### 实现 vDOM 渲染

好了，现在我们已经用 *hyperscript* 完成了动态应用程序，让我们继续实际的虚拟 DOM 及其实现。首先我们需要改变我们的*超脚本*函数。现在它不应该创建真正的 DOM，而是应该创建虚拟的 DOM。因此，给定*节点名、* *属性*和*子节点*，我们只需创建一个带有相应键的对象。有了 ES6，我们可以用一条线做到这一点:

<figure>[![](../Images/35e54be1419a5f65328167428ffa02c5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fB3jAGs_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AkZpNI6nl6KVQjfFj2ewY6Q.png)

<figcaption>hyperscript*返回虚拟 DOM 的函数*</figcaption>

</figure>

我们有一个虚拟的 DOM，如果我们使用相同的表情列表执行**应用**功能，我们会得到如下结果(登录控制台):

<figure>[![](../Images/66261b95cde617a0476ce68d1803f498.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JWjKhF91--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/663/1%2AHYsUARTHUNuhAe5uvPeEig.png) 

<figcaption>我们应用的虚拟 DOM</figcaption>

</figure>

和多姆很像。现在让我们创建一个将虚拟 DOM 渲染成真实 DOM 的函数。正如您可能已经猜到的那样，它应该将虚拟节点作为参数。这是:

<figure>[![](../Images/0dcb195da4034cbf760743823e23ff29.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dNGJmc8K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AQkHR2QSgoAvuwkv406bf3Q.png) 

<figcaption>该函数渲染真实 DOM(DOM 节点)给定虚拟 DOM(节点)</figcaption>

</figure>

让我一步一步解释它的功能:

1.  使用析构，我们检索虚拟节点的节点名、属性和子节点
2.  如果 vnode 是文本(我们可以通过 *vnode.split* 来检查它)，那么我们返回文本节点
3.  否则，我们用 nodeName 创建一个元素，并从 *attributes* object 设置它的属性
4.  如果有的话，为孩子们做同样的事情

现在，还记得我们的*渲染*函数渲染我们的**应用**吗？我们只需要做一点小小的改变就能让它发挥作用:

[![](../Images/ebba90fe4a5d4f55e7c36b1632a7fb11.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7BtTiS-X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/812/1%2APlV4Puh-8DuuL5ZI1lD6hg.png)

> 这篇文章比我想象的要长一些，所以我决定分成两部分。

让我们来回顾一下。我们创建了一个 **hyperscript —** *虚拟 DOM factory* **，renderNode —** *将虚拟 DOM 转化为 DOM 元素*和一个*函数组件* ***App*** 创建整个页面。结果现在和我们之前没有虚拟 DOM 时一样，但是现在我们有了更多的控制。在下一篇文章中，我们将探索是什么让 React(和虚拟 DOM)如此之快。

*你可以在我的* [*GitHub 资源库*](http://bit.ly/2NKXH0V) *中查找所有步骤。你可以在分支中找到这些步骤。*

在下一篇文章中，我们将实现简单的**差分算法**，这将使我们的应用程序更快，你将能够看到它的行动。

*更新:嘿，如果你喜欢，去看看这篇文章的第二部分！*

*更新 24.11.2019:如果你想通过做许多练习来学习 React，请随时报名参加我即将推出的课程 [30 天-react](https://aibolik.github.io/30-day-react/) 。***