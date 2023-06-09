# 做出反应。关键点。

> 原文：<https://dev.to/aberezkin/react-the-key-points--4h83>

[![React logo](img/4e8efb997b899cab07b52dc1a89e5132.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BvBER8aJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://logos-download.com/wp-content/uploads/2016/09/React_logo_wordmark.png)

# TL；速度三角形定位法(dead reckoning)

在这篇文章中，我将谈论我认为 React 的关键点。这些是我在了解(剧透警报)库时很乐意知道的事实。

顺便说一下，这是我在 dev.to 上的第一篇文章。希望你会喜欢！

* * *

# 没什么框架

React 有很酷的特性，巨大而活跃的社区，甚至有自己的 javascript 语法！这些事实会让你误以为 React 是一个框架。但事实并非如此。就像我之前糟蹋的那只是个图书馆。

通常，框架可以为您提供构建应用程序所需的一切。看看 angular，比如它有那么多特点。视图渲染，HTTP 交互，动画，表单验证，这些你都能在 angular 里找到。React 只做其中一种视图渲染，而且做得相当好。

任何与呈现视图无关的事情都不是 Reacts 的责任。即使您想使用其他工具呈现应用程序的某个部分，React 也没问题。

软件开发中的每个工具都有其成本。一个框架以更高的成本给你更多的特性。但是尽管 React 的功能很棒，它的成本却相对较小。我想这就是它如此受欢迎的原因。您可以使用它来呈现一个页面，甚至是应用程序的某个组件。您不需要暂停功能开发来迁移整个应用程序以做出反应。如果你认为 react 可以做得更好，那就试一试，只用 React 实现一个组件。React 确实给了你这种灵活性。

# 它拥抱 javascript

这也是 React 不是一个框架的好地方。是的，在 JSX 中有一个额外的语法，但这只是一个语法糖，不像在 Typescript 中引入了新的特性，比如 Javascript 的静态类型。JSX 电码

```

  Hello, World!
 
```

Enter fullscreen mode Exit fullscreen mode

简单地编译成

```
React.createElement(
  Title,
  {large: true, color: 'darkgray'},
  'Hello, World!'
) 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已。

此外，仍然可以使用纯 Javascript 为 React 组件编写代码，但这并不实际。脸书的开发者本可以在 JSX 中开发更多的功能，但这些都只是些华而不实的东西。相反，React 试图让开发人员按原样使用 javascript，并鼓励他们发现和使用最佳实践。

这对 javascript 初学者来说也是个好消息。学习 React 的开发人员也可以学习普通的 Javascript，比学习 Angular + Typescript 的开发人员更加灵活。我自己一开始也学过 Angular，后来改用 React，所以我知道我在说什么。

# 逻辑完全是你的责任

正如我之前所说，React 不关心任何不渲染视图的东西。尽管事实上很多 React 应用都是和 Redux 一起构建的，但是没有人强迫开发者这么做。这只是在应用程序中构建数据流的一种便捷方式。

组件只需要获取数据作为道具展示。你在哪里找到它的？不关他们的事。

# 它是陈述性的

至此，你可以认为。

> 如果 React 这么小，做的这么少，为什么每个人都认为它很伟大？

是的，react 虽小但功能强大。但是它的力量不是来自许多特性，而是来自伟大的(乍一看相当复杂的)范例。其中之一就是**声明式编程**。

简而言之，在声明性范例中，你描述的是程序应该做什么，而不是 T2 如何做。开发人员在 React 中描述组件视图的方式是声明性的。你**宣布**一个组件应该如何使用每一套可能的道具。

例如，让我们考虑一个用纯 Javascript 和 React 构建的简单的类似复选框的组件。

纯 javascript 版本看起来会像这样

```
<button onclick="check()">Check</button>

<p>the flag is <span id="not">not</span> checked</p>

<script type="text/javascript">
  var checked = false;
  var notEl = document.getElementById('not');

  function check() {
    checked = !checked;
    notEl.innerText = checked ? '' : 'not';
  }
</script> 
```

Enter fullscreen mode Exit fullscreen mode

这里你应该直接**变异**DOM。那有什么问题？没有，如果只有一个节点变异。但是当应用程序变大时，通常会有一个以上的突变，这就是事情变得棘手的时候。DOM 突变很难维护、调试和测试。React 正试图解决这个问题(根本不允许突变)。

下面是它在 react 中的样子。

```
import React from 'react';
import ReactDOM from 'react-dom';

const KindaCheckbox = ({ onCheck, isChecked }) => (
  <button onClick={onCheck}>Check</button>

  <p>the flag is { isChecked ? '' : 'not' } checked</p>
)

// these 2 lines are not best practices don't write code like that
let isChecked = false;
const onCheck = () => { isChecked = !isChecked };

ReactDOM.render(
  <KindaCheckbox onCheck={onCheck} isChecked={isChecked} />, 
  document.getElementById('root')
) 
```

Enter fullscreen mode Exit fullscreen mode

这看起来有点复杂，但是核心思想是你描述你的视图在任何时刻应该是什么样子，不管这个标志是否被选中。换句话说，你声明**什么**来显示**如何随着时间的推移**到**如何变异**。现在您可以专注于处理数据，而不是 DOM。

这也是为什么开发人员非常喜欢它的原因。编程不是在网页上移动 div，而是处理数据！React 让您忘记维护 DOM，只做自己的工作。

* * *

# 包装完毕

在我看来，这些是你在使用 React 之前(或同时)需要考虑的最重要的因素。我希望这些能帮助你解决 React 陡峭的学习曲线，并使用它构建优秀的应用程序。

* * *

坦白地说，我本来打算再写 3-4 个要点，但是看起来这篇文章越来越大了。我在考虑第二部分。如果你认为这是一个好主意，给我留言或在 Twitter 上留言。