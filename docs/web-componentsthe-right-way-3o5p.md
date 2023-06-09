# Web 组件——正确的方式。

> 原文：<https://dev.to/equinusocio/web-componentsthe-right-way-3o5p>

大约两年前，我们就听说过 **web 组件**，这篇文章不会解释它们是如何工作的(因为[有谷歌](http://bfy.tw/H9oY))，而是我们将讨论这项技术的目的，何时以及如何使用它。

我们可以把 web 组件看作是扩展 HTML 的工具，而不是取代它。可用的技术遵循我们都习惯了的规则——总是如此——在世界上最著名的语言标记中看到。

> Web Components 是一套不同的技术，允许您创建可重用的自定义元素，将它们的功能封装在代码的其余部分之外，并在您的 Web 应用程序中使用它们。
> — MDN

## HTML —加速课程

每个 web 开发人员都知道——或者至少应该知道 HTML 的基础知识，以及如何定义 web 元素的标记，这些元素将由浏览器以特定的方式和特定的行为来表示。

[![example1](img/5dac30ede2fc60b5e574331257c8c65f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Tg2m7hrU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A3hAL96Wb0tyLuSV40CslKQ.png)

在上面的例子中，像 HTML 元素的大部分一样，节点可以接受将由浏览器或 CSS 用户显示(和样式化)的内容。但是众所周知，它也存在不接受子节点的元素(众所周知的 void 元素)，例如`<img>`和`<input>`标签，以及其他只需要某些类型节点的标签。

与更多其他元素一样，`<select>`元素是一个使用影子 DOM 类似逻辑的节点；因此，如果您愿意，可以像查看 web 组件一样查看这些元素的内容:

[![enabling-shadow-dom](img/bb711d903afb5dcd435c9722e2ef97b3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QMJ-u0Sv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AAb6dzpZOFBra_jxexg8sQQ.gif)

这大概是这种语言之外的机制，所以我们可以定义两种不同的 HTML 元素模型:

*   **模块化元素** —允许子节点(普通元素)
*   **自结束元素** —不允许子节点(空元素)

## 自定义元素

正如我们所写的，超越 web 组件的技术旨在**扩展 HTML** ，而不是取代它。这是什么意思？我们有工具来创建新的 HTML 元素，如果不通过用户代理(浏览器)实现，这些元素将不会自然存在，或者通过添加[功能和自定义样式](https://developers.google.com/web/fundamentals/web-components/shadowdom#host)来扩展那些已经存在的元素的行为。

### 什么时候？

答案比你想象的要简单。当可用的 HTML 元素不能满足功能和设计需求时，定义新的 HTML 元素是必要的。

如果我们需要创建一个具有定制风格的新的`<my-button>`元素，我们必须考虑[渐进增强](https://en.wikipedia.org/wiki/Progressive_enhancement)。所以我们应该增加功能和风格，而不是从头开始重新创建元素。

## 走错了路

如果我们在互联网上冲浪，我们可以找到一些完全由自定义元素组成的*模式库*(使用或不使用一些框架，如[聚合物](https://www.polymer-project.org/))，*测试*，*示例*，*操场*…它们都有一个共同点，**它们都是使用错误的模式**创建的。你还记得吗？我们正在扩展 HTML，所以我们应该遵循它的范例和组成。以下是一些例子:

[![ex2](img/b33a49c331e5e19cf1443bab2cf4bf29.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ESe3CnVB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AfXvsGsqwR9QcP-E9jeXsxg.png) 
一整个 app 里面的影子根。和把 app 放在`<input>`标签里面是一样的。

[![ex3](img/ba74d5c363d156998bbad3ce53bda19e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sF7WVoE8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AvBXhxekSAeyj5U35AUkrCA.png) 
一个原生的`<button>`元素包裹着……另一个自定义的按钮里面。[和把一个`<button>`放在另一个`<button>`](https://inception.davepedu.com/) 里面是一样的。

[![ex4](img/f156d6897fffb500cc4b07f2d17e9625.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MAhNMe5P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AE-gG1wPhyjHbsrdABqlgKQ.png) 
这个形象真的需要描述吗？想想上面的`<select>`例子。

# 本文继续...

[https://equinusocio.dev/blog/web-components-the-right-way/](https://equinusocio.dev/blog/web-components-the-right-way/)