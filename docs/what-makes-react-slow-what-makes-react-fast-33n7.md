# 什么使反应慢，什么使反应快

> 原文：<https://dev.to/rakshitsoral/what-makes-react-slow-what-makes-react-fast-33n7>

有多少次你听到你的开发同事尖叫…**“反应很快，但往往会使开发变慢”？**

5 次、10 次、100 次或者 1000 次！对吗？

好吧，我不是在开玩笑或咆哮这个框架。我在这里告诉你**真相**。

大多数开发人员发现难以达成一致的事实。

## 你需要知道的关于 DOM 的真相

DOM 操作是所谓的现代交互式 web 开发的核心。不幸的是，执行操作的速度非常慢。甚至比典型的 Javascript 操作还要慢。

众所周知，大多数 Javascript 框架都不必要地更新 DOM，而且比需要的更新更频繁，这使得我所说的缓慢变得更加糟糕。

例如，假设您有一个展示十个商品列表的电子商务应用程序。您核对了第一项，发现列表已经被**重建**。现在，那是 **10 倍**的多余工作！

React 的工程师找到了解决这个问题的方法。他们想出了一个叫做“虚拟 DOM”的东西。

## 进入虚拟 DOM - React

在 React 中，对于每一个 DOM，你都有一个相应的虚拟 DOM 对象，就像原始 DOM 对象的一个轻量级副本。DOM 对象和虚拟 DOM 对象具有相似的属性，只是更新内容的大小写不同。

一旦通过呈现 UI 组件更新了虚拟 DOM，React 就会将虚拟 DOM 与更新前拍摄的虚拟 DOM 快照进行比较。

通过这种方式，React 评估哪些虚拟 DOM 对象发生了变化。这个过程被称为“差异”。

在上面的例子中，React 将足够聪明地分析更改并重新构建第一个项目，从而不考虑列表的其余部分。

这给了 React 很大的速度提升，因为 React 只更新 DOM 中必要的部分。

## 关于 React 的虚拟 DOM 的脏部分

在 Javascript 中操作 DOM 很慢，在 React 中操作虚拟 DOM 甚至更快。这是连 React 社区都会同意的事情。现在，让我们讨论使优化反应变得困难的事实。

每当在 React 组件上调用 setState 时，React 都会将其标记为脏。在结束事件循环时，React 将查看这些脏组件并重新渲染它们。这恰好在 DOM 更新时发生一次。

[![React Diff Algo](../Images/4b1d3918b9bef5a967a8ef6516db8985.png "React Diff Algo")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--J9wNdiZT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o3qwv6w8yjfyag866gni.png)

在调用 SetState 时，React 为所有子组件重建虚拟 DOM。但问题是，如果你重新渲染根元素，你最终会渲染整个应用程序，这很难优化。

[![React Re-rendering of Tree](../Images/9ba6f1f418461ee0682b617d5c067f29.png "React Re-rendering of Tree")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WzaFFTj3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z6d5pw0cwrwp319sv949.png)

谢天谢地，有大量的优化技术可以让你的应用反应更快。有一本[关于让 React 应用更快的权威指南](https://www.simform.com/react-performance/)，详细介绍了优化 React 组件的细节。