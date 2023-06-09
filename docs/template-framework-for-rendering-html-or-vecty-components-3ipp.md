# 用于呈现 HTML 或 Vecty 组件的模板框架

> 原文：<https://dev.to/progrium/template-framework-for-rendering-html-or-vecty-components-3ipp>

在我创建我理想的 Go 前端 web 框架的过程中，我一直在试验一个与 [Vecty](https://github.com/gopherjs/vecty) 一起工作的模板系统。我最初构建了一个输出 HTML 原型，就像一个标准的模板引擎。然后，我将它用于输出 Vecty 对象，并获得了在浏览器中使用 Vecty 的概念证明。但是，我真的不想制作一个专门针对 Vecty 的模板系统，尤其是因为 Go 中没有太多优秀的模板系统。所以，我想让核心功能与输出 HTML 字符串或 Vecty 对象无关。这花了一些时间才弄明白，但我已经做到了。

当我在做的时候，我还添加了一个表情评估系统。大多数模板系统需要某种表达式求值，这导致他们发明一种新的微语言，并且不得不从头构建助手功能，或者慢慢地构建到宿主语言助手的绑定。因为从结构上来说，这个模板系统是受 Vue 模板的启发，Vue 模板是有效的 HTML，我们只需要一种方法来评估在指令属性和插值括号中使用的表达式。在 Vue 中，这只是 JavaScript，所以我想为什么不在这里使用 JavaScript。我找了一个现成的 Go JavaScript 解释器，做了一个接口，这样你不仅可以交换不同的 JavaScript 实现，还可以交换完全不同的语言运行时。

其结果是在使用和实施上都非常符合人体工程学。我们在 HTML 解析器的基础上构建了一个新的节点树，它允许我们编写自定义指令、自定义元素解析器，以及我提到的自定义表达式赋值器。所以我们真正拥有的是一个受 Vue 模板启发的模板系统框架。

既然我已经用 HTML 渲染器完成了这个工作，我将返回到构建 Vecty 渲染器，它有自己的基本指令实现。自定义元素让我包装 Vecty 如何处理组件，我只需要实现插槽和一些其他指令，我就可以开始用 Vecty 构建组件了。

[https://www.youtube.com/embed/hQ3pwaPjjEM](https://www.youtube.com/embed/hQ3pwaPjjEM)