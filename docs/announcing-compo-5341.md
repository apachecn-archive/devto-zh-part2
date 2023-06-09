# 宣布:compo

> 原文：<https://dev.to/swiip/announcing-compo-5341>

#### 🎼组合 Web 组件

[**compo**](https://github.com/Swiip/compo) 是一个新的 [JavaScript](https://hackernoon.com/tagged/javascript) Web UI 库，基于纯 Web 组件提供了一个现代的 [API](https://hackernoon.com/tagged/api) ，使用函数组合来轻松定义和丰富它们。

是啊！这是又一个新的 JavaScript Web UI 库！很抱歉“JavaScript 疲劳”的人，但我仍然认为这个项目中有一些好想法，至少值得发表。

### 灵感

它受到许多流行库的启发，如 [React](https://reactjs.org/) 、 [Redux](https://redux.js.org/) 、 [Recompose](https://github.com/acdlite/recompose) 或 [styled-components](https://www.styled-components.com/) ，将现代概念和 API 应用于自定义元素标准。

前段时间由 **React** 引入的**虚拟 Dom** 概念已经被用来解放用户来管理 Dom 转换。这个概念可以应用到标准的 Web 组件世界中，每个组件都将其模板赋值给自己的影子 DOM。

**重新组合**引入了一个全功能组合 API，允许根据需要逐个增强组件特性。通过用所需的特性扩展定义类，它可以再次完美地匹配定制元素。

最后，在 JS 中使用 CSS 和 **styled-components** 有一段时间推动了将 CSS 视为 UI 库的一个组成部分，并以与标记相同的方式对待它。Shadow DOM CSS 作用域完美地完成了这项工作。

### **API**

在所有以前的灵感中，我感到遗憾的是使用许多不同的库来最终获得一个充满好想法但具有复杂的非同质 API 的奇怪怪物的复杂性。

因此, **compo** 是一个功能齐全的库，它提供了一个单一的一致的 API 来管理组件的所有方面:状态管理、标记、风格。

如题所言，选择的接口是函数组合。你用**组件**函数定义一个组件，第一个参数为名称，所有其他参数用于增强功能，如**带存储**、**带标记**或**带样式**。