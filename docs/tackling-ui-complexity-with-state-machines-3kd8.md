# 用状态机处理 UI 复杂性

> 原文：<https://dev.to/carloslfu/tackling-ui-complexity-with-state-machines-3kd8>

[![A simple on-off state machine](img/c5413673fd91a07300e70ae740bf1dd4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HDHOuum0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gq487rt6bjrcbupaomyh.png)

我会告诉你我的故事，也许也是你的…

这是我工作场所美好的一天，我开始编写一个看起来非常简单的应用程序。

随着时间的推移，我完成了一些任务，在某个时候，应用程序开始变得混乱。

我通过一些技巧来解决这个问题，因为我是一个非常聪明和有经验的开发者😉。

但是，我开始感到肮脏，有时惊讶于我头脑中的这个简单的想法是如何变得如此复杂和冗长，以至于无法映射到代码中。我认为我的思维导图和代码之间没有一对一的关系，看起来像是 T2 的一对多关系。

我是创造者。我知道事情是如何运作的，这并不令我担心，因为我尽最大努力编写可理解和可读的代码。

之后，应用程序开始变得越来越大，现在我必须和一个同事一起工作来加快速度。

现在，我的同事有很多问题，因为他不知道这个该死的应用程序是如何工作的！以及背后的想法是什么。我关心他的健康，并花时间向他解释准则。我是一个善良的人，我不想让他浪费时间😅。

一段时间后，我被分配到其他应用程序，现在，我可以理解我亲爱的同事理解我的代码是多么痛苦，因为我必须弄清楚 cr*p 这个新的 UI 代码是如何工作的。

几个月前，我被重新分配到第一个解决 bug 的应用程序，你知道，我应该弄清楚这些天我的想法如何。

[![Batman interesting meme](img/23f3115b7b944f084f66ccb588d90724.png)T2】](https://i.giphy.com/media/a5viI92PAF89q/giphy.gif)

我一直认为我缺少了一些东西，一些没有扩展和过时的 UI 规范文档的解决方案。能让我和其他人更快地抓住想法，花更少的时间理解代码。是的，我是对的，最后，我找到了。

# 介绍状态图

这些问题几乎发生在所有参与任何类型的反应式系统开发(如 UI 开发)的人身上，有些人已经习惯了与它斗争，这似乎是不可避免和自然的，但事实并非如此。

这个问题是众所周知的，*由于事件编排的复杂性*，反应式系统变得复杂。而且，解决这个问题是从一开始就要把事情搞清楚。所以，你的心智模型应该以一种你和其他人都可以很容易理解的方式编码。

简单地说，**状态图**是一个纯粹的函数，它以一种明确的方式包含所有与事件状态编排相关的逻辑。这样我们可以避免最常见的错误，如果有一些，很容易找到它们。

有了状态图，您就有了一种简单且易于管理的方法，通过使用图表和一些结构来组织这种逻辑。这给了我们推理复杂 UI 流的能力，同时防止了意外行为的发生。仅供参考，这种形式主义在美国宇航局用于火星科学实验室任务。

[![Taken from the NASA gallery](img/d5a8cf20502e1ac97dd6402e49f5c9db.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1CnE3fhg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/b5qmfaw7fm1gzjrtue43.jpeg)

没错。好奇号使用状态图！😮 😎

```
state, actions = Statechart(state, event, data?) 
```

你给状态图*当前状态*、发生的*事件*和可选的一些*外部数据*，它还给你*下一个状态*和应该执行的*动作*。**动作**是一个非常简单的概念，你可以把它理解为响应发生的事件而应该执行的命令或功能。

## 你的代码中已经有了状态机

所以，你的代码中已经有了这个逻辑，但是问题是**不是显式的**。这里我不得不引用雷金纳德·布莱斯维特的话:

> *任何足够复杂的模型类都包含一个特定的、非正式指定的、充满 bug 的、缓慢的半状态机实现。——雷金纳德·布雷斯韦特(Reginald Braithwaite)在《我如何学会停止担忧》和《❤️的国家机器》中* 。

您已经在您的 UI 代码中实现了所有类型的流，并且可能是通过手动处理所有情况并通过做一些探索性测试找出边缘情况来实现的。这就是所谓的*自底向上的方法*，这也是目前开发 ui 最常见的方法。我们将事件监听器附加到 UI 组件上，并在其中执行某种操作。例如，在普通的 JS 中，你可以有:

```
const signUpButton = document.getElementById(‘signUpButton’)
signUpButton.addEventListener(‘click’, () => {
  // mutate state and/or perform side effects
}) 
```

但是 **Redux** 呢？嗯，在 Redux 式的架构中，你将事件处理程序分为 reducers、状态管理和效果处理程序。这是一个很好的实践，Redux 解决了状态管理问题，但是您仍然需要处理事件编排问题。

## 范式问题

上述方法被称为*事件-动作范例*。在这种范式中，当某个事件发生时，您会触发一个动作来响应它。它的问题是，为了协调 UI，您必须在事件处理程序中放置许多条件，更糟糕的是，您可能嵌套了 if-else 条件、switch cases 等等。

在 Redux 中，你把它分解到了 reducer 和 effect 处理器中，但是你仍然有同样的问题。这种范式使您的代码变得复杂并且容易出错。作为一名开发人员，你必须记住所有这些情况，并且小心不要忘记某些事情。

状态图使用*事件-状态-动作范例*，这是复杂用户界面的本质。你可能会问，状态图和状态图有什么区别。长话短说:

```
statecharts = state diagram + nesting + parallelism + broadcast communication 
```

# 构建状态图🏗

有一种使用 XML 构建状态图的符号叫做 [SCXML](https://www.w3.org/TR/scxml/) 。我们将使用 XState，这是一个 JavaScript 库，它有自己的符号，但实现了 SCXML 规范的大部分内容。让我们动手构建我们的第一个状态图。

## 状态图

*状态图由节点和边构成*。一个节点代表机器的一个可能的**状态**，一个**边**代表状态之间的转换。转换是为响应事件而分派的，这些事件通常是用户事件。

因此，让我们实现一个简单的开关状态图:

[https://stackblitz.com/edit/xstate-example-on-off?embed=1&&](https://stackblitz.com/edit/xstate-example-on-off?embed=1&&)

## 嵌套状态

我们来做一些嵌套状态。假设当我们的 UI 处于 **on** 状态时**红绿灯**可见，用户可以点击它们。

[https://stackblitz.com/edit/xstate-example-on-off-nested-lights?embed=1&&](https://stackblitz.com/edit/xstate-example-on-off-nested-lights?embed=1&&)

## 条件

我们可以进行条件转换。例如，我希望用户仅在机器处于 *on.green* 状态时关闭机器。为此，让我们使用**在后卫**。

[https://stackblitz.com/edit/xstate-example-on-off-nested-lights-conditional-off?embed=1&&](https://stackblitz.com/edit/xstate-example-on-off-nested-lights-conditional-off?embed=1&&)

# 结论

您可能想知道，在什么情况下我需要它？那么，让我们开始将它用于小部件和有状态组件。

所以，当你必须编排事件(状态+网络请求+用户交互+任何事情)时，*记住状态图是你的朋友*。

# 接下来是什么

在下一篇文章中，我将展示如何创建一个真实世界的应用程序，并解释一些为你的应用程序构建状态图的良好实践和技巧以及其他状态图特性。

感谢 SchoolApply，特别是鼓励我学习和发现 Statecharts 世界的 Mikael Karon。