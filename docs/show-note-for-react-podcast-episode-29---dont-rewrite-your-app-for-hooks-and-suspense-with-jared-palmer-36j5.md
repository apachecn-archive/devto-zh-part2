# React 播客第 29 集的展示笔记——贾里德·帕尔默的“不要为了悬念重写你的应用”

> 原文：<https://dev.to/dance2die/show-note-for-react-podcast-episode-29---dont-rewrite-your-app-for-hooks-and-suspense-with-jared-palmer-36j5>

*照片由[凯·皮尔格](https://unsplash.com/photos/1k3vsv7iIIc?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在[Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)T5 上拍摄*

更多激动人心的功能将在 React 播客第 29 集中讨论。

[Chantastic](https://twitter.com/chantastic) 在 React Conf 2018 之前与[贾里德·帕尔默](https://palmer.net/)的演示讨论了钩子&悬疑特性。

**免责声明** : *下面是一个非官方的节目说明，突出了讨论，因为我不属于播客，只是一个热心的听众:)。*

## 📑目录

*   [挂钩](#hooks)
*   [悬念](#suspense)
*   [暂记+缓存](#suspense-cache)
*   [重复](#reiteration)
*   [下一个挑战](#next-challenge)
*   [Chantastic 的洞察力](#chantastics-insight)
*   [升级提示](#upgrade-tips)
*   [根本不要重写你的应用](#dont-rewrite-your-app-at-all)
*   [资源](#%F0%9F%8F%94-resources)

## ⚓钩

### 🔹从根本上取代了渲染道具

*   [渲染道具](https://reactjs.org/docs/render-props.html)可以导致一个[回调地狱](https://twitter.com/acdlite/status/955955121979969537)由链接多个渲染道具引起(在 node 中普遍存在)。
*   钩子使用普通的旧 JavaScript 函数解决了过度嵌套的代码。

### 🔹用 JavaScript 全力以赴是因为

1.  这很容易测试
2.  一切都在范围内

**宋注**:因为类组件会要求你使用`this` &来访问状态，所以很多新人苦于不知道用当前实例的`this`来绑定方法，比如`this.method.bind(this)`？_

### 🔹以打字打的文件

需要更少的代码

1.  因为它消除了为类组件的属性和状态创建接口的需要。
2.  并且使用初始值`useState` TypeScript 可以推断作为返回元组中的第一个参数返回的状态类型(帮助编辑器中的自动完成功能)。
3.  让各州之间的转移变得容易。

### 🔹其他好处

*   您可以使用简单的函数语法，而无需传输。
*   从头到尾阅读 [Hooks 文档](http://reactjs.org/hooks)不要跳过

### 🔹可能的缺点

学习曲线可能是一个问题，因为这是一个新功能，因此需要你记住“一堆东西”。

但是一旦你克服了障碍，这很好，因为

1.  您可以轻松地重构状态。
2.  您可以选择将新状态放入组件或其他地方。

Jared 对 Hooks 的未来持乐观态度，并认为这是 React 的下一个合理步骤。

## 🌉焦虑

### 🔹是关于什么的？

悬疑讲的是一种“资源的高效装载”。

### 🔹装载状态问题

在快速连接时，用户不喜欢看到一个奇怪的加载屏幕，但在慢速连接时，他们希望看到它。

在这种情况下，速度、性能和 UX(用户体验)直接相互冲突，因为

*   根据 UX 的一项研究，当变化发生在 200～400 毫秒时，用户感觉不到速度差异。
*   在此期间显示一个加载微调器会让用户觉得它是一个快速连接上的 janky，但会想知道当达到阈值时会发生什么。

什么都不显示仍然不能解决问题，因为您仍然需要检查加载状态。

人们做了许多尝试来解决这个问题(延迟加载数据、资产等)

，但它们仍然无法对(慢速/快速)网络做出反应。

### 🔹悬念是怎么运作的？

悬念允许您在其他组件等待资源时暂停渲染。

当资源到达时，React 可以继续呈现，否则悬念可以显示回退内容。

悬念实际上阻止了时间的发生。

### 🔹有什么帮助？

悬念使我们能够以声明的方式编排回退。

*   这使我们能够删除加载状态和强制检查。
*   您只需告诉 React 在哪里暂停/等待(DOM 突变在此阶段暂停)。

暂停可以使加载动态组件变得容易。

1.  我们可以使用 [React.lazy](https://reactjs.org/docs/code-splitting.html#reactlazy) 延迟加载组件，方法是暂停加载，告诉 React 暂停，直到组件被解析。
2.  如果花费的时间超过某个阈值，暂停显示一个回退。
3.  这解决了上面问题陈述中提到的速度、性能和 UX 问题。

### 🔹如何处理悬念错误

使用[误差边界](https://reactjs.org/docs/code-splitting.html#error-boundaries)。

### 🔹并发模式

先决条件是你的应用需要通过一个[严格模式](https://reactjs.org/docs/strict-mode.html)，方法是修复你使用`<React.StrictMode />`包装应用时产生的所有警告。

一旦你的应用通过了严格模式，你可以用 [ReactDOM.createRoot](https://github.com/sw-yx/fresh-concurrent-react/blob/master/apis/roots.md#reactdomcreateroot-and-rootrender) 替换 [ReactDOM.render](https://reactjs.org/docs/react-dom.html#render) ，你返回一个对象并调用 render。

### 🔹如果不能通过严格模式测试怎么办？

您仍然可以通过以下方式使用并发模式

1.  识别严格模式经过的反应树
2.  并在[反应中包裹住那部分树。并发模式](https://github.com/sw-yx/fresh-concurrent-react/blob/master/Intro.md#getting-started-with-concurrentmode)。

这有助于逐步采用。

### 🔹在并发模式之外？

如果在并发模式之外使用暂挂，当子组件仍在加载自身时，暂挂将立即回退。

您仍然可以获得 janks，但是代码更少，因为它摆脱了命令式代码中使用的加载状态。

在并发模式之外使用暂挂的好处是

1.  更好的 DX(开发者体验)
2.  它会慢慢地让你的应用为更好的 UX 做好准备。

## 🌉悬念+💲隐藏物

由于悬念的工作方式，当与缓存结合时，数据获取被简化了，因为一切(*并不清楚。杰瑞德是指国家吗？还是组件？*)始终被定义。

*当万物？定义了*)，TypeScript 将不需要分部类(*我需要一些反馈，因为我是 TS 新手…* )。

将钩子与悬念+缓存耦合起来，你可以像访问同步代码一样访问异步代码，从而提供更好的可读性。

这种组合还解决了组件状态依赖编排的[瀑布问题](https://www.quora.com/What-is-waterfall-problem)(当前一个组件被加载时，下一个组件需要等待的情况)。

### 🔹例子

单个悬念的兄弟并行运行。