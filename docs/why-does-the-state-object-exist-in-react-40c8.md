# 为什么状态对象存在于 React 中？

> 原文：<https://dev.to/horia141/why-does-the-state-object-exist-in-react-40c8>

使用`@depiction`询问 StackOverflow [为什么状态对象存在于 React 中？](https://stackoverflow.com/questions/48869892/why-does-state-object-exist-in-react/48870135#48870031)。我已经对这个问题提供了一个较大的答案，所以我想我也应该把它加到博客上。我的回答如下:

首先，状态通常用于保存组件本地的一些内容，这些内容可以通过用户操作或来自服务器的推送更新等进行更改。-类似于复选框是否被选中，或者文本框中的输入值。关键词是它是组件的局部，没有理由让它成为“类的根”(无论这意味着什么)。在此基础上还有额外的构造，如 redux/flux 等，这些构造更具全局性，但对于常规和小规模的 React 来说并不是必需的。

对于`setState`的用法也可以这么说——这是一个设计决定。这是不需要的，React 可能使用与 Angular 相同的方法，即扫描某些应用程序级事件的组件状态字段变化。这甚至会更容易，因为所有被认为是“状态”的都被放在了`state`字段中，所有影响渲染的都在`state`或`props`(可能还有`context`)中。但是我发现非常明确的`setState`方法更合理——状态改变和渲染触发的点比 Angular 的“未来某个时候”/magic 方法定义得更好。

一个相关的事情是，在 React 中只有单向数据流。基本上`DOM = f(State, Props)`。并且对状态的任何改变必须是明确的。所以对于一个`<input>`元素，你需要提供一个`value`属性，以及一个`onChange`属性。后者是一个在改变时调用的函数，它将在某个时候`setState`改变输入到`value`属性的状态字段(作为渲染的一部分)。与 Angular 相比，AFAIK 只需提供值，输入和状态之间就会有双向数据流。它在局部看起来更好，但是在组成组件时很难处理——以至于我在 Angular 中最常用的处理模式基本上就是 React 正在做的。同样，设计约束让你写更多的代码，但也提供了一个更理智的开发体验。

总的来说，React 比 Angular，甚至 jQuery 或 plain-ol-javascript 有更严格的 UI 构建方法。对一些人来说这是额外的奖励，对另一些人来说这很烦人。这三个我都用过，我会选择 React 来完成所有未来的工作。