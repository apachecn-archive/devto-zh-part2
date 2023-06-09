# 为什么 React 社区忽略了关于 Web 组件的要点

> 原文：<https://dev.to/ben/why-the-react-community-is-missing-the-point-about-web-components-1ic3>

我是一名多面手，在一些图书馆工作过。我不认为自己是任何技术方法的纯粹主义者或传播者，尤其是在前端。也就是说，我并不觉得自己在 Web 组件大战中有狗，但是我觉得这个讨论很有意思。

在最近一次关于技术趋势的对话中，我和 [@bennypowers](https://dev.to/bennypowers) 谈到了围绕 web 组件的对话，以及来自 Web 社区的反馈。不管你在这个问题上的立场如何，我发现这个评论非常值得一读。

[![bennypowers profile image](img/da89de13ba506ff0679d92d07166a62a.png) ](/bennypowers) [ Benny Powers 🇮🇱🇨🇦 ](/bennypowers) • [<time datetime="2018-11-03T17:51:10Z" class="date-short-year"> Nov 3 '18 </time> • Edited on <time datetime="2018-11-03T18:03:41Z" class="hidden m:inline-block date-no-year">Nov 3</time>](https://dev.to/bennypowers/comment/6hl2) 

依我拙见，react 社区对他们的库投资太大了。鉴于他们图书馆的巨大成功，我可以理解，但我确实认为泡沫最终会破裂。明智的开发人员将开始改进他们的定制元素，并应该在他们的下一个项目中考虑它们。

## React vs. Web Components 是一个伪二分法。

因为 web 组件是浏览器标准，所以它们在 react 组件中与`div`一样可用。由于 react 与 DOM 的工作方式不同，围绕自定义事件有一些小的注意事项，但是在这方面有一个常用的解决 react 古怪之处的方法。顺便说一下，Preact 在很大程度上减轻了这些警告。
虽然我认为 web 组件在技术上优于 React 组件，因为它们由 web 浏览器原生支持，而不需要开发人员与浏览器斗争，但实际上两者并不矛盾。

## React 是明天的 MooTools

React 现在很受欢迎，这是事实，但情况可能并不总是如此。`<span>`不会去任何地方，`querySelector`也不会，但是我们已经看到许多库框架半途而废。与此同时，web 组件由于其明显的面向未来的优势，以及浏览器和库之间的互操作性，已经在企业世界中获得了牵引力。

## 今天，Web 组件得到了广泛的支持

现在 Firefox 支持 Shadow DOM 和自定义元素，Edge 团队也宣布了他们的发布意图，web 组件标准已经真正到来，并且是该平台的一个引人注目的特性。我不认为这是一个巧合，在过去的一个月里，我们在社交媒体上看到了很多关于 WC 的报道。现在标准已经落地，对全 js 组件模型来说，这是一个更加隐现的威胁。

## 围绕 WC 的 FUD 是没道理的

许多反对 web 组件的争论都可以归结为

*   这些标准不被接受
*   他们很难共事
*   他们做不到 react 所做的

但这根本不是真的。现已废弃的 v0 标准不再受支持，它们已被 v1 标准取代，正如我们所见，v1 标准得到了广泛支持。虽然标准所描述的低级 API 可能会很麻烦，但像 hybridsjs 或 lit-element 这样的库和基类只需稍微增加 js 页面的加载大小就可以克服这些障碍。
就特性而言，web 组件库可以做 react 能做的一切，甚至更多，而没有 VDOM 开销或抽象带来的认知和工具复杂性——Web 组件开发工具*是*浏览器/dom 开发工具。

## 一件轶事

我有一个朋友在一家浏览器厂商工作。几年前，他的团队忙于使用 react.js 构建浏览器 ui 和功能。即使他称赞单向数据流与中央存储架构的优雅(现在可以轻松地通过定制元素基类(如 GluonElement 或 LitElement)和状态容器(如 redux 等)实现)，他也抱怨 React JS 库的性能限制(用他的话说)。我建议他检查一下 web 组件，因为有了它们，性能限制将是浏览器本身，而不是 JS 库。他给一个同事发了一条即时消息。几年后，你瞧，同一家浏览器厂商目前正在用他们自己的基于 web 组件的库重写他们的 UI 组件。

## 总结

React 给 web 开发社区带来了很多美好的东西。

*   用户界面的功能方法
*   单向数据流模式
*   推广组件模型

但这也带来了很多负担。

*   FUD 直接与 DOM 打交道
*   笨重且不必要的工具
*   普遍的误解是 react 是在 web 上实现基于组件的 ui 的唯一方式

如今，除了浏览器和文本编辑器之外，您可以在没有任何浏览器标志、构建步骤、捆绑或工具的情况下构建模块化的、基于组件的前端应用程序。它可以在所有主流浏览器上运行。

对我来说很清楚，未来将建立在开放标准而不是定制实现的基础上。无论 React 为网络社区做了多少好事(我希望我清楚地认识到这一点)，它都无法与之竞争。

如果你想学习更多关于 Web 组件的知识，Benny 是少数几个发布了关于这个主题的教程的 DEV 成员之一。

[![bennypowers](img/41a155870eae4885566582be875341bb.png)](/bennypowers) [## 让我们构建 Web 组件！第 1 部分:标准

### 本尼为🇮🇱🇨🇦供电 9 月 18 日 1810 分钟阅读

#webcomponents #customelements #javascript #html](/bennypowers/lets-build-web-components-part-1-the-standards-3e85)[![thepassle](img/672cee74f5eea90a7456b606b9fccbb1.png)](/thepassle) [## Web 组件:从零到英雄

### Pascal Schilp Oct 28 ' 18 16min read

#javascript #webcomponents](/thepassle/web-components-from-zero-to-hero-4n4m)

快乐编码