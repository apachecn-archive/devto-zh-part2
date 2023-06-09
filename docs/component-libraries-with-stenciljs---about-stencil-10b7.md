# Stencil.js 组件库-关于 Stencil

> 原文：<https://dev.to/johnbwoodruff/component-libraries-with-stenciljs---about-stencil-10b7>

*这是关于使用 Stencil.js 创建 web 组件库系列文章的第一篇*

我从 2012 年开始专业做前端开发。在那段时间里，我学习了一个又一个 JavaScript 框架。我从 [Backbone.js](http://backbonejs.org/) 开始，相当不喜欢。我敢肯定这在当时是一个很棒的框架，但直到今天我还把它当成骂人的话来用。早在 2012 年底，当我得知 [Angular.js](https://angularjs.org/) 的时候，我非常激动，并且用 Angular.js 在两天内用大约 1/8 的代码量重新编写了我们复杂的主干应用程序，这让所有人都感到震惊。

那个周末激发了我对学习新 JavaScript 框架的热爱，这种热爱从未离开过我。我密切关注了 Angular 的发展，它是 Angular.js 的继承者，也是 Angular . js 的完全重写版本，在我目前在 Domo 的工作中仍然大量使用 Angular。 [React](https://reactjs.org/) 也出现了，并带来了一个不同的范例，它不是一个包罗万象的“框架”，而是一个视图渲染库。我喜欢学习一种全新的构建应用程序的方式，除了 React 之外，还可以使用不同的库将我需要的各种组件连接在一起。我当然也尝试过许多其他框架，包括但不限于 [Ember.js](https://www.emberjs.com/) 、 [Vue](https://vuejs.org/) 和 [Aurelia](https://aurelia.io/) 。

## 组件库

我强烈推荐使用组件库。除非你是一个出色的设计师和开发者，否则对我们大多数人来说，它们会大大简化事情。然而，在我前面提到的所有框架实验中，我发现了一个主要的痛点。每当我使用一个新的框架时，我使用的组件库很可能有它自己的新框架的特定版本，或者根本没有它的版本。例如，我在工作和兼职项目中大量使用[角状材料](https://material.angular.io/)。然而，每当我在 React 应用程序上工作时，所有这些组件显然在 React 应用程序中不可用，因为它是特定于角度的。于是，我找到了最常用的 React 替代品， [Material-UI](https://material-ui.com/) 。虽然这是一个很棒的库，但我现在必须进行上下文切换，因为这些组件是由完全不同的个人、架构和术语构建的。然而，这是最好的情况，因为这是两个最广泛使用的框架和一个最广泛使用的设计系统。

许多公司现在使用他们的商业风格指南开源他们自己的组件库。比如 Atlassian 的 [Atlaskit](https://atlaskit.atlassian.com/) ，VMWare 的 [Clarity](https://vmware.github.io/clarity/) ，或者 IBM 的 [Carbon](http://www.carbondesignsystem.com/) 等库。特别是在 IBM 的例子中，他们不得不为 Angular 发布一个版本的库，为 React 发布一个版本的库，而另一个版本只包含 HTML 和 CSS 部分，类似于 [Bootstrap](https://getbootstrap.com/) 的工作方式。对于没有 IBM 资源的大多数企业来说，这是不可行或不可维护的。相反，许多人选择了一个框架并只发布了这个库。

## 进入 Stencil.js

在过去的几年里，我很少像对 [Stencil.js](https://stenciljs.com/) 一样对一项技术感到兴奋。它不是另一个框架；事实上，它根本不是一个框架。由 [Ionic](https://ionicframework.com/) 团队构建的 Stencil.js 采取了不同的方法。他们没有提供组件框架，而是构建了一个组件编译器，它采用现代工具和高级 API 构建的组件，如 [TypeScript](https://www.typescriptlang.org/) 、[decorator](https://www.typescriptlang.org/docs/handbook/decorators.html)和 [TSX](https://www.typescriptlang.org/docs/handbook/jsx.html) ，并将它们编译成符合标准的 [web 组件](https://www.webcomponents.org/)。运行这些组件不需要框架，它们使用本地浏览器 API 来呈现定制元素。

web 组件标准由各种 API 组成，这些 API 共同允许您呈现复杂的定制元素。虽然您完全可以自己编写这些程序，但它们是特意设计成低级 API 的。这就是我强烈推荐 Stencil 的原因，它从 Angular 和 React 中提取最佳部分来构建组件，并为您处理剩余的复杂工作，如动态填充缺失的功能和执行异步渲染。像 React 这样的库的所有好处，但是使用平台本身，这允许你在任何框架中使用这些组件或者根本不用框架。

## 一个基本的模板组件

让我们来看看一个基本的模板组件。

```
import { Component, h, Prop } from '@stencil/core';

@Component({
  tag: 'hello-world',
  styleUrl: 'hello-world.css'
})
export class HelloWorld {
  @Prop() name: string;

  render() {
    return (
      <p>
        Hello {this.name}!
      </p>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

你可以简单地在一个标准的 HTML 页面中使用这个组件，比如:

```
<!DOCTYPE html>
<html lang="en">
  <head>
  </head>
  <body>
    <hello-world name="John Woodruff"></hello-world>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

如果你熟悉 Angular，你会认出组件装饰器:

```
@Component({
  tag: 'hello-world',
  styleUrl: 'hello-world.css'
}) 
```

Enter fullscreen mode Exit fullscreen mode

这个装饰器允许您定义 HTML 标记名，以及一个到样式表的链接，该样式表将为这个组件提供 CSS。

在组件装饰器之后，您有了一个普通的 TypeScript 类。您会注意到类属性`name`上有一个`@Prop()`装饰器。这定义了一个公共属性，您可以在自定义元素上使用它来将一个字符串传递给`name`属性。

最后，我们有标准的`render()`方法。您返回 TSX (TypeScript XML ),它是将在自定义元素中呈现的标记。在这种情况下，我们只是传回一个带有消息的`<p>`标签。这个消息是“Hello ”,带有我们在道具上传递的名称。如果我们要更改传递给 prop 的名称，组件会检测到并相应地重新呈现。

## 为什么要蜡纸？

你，一个精明的读者，可能会问自己为什么我会选择模板，当我可以使用聚合物代替？答案是绝对欢迎你使用聚合物。这是一项伟大的技术，它帮助将 web 组件带到了 web 上。就我个人而言，我更喜欢用模板而不是聚合物来构建 web 组件，这仅仅是因为开发人员的经验和使用的工具。这与在 React 和 Angular 中构建组件的体验非常相似，这两种技术我都非常熟悉。

换句话说，模板编译的 web 组件是高性能的、小型的，并且非常容易构建和发布。您可以发布单个 web 组件，或者整个组件库。几个月来，我一直在工作中使用 Stencil 构建组件库。这种体验对我来说非常积极，我们现在有了一个共享组件库，可以在整个公司使用的各种框架中使用。

## 下一步

如果你已经做到了这一步，并且仍然有兴趣在这些帖子中与我一起构建一个组件库，感谢你的到来！这第一篇文章几乎没有实际的代码，因为我不想在你清楚地了解原因和好处之前就开始写。既然我们已经知道了，我们就在下一篇文章中再见吧！

*单纯想看最终结果回购？来看看[这里](https://github.com/johnbwoodruff/mountain-ui)T3】*