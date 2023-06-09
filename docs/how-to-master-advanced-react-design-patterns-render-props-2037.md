# 如何掌握高级 React 设计模式:渲染道具

> 原文：<https://dev.to/shaunoff/how-to-master-advanced-react-design-patterns-render-props-2037>

### 如何掌握高级 React 设计模式:渲染道具

[![](img/d7079ff6831f3bf71527a8372661314c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0JKTV5so--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Aji5DgEliT0219-opdiE5dw.png)

在本系列的第 1 部分中，我们讨论了如何使用复合组件和静态类属性来生成可读和可重用的 Stepper 组件。我们看到这种设计模式有一些限制，因为它不是很灵活；组件需要是父组件的直接子组件，否则它会断开。

[点击此处查看第 1 部分](https://dev.to/shaunoff/using-advanced-design-patterns-to-create-flexible-and-reusable-react-components-part-1-2040-temp-slug-5672359)

在第 2 部分中，我们使用了新的上下文 API 为第 1 部分的局限性提供了一个优雅且易读的解决方案。这种设计模式的问题是，它需要一些初始设置才能工作，我们的组件不能放入另一个应用程序中，并且在没有初始样板代码的情况下独立工作。

[点击此处查看第二部分](https://itnext.io/using-advanced-design-patterns-to-create-flexible-and-reusable-react-components-part-2-react-3c5662b997ab)

在这一部分中，我们将讨论一种设计模式，它可以解决到目前为止我们已经确定的所有问题。它叫做:渲染道具。

这种设计模式一开始可能有点让人摸不着头脑(还记得我们在第 2 部分中放在上下文消费者内部的函数吗？)为了真正掌握它到底是如何工作的，我们需要深入了解顶级 React API，以及我们编写的 JSX 代码是如何转换成 javascript 的。因此，让我们用一个非常简单的例子，并通过引擎盖下发生了什么。

### JSX

JSX 是由脸书的工程师设计的 JavaScript 的语法扩展。我们用它和 React 来描述 UI 应该是什么样子(有点像模板语言)，但是它具有 JavaScript 的全部功能。无论何时你用 JSX 编写任何组件，Babel 都会把它编译成一个 React.createElement()调用。

[![](img/17cae2b0a2137541909302f4fb0d1bb2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FDLOl9u---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ao0vuU7Dzb5aaz7m3p9-Y5A.png)

让我们看一个非常简单的例子:

[![](img/5266cc08b4a558af1d30b0a1ba517b9e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5iUnlsJF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A1KwKntECiGRJU5sxnL7LYA.png)

上面的两个例子产生了相同的结果，父组件被简单地转换为 React.createElement()调用，类型是我们的“父”组件，没有道具，也没有子组件。

[![](img/e521765673adb9bbfa9953cc605d208f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UOazoM4l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AMZqaPGcEykaEhvp8X1TrlA.png)

当我们添加一个子组件时，请注意它本身是如何被转换成 React.createElement()调用的，正是这种格式创建了我们的 React 组件树。

[![](img/1dd36294a1f823f3d051632d0d1fab07.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WrHTHPH6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AZuKIoHa_UECNwLVdSVgYMA.png)

这里需要理解的关键点是，Babel 编译任何作为单个 props javascript 对象添加的 props；因为它是纯 javascript，所以我们可以传递任何我们想要的东西，比如函数。

[![](img/4e99eb1dc22548d46d6776b64396756f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2tjWyLyg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ApxbMxCmpRYI-pN0Xul3JjQ.png)

在上面的例子中，我们没有传递“字符串”，而是传递了一个返回“字符串”的函数。所以，当这个函数被调用时，我们可以得到完全相同的结果。

[![](img/9d48a92396dbba15498675087977c4fe.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iSaiz8Lt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2APQgX1RJVXBUHWPYqbAMAXg.png)

那么上面的例子到底是怎么回事呢？在最初的例子中，我们只是传递字符串，把它放在一个“div”中，然后它就被呈现了。然而，在下一个示例中，我们将它作为一个函数传递，并将其放在一个“div”中，但这次调用该函数可以实现完全相同的结果。

### 渲染道具

为什么这很重要？嗯，传统上我们用**渲染子组件**,我们把它们放在父组件里面。

[![](img/64c1d7bb8ec3132549b3f29b9bf02865.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8HTMjeC1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AW4X307RXCRX-M92h2XFeyA.png)

这是需要理解的关键，不是设计我们的组件来渲染一个孩子，而是没有任何东西阻止我们**渲染道具**，同时实现完全相同的结果:

[![](img/97e0917854e485ae87d09f746c2c1026.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YqorS1di--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AmugiWhmq3woQb4oYMp509A.png)

因此，在这个设计模式中，我们**渲染道具**而不是孩子。我们还可以更进一步。我们还能用函数做什么？我们可以在调用参数时传递它们:

[![](img/e7bc42315f04c1900aee559f1bb5ee1a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VWR5_lD0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ayhv4JdMJMlGdenfchcYe2A.png)

让我们花点时间来消化一下刚刚发生的事情。我们已经像以前一样传入了一个函数，但它不是总是返回“字符串”,而是在被调用时返回我们传入的参数！

等等，这不是我们在第 1 部分遇到的问题吗？为了解决这个问题，我们必须克隆元素，遍历每个元素，然后传递任何需要的道具。

使用这种设计模式，我们能够将道具传递给子组件，嘭！

我们可以给这些道具起任何我们想要的名字。因此，我们不用“示例”，而是用更合适的词:

[![](img/9ec19a5a0132084d9b65103dd1f77526.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tuNCHlP---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ADm4kxErU2ytof6UYUMIL7A.png)

如果你以前用过 react 路由器，这可能看起来很熟悉。当你需要传递道具到一条路线时，你需要使用一个渲染函数。

[![](img/9cc979022639b80483269431b1278633.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KnRBGyKK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AHe7Q_dBroJoyZJykddFUIw.png)

这是**渲染道具。**我们可以调用“render”并传入我们想要的任何参数，而不是直接呈现组件。

让我们回到 stepper 组件，看看如何利用这种设计模式(我已经删除了所有的上下文样板文件，并将状态添加回 Stepper 组件)。

[![](img/2abe8c073706b010175b2821f8681495.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sIAYPDVn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Al2Aca-G6z5SVExCUc5ThRA.png)

这次不是添加{this.props.children}，而是添加{this.props.render(stage，HandleClick)}。我们不再需要向 stepper 组件添加任何子组件，我们需要做的只是在 render prop 中返回相同的标记。

[![](img/24202b03fec4bc7dc1846f3a7154bee9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6-EKGsTz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A4e8wL_DEKLswiKya4st1Rw.png)

这实现了什么？现在，树中的每个组件都可以访问所有的道具。它本质上为我们提供了与上下文 API 相同的道具，我们不必手动将道具传递给每个孩子，并且我们可以灵活地移动东西。对组件设计的简单调整解决了我们之前提到的所有问题。

[![](img/d7079ff6831f3bf71527a8372661314c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0JKTV5so--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Aji5DgEliT0219-opdiE5dw.png)

不过，使用这种设计模式有一个小小的折衷。代码比以前可读性稍差。还记得我们在本系列前面看到的那个奇怪的函数吗，我们需要在 Context.consumer 组件中添加一个函数。

[![](img/92ee237209525e42d6a44b0bb3090511.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PUS0G6qW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Avl1h9TNE5VRFo_q3NrM_zQ.png)

对我来说，这看起来可读性很强；让我们想想这是怎么回事。我们没有添加渲染函数，而是简单地添加了一个相同的子函数。

让我们用之前使用的示例组件来尝试这样做:

[![](img/53914914dc94680c9a45f7581f3c6c1e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sj3cS_cb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AQg0GYaUlIrS5Zs6k__DHgw.png)

在左手边，我们像以前一样给渲染道具添加函数。当这个由 Babel 编译时，函数被添加到第二个参数中:props。在右边，我们把它作为一个孩子添加，当它被编译时，它被添加到第三个参数:孩子。

[![](img/17cae2b0a2137541909302f4fb0d1bb2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FDLOl9u---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ao0vuU7Dzb5aaz7m3p9-Y5A.png)

创建组件时，我们如何访问子组件？

 *[![](img/0bad4defa0263c218c6ce57a21ea34ed.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nsPqJmk0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AWWg_57uJXAc5ImvgoY_idw.png)

与调用 render prop 的方式类似，因为 child 是一个函数，所以我们可以调用 props.children 来代替，并传入我们所需的参数，为我们提供与以前相同的结果，并增强了可读性。

[![](img/70c7ef7faf402c0c4d4693adc36e7ab1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aTKgMCVQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AWafoqv6u2sdiGmpkOy4FhQ.png)

现在你知道了，我们设计了一个高度灵活且可读性极强的组件。用户可以自主地重新排列子组件，而不用担心它是否可以访问他们需要的道具。最终，它是可重用的。我们可以将它直接放在任何其他应用程序中，无需任何预先设置，它将完美地工作。

[https://medium . com/media/B1 a 39 A8 c 067 cf 87 b 6 a 0 f1 BC E3 AE 8545 e/href](https://medium.com/media/b1a39a8c067cf87b6a0f1bce3ae8545e/href)

* * **