# 创建多根 Vue.js 组件

> 原文：<https://dev.to/kball/creating-multi-root-vuejs-components-23j0>

Vue.js 等基于组件的框架中的一个常见约束是每个组件必须有一个根元素。这意味着一个特定组件中的所有东西都必须从一个元素派生出来，就像这样:

```
<template>
  <div> <!-- The root -->
    <span></span> <!-- now we can have siblings -->
    <span></span>
  </div>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

试着用这样的模板构建一个组件:

```
<template>
  <span></span> <!-- two siblings at the top level of the hierarchy! -->
  <span></span>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

您将得到可怕的错误:`Component template should contain exactly one root element. If you are using v-if on multiple elements, use v-else-if to chain them instead.`

在绝大多数情况下，这种约束不会引起任何问题。有两个必须在一起的元素。简单地在 DOM 层次结构中添加另一层，并将它们包装在一个 div 中。没问题。

然而，在某些情况下，你不能简单地添加一个额外的层次结构层，这种情况下 DOM 的结构非常重要。例如——我最近有一个项目，我有两个`<td>`元素，它们总是必须紧挨着。包括一个，你必须包括另一个。从逻辑上讲，它们是一个单独的组件，但是我不能把它们包装在一个包装器中，因为`<td>`元素需要是`<tr>`的直接后代才能正常工作。

## 解决方案:功能组件

这个问题的解决方案在于 Vue.js 的一个实现细节，Vue 目前不能支持多根组件的关键原因在于模板渲染机制——一个组件的模板被解析成一棵抽象语法树(AST)，而一棵 AST 需要一个根！

如果避开模板渲染，就可以避开单根限制。

它不太常用，但是完全有可能在没有模板的情况下实现 Vue.js 组件，只需定义一个`render`函数。这些组件被称为功能组件，可以用于无数的目的，包括在单个组件中呈现多个根。

## 代码

为了简单起见，我将每个成对的`<td>`元素编写成它自己的单个文件组件，然后简单地将它们包装在一个功能组件中，这个功能组件将 props 传递给它们两个。

```
/* paired-cell.js */
import FirstCell from '~/components/paired-cell/first-cell';
import SecondCell from '~/components/paired-cell/second-cell';

export default {
  functional: true,
  props: ['person', 'place', 'thing'],
  render(createElement, context) {
    const first = createElement(FirstCell, { props: context.props });
    const second = createElement(SecondCell, { props: context.props });

    return [first, second];
  },
}; 
```

Enter fullscreen mode Exit fullscreen mode

`FirstCell`和`SecondCell`是标准的 Vue 单文件组件，每个组件都有一个`<td>`元素作为根。但是 PairedCell 不同——它是一个纯 JavaScript 文件，导出一个功能组件。

功能组件和传统组件有两个主要区别。

1.  功能组件是无状态的(它们不包含自己的`data`，因此它们的输出完全由传入的属性定义。
2.  功能组件是*无实例的*，这意味着没有`this`上下文，而是通过`context`对象传递属性和相关值。

看看代码在做什么，它声明组件是功能性的，声明一组可接受的道具(一个人、一个地方和一件东西)，并定义一个带两个参数的`render`函数:`createElement`和`context`。

这两个参数将由 Vue 提供。`createElement`是一个在 Vue 的虚拟 DOM 中设置元素的函数。您可以直接将元素属性传递给它，但是在这种情况下，我只是用它来呈现子组件。

第二个参数包含组件的上下文；在这个例子中，我们唯一关心的是我们传递的`props`,但是它也包含了像子元素、插槽、父元素等等——实现一个组件可能需要的所有东西。

为了分解我们正在做的事情，我们实现了一个组件，该组件接受一组道具，将两个子组件呈现为兄弟组件，并将它们作为数组返回。呜！一个多根组件！

* * *

又及——如果你对这类话题感兴趣，我会发送一份名为“[星期五前端](https://zendev.com/friday-frontend.html)”的每周时事通讯。每周五我会发出 15 个链接，链接到 CSS/SCSS、JavaScript 和其他各种精彩的前端新闻中的最佳文章、教程和公告。在这里报名:【https://zendev.com/friday-frontend.html T2】