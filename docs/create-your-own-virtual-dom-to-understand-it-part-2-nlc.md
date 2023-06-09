# 创建自己的虚拟 DOM 来理解它(第 2 部分)

> 原文：<https://dev.to/aibolik/create-your-own-virtual-dom-to-understand-it-part-2-nlc>

*这篇文章是* [*第一部分*](https://dev.to/aibolik/create-your-own-virtual-dom-to-understand-it-part-1-3g4e) *(所以你最好也读第一部分:)，在那里我们学习了如何用类似 JSX 的函数****hyperscript****创建虚拟节点并渲染它们。在这一部分我们将实现虚拟 DOM 的* ***diffing 算法*** *，这是它的主要优点。*

*本来我是受了《Web 叛逆者 2018》上* [*斯特凡·朱迪斯*](https://medium.com/u/c3a0319fab53)_ 的演讲的启发，所以在这里随意看看吧[。](https://youtu.be/kghSjWmNJyQ)

好的，我们有一个简单的函数组件来呈现动态组件。让我们通过引入它自己的状态并使用 render 方法将其转换为类来使它变得复杂一点。我们也可以单独出新的 ***人物组件，*** 那将只负责渲染表情列表。以下是之后的样子:

<figure>[![](img/1c5fbaa88d69927ecf7ab7951cbe9c85.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tAtVLc71--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AgP5nb4zbPqgKgwtTNWdneA.png) 

<figcaption>**人物组件**，呈现其属性中给出的表情列表</figcaption>

</figure>

这个组件没有什么特别之处:它只是呈现道具中给出的项目列表。**注意**:我们在这里介绍了 render 方法——它实际上创建了我们用来渲染真实 DOM 的虚拟 DOM。下面是我们的 ***App 组件*** 将如何变化:

<figure>[![](img/b60a345d1a958c4ef87028b9d2f24018.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Acokp9Nl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A23EwA0oCCuzbjJvBpM2RTQ.png) 

<figcaption>**App 组件**变成了类组件</figcaption>

</figure>

这里要提到两件事:

1.  我们在构造函数中设置它的状态，并创建*定时器，*每 1 秒(1000 毫秒)更新一次它的状态
2.  我们调用 setState 方法来更新它的状态。

你可以在[的文档](https://reactjs.org/docs/faq-state.html#what-does-setstate-do)中找到 setState 方法做什么反应。我们将在这里实现几乎相同的功能——更新它的**状态**对象和**使我们的组件重新呈现。**当然，React 的实现并不简单，它在此基础上进行了大量优化，但拜托，我们只是在构建它:)

因为 setState 是每个函数都会调用的方法，所以创建一个实现它的抽象类是有意义的。当你写类时，MyApp 扩展 React。在 React 你是*组件继承*[**组件**](https://reactjs.org/docs/glossary.html#components) 类并可以使用其方法。好了，让我们先来看看简单的基本组件类:

<figure>[![](img/a1e5d650f29df0347460e323e4eba6b7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0XldU82b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/858/1%2AD5-dNjonvaqEbQC98iUt9A.png) 

<figcaption>**基础组件**实现</figcaption>

</figure>

基本上，我们只是用 props 和 state 字段初始化我们的组件，并实现 setState 方法，该方法基本上重写了我们的状态，并在其参数中调用自身的 **renderComponent** 。我现在解释一下，但首先让我们对组件进行更改:

<figure>[![](img/8eea6d47c3edc75931f37f91171acd27.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FP4SDCPF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/716/1%2AeKQkT7QW7w98peWRoGDFLw.png) 

<figcaption>修改我们的组件</figcaption>

</figure>

好了，现在我们已经更新了我们的**应用**和**人**组件，我将试着解释一下 **renderComponent** 应该做什么。

1.  它获取旧的基础(保存在 component.base 中的更改前的当前 DOM)
2.  渲染虚拟 DOM——我们可以从 *component.render* 方法中获取，并保存到 *component.base.* 中
3.  用新的孩子替换旧的孩子

这是:

<figure>[![](img/c2292bcfe1a62713a931cb925e719ccb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_VEVCW5p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AECgKa89HTXvXDMrIghlilg.png)

<figcaption>**render component**函数实现</figcaption>

</figure>

> 我已经添加了 parent 参数，我们可以在第一次渲染根组件时使用 parent.appendChild。

一个小小的改变使它变得完美是改变一点点 renderNode 函数。记住它以前的样子:

<figure>[![](img/acaa42132b7275915e5d673315fa0112.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4lGB6tlE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2ARxplN3cdbxAPc8Qr5P-vUQ.png)

<figcaption>**render node**</figcaption>

</figure>

它只处理 DOM 元素，但实际上我们的 ***App 组件的***render _*method * **:_ * *
中有这样的东西

```
return h('div', { class: 'app' },
 h('h1', null, 'Simple vDOM'),
 h(People)
 ) 
```

Enter fullscreen mode Exit fullscreen mode

如您所见， **nodeName** 不仅可以接受字符串值，还可以接受另一个组件/函数来呈现，在我们的例子中，这是 ***人组件*** 。我们需要确保正确处理它们——基本上，我们需要初始化组件，渲染它，并对它的所有子组件执行此操作。这是我们更新的 renderNode 实现:

<figure>[![](img/e949734612c6755b106fcba3cfd2b97a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7HcD8IZC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AudGX7_pc2dUa3vVwRvyVlw.png) 

<figcaption>更新 **renderNode** 函数</figcaption>

</figure>

渲染就是这样！现在，我们的应用程序工作得很好:任何状态变化都会导致应用程序重新呈现——因此每个子元素/组件都会更新。为了利用这种方法，让我们将应用程序中的更新操作从 ***App 组件*** 移到 ***People 组件*** *(从 App 中移除构造函数并将其写入 People)****:***

<figure>[![](img/e8ec6abaa484615a96be9823e2221e15.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7xLFe4yE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AsuVzr44RaWNPduSMVh1t9Q.png) 

<figcaption>**建造师**组成人员</figcaption>

</figure>

并由此更新人的呈现方法:返回 h('ul '，null，...props.list.map(item => h('li '，null，item)))到这个返回 h('ul '，null，...state.list.map(item => h('li '，null，item)))

现在只有 ***人物组件*** 在其状态更新时会被重新渲染。比较这些 gif:

<figure>[![](img/6f18d06396b9c7cb368ba585b7cd2d66.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EqAsinsZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/480/1%2AOD2VWhF04kXUvHBEdRvVCA.gif) 

<figcaption>应用重新渲染</figcaption>

</figure>

**之前

<figure>[![](img/c5a5a545a289f5a02f7a042acfd3c84b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Q9WzSPJT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/480/1%2A1x8MWMqh2HCv4yg8oEjYLg.gif)

<figcaption></figcaption>

</figure>

后应用重新渲染 **如您所见，只有列表会更新，而标题(简单的 vDOM)不会重新呈现。现在，我们让我们的组件实现了自治。

> **注 1** :我改变了一点样式:在 **flex** 行显示的项目，这样添加新项目就不会引起重排。
> 
> **注 2:** 要查看网站的哪些部分可以重新渲染，你可以在 Chrome Dev 工具- >更多(…图标)- >更多工具- >渲染中启用**油彩闪烁**

### 差分算法

嗯，它看起来更像是 *React-ish* ，但是当我们刚刚向列表添加一个新项目时，重新渲染整个应用程序是很糟糕的。现在是时候引入“**差分”算法**了，这将使我们的应用程序更加强大。

在我们的例子中，我们可以简单地检查在新的虚拟节点中是否有新的条目，如果有，我们将添加它。所以我们开始一步一步的实施吧。首先，如果没有提供 DOM，我们只想呈现我们的虚拟节点并将其附加到父节点:

<figure>[![](img/959c72927e963cb6caa0d33291d07e59.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--l2D3yegQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/812/1%2A8lZ2fUQEgnjgfFZvNL4YFg.png) 

<figcaption>渲染我们的 app 并追加到父项，如果没有 DOM 比较</figcaption>

</figure>

这适用于第一次运行的*——这里还没有呈现 DOM，也是我们在其参数中给出 _parent* 的唯一地方。所以我们可以用它来进行初始渲染，我们将这样做:

<figure>[![](img/bb170cd88c966d7b6e45b828b858753b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AxDT2Mjg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/952/1%2A4gZn82zKuRjhrG1k2V3XFQ.png) 

<figcaption>渲染 app 第一次</figcaption>

</figure>

好了，现在我们渲染了我们的应用程序， ***人物组件*** 将在 1 秒后更新其状态(还记得我们设置的*计时器*吗？).这将导致我们的 renderComponent 被调用，因此它也将被更改:

<figure>[![](img/bb7726d13f9699bc782a95da06d281d3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Beo4tAnK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AWDNSeZ6pk67WgR256eTB6A.png) 

<figcaption>更新 **renderComponent**</figcaption>

</figure>

首先，我们用 component.render 方法创建虚拟 DOM，然后我们将它与当前 DOM 状态(保存在 component.base 字段中)进行比较，并再次将 **diff** 的结果保存在其中。所以我们的 **diff** 函数应该检查是否有新的子节点，如果是，我们只需*追加*它。这是它的样子:

<figure>[![](img/381a6dc11bdfe4ff61176917930d8cd1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Y2y9pmV8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AwEA0oTjVUZPluaaaqYzSHg.png) 

<figcaption>简单区分，即检查新的子节点</figcaption>

</figure>

就这样，我们成功了！现在看看它在我们的第三次迭代中的样子:

<figure>[![](img/981571870d376d3c73f654a54a2835d7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jdxkTlD2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/480/1%2AIdRJUrYBPB7rl61Ga8ZK0A.gif) 

<figcaption>用差分算法重新渲染</figcaption>

</figure>

正如你所看到的，整个应用程序和列表没有重新呈现。只有新的孩子被添加到列表中。

这只是一个简单的例子，有简单的算法*、*，但是*希望*、**对那些不熟悉这些概念**、*的人有意义。它不能和 React 相比。以下是 React 不同于 T9 算法的几个方面:*

1.  比较类型
2.  比较属性(道具)
3.  比较孩子们

当然，在引擎盖下有大量的优化，这使得 React 成为如此强大和美丽的用户界面库。

> 如果你喜欢这个和以前的帖子，请随意 like(❤)并“独角兽”它，这将激励我做更多这样的事情。感谢阅读！
> 
> *更新 24.11.2019:如果你想通过做许多练习来学习 React，请随时报名参加我即将开设的课程 [30 天-React](https://aibolik.github.io/30-day-react/)* 。

资源:

*   斯特凡·朱迪斯:什么是 v…DOM？|网络反叛者 2018
*   [Stefan Judis 在 CodePen 中实施该实验](https://codepen.io/stefanjudis/pen/vjYWNO)
*   [本次实验的 GitHub repo](https://github.com/aibolik/simple-vdom)
*   [React 的差分算法](https://reactjs.org/docs/reconciliation.html)****