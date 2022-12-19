# React 中默认属性的完整指南

> 原文：<https://dev.to/bnevilleoneill/a-complete-guide-to-default-props-in-react-1o8o>

#### 了解关于为不同风格的 React 组件设置默认属性的所有信息。

[![](../Images/c63db3822727f891205865e70bcf4705.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EvKg0vqJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AxTSb8MHuCUfj_Prce0Hueg.jpeg)

**React** 是一个非常强大的基于组件的 JavaScript 框架，用于构建可在不同平台上运行的可扩展应用程序——服务器、web、移动、桌面等。如今，在这些平台上运行的数千个应用程序都是基于 React 构建的。

React 的一些惊人特性包括:*精益框架*、*虚拟 DOM* 、 *JSX 支持*、*代码可重用性*。你可以从这个文档中了解更多关于 React 的信息。

本指南在非常基础的层面上，试图揭示您需要知道的关于为 React 组件设置默认属性的所有内容。它主要面向 React 框架的新手。因此，它需要一些 React 的基础知识。

然而，使用 React 已经有一段时间的开发人员很可能仍然会发现本指南的某些部分很有见地。

*本指南中的截图显示了使用一些基本的* [*引导 4*](https://getbootstrap.com) *CSS 样式渲染的视图。为了获得非常相似的结果，您将不得不使用一些额外的引导样式来运行代码片段。*

[![](../Images/2063f22e152ef36aa267c13ae9842601.png)T2】](https://logrocket.com/signup/)

### 你好，反应过来的分量

React 应用程序通常由几个独立的组件组成，这些组件构成了应用程序的 UI。React 组件是任何 React 应用程序的构建块。

React 组件只是一个 JavaScript 函数，它接受一个称为 ***props*** 的任意输入对象，并返回 React 元素来描述应该在 UI 上呈现什么。

```
// Simple React Component
function ReactHeader(props) {
  return <h1>React {props.version} Documentation</h1>
} 
```

这段代码片段定义了一个非常简单的 ReactHeader 组件，该组件呈现一个包含指定 React 版本文档标题的`<h1>`元素。它使用 **JSX (JavaScript XML)语法**以声明的方式创建组件的 DOM 元素层次结构。你可以在这里了解更多关于使用 [JSX 和 React 的信息。](https://reactjs.org/docs/introducing-jsx.html)

如果没有 JSX，前面的代码片段会写成这样:

```
// Simple React Component (without JSX)
function ReactHeader(props) {
  return React.createElement('h1', null, `React ${props.version} Documentation`);
} 
```

使用 React 不需要 JSX。例如，如果您打算在没有任何形式的编译的情况下使用 React，那么 JSX 就不是合适的选择。

事实上，React 组件中的每一个 JSX 在组件呈现之前都会被编译成它的 **createElement** 等价物。然而，在本指南中，JSX 将在所有代码片段中尽可能使用。

从前面的代码片段可以清楚地看出，ReactHeader 组件要求向它传递一个版本属性。

ReactHeader 组件可以呈现在 DOM 上(在任意

element) as follows:

```
// Render a React Component
ReactDOM.render(, document.getElementById('root')); 
```

请注意，ReactHeader 的版本属性设置为 16。目前，ReactHeader 组件中的一切似乎都工作正常，如下图所示。

[![](../Images/e1c3cda7c6be62182084228f2cf54855.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4ImHI3f_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2ALspg0s2QB5pgdHhp.png)

<figcaption>react ader 组件</figcaption>

### 默认道具

**版本道具不通过会怎样？**

```
// Render the ReactHeader Component
ReactDOM.render(, document.getElementById('root')); 
```

[![](../Images/7adeb2e9d742a29b231bbd54b64c1674.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D3G0TOeh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AREmjO3vBUL7tHrSD.png)

<figcaption>react ader 组件无版本</figcaption>

你可能已经猜对了。下面是在没有版本属性的情况下呈现 ReactHeader 组件时发生的情况:

由于没有传递版本 prop，组件中对 props.version 的引用是未定义的，因此出现了上面的截图。

处理这个问题的一种方法是应用**条件渲染**。您可以阻止组件在所需的属性没有被传递或无效时进行渲染，而不进行任何渲染，如下面的代码片段所示:

```
// Simple React Component
function ReactHeader(props) {
  return (
    Number.isFinite(props.version)
      ? <h1>React {props.version} Documentation</h1>
      : null
  );
} 
```

另一种解决方法是为组件设置默认属性。您可以稍微调整一下组件，以便在版本属性没有被传递时使用默认值。

就是这里:

```
// With JSX
function ReactHeader(props) {
  return <h1>React {props.version || 16} Documentation</h1>
}

// OR
// Without JSX
function ReactHeader(props) {
  return React.createElement('h1', null, `React ${props.version || 16} Documentation`);
} 
```

在这里，逻辑 OR (||)操作符用于在版本属性未被传递时设置一个回退值。版本属性的默认值为 16。有了这个改变，现在一切都按预期工作了。

在本指南中，您将看到为不同风格的 React 组件设置默认属性的不同方式:

1.  使用 React.createClass() API

2.  类别组件

3.  功能组件

4.  使用高阶组件

### 反应。createClass()

在 React 中，类最适合于构建有状态组件，在这种情况下，您需要在组件内部维护状态，或者在您希望利用组件的生命周期方法的情况下。

React 最初发布时，类还没有真正成为 JavaScript 中的一个东西。因此，实际上没有办法用 JavaScript 创建类。

然而，React 提供了用于创建类组件的 React.createClass() API。随着时间的推移，这个 API 被弃用，并最终从 React 中移除以支持 ES6 类。

如果您使用的是早于 **15.5.0** 的 React 版本，那么您可以使用 **React.createClass()** API 创建一个简单的 React 组件，如下所示:

```
import React from 'react';

/**
 * ThemedButton Component
 * Using React.createClass()
 *
 * Renders a Bootstrap themed button element.
 */

const ThemedButton = React.createClass({

  // Component display name
  displayName: 'ThemedButton',

  // render() method
  render() {
    const { theme, label, ...props } = this.props;
    return { label }
  }

}); 
```

该代码片段使用 React.createClass() API 创建了一个非常简单的 ThemedButton 组件。这个组件主要根据传递给它的道具来呈现一个引导主题按钮。

另外，请注意，它需要传递一个主题属性和一个标签属性来正确呈现按钮。

一组主题按钮现在可以在 React 应用程序上呈现如下:

```
import React from 'react';
import ReactDOM from 'react-dom';

// [...ThemedButton component here]

function App(props) {
  return (
    <div>
      <ThemedButton theme="danger" label="Delete Item" />
      <ThemedButton theme="primary" label="Create Item" />
      <ThemedButton theme="success" label="Update Item" />
      <ThemedButton theme="warning" label="Add to Cart" />
      <ThemedButton />
    </div>
  );
}

const rootElement = document.getElementById('root');
ReactDOM.render(<App />, rootElement); 
```

请注意，ThemedButton 组件在应用程序中已经呈现了五次。第五次，ThemedButton 没有传递任何道具。下面是该应用程序的屏幕截图:

[![](../Images/8e910edceb2d8e9ff7fad0e7adc0a896.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--G2gsybtY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AKzv_kxjrGGbH5Kzp.png) 

<figcaption>主题按钮 App</figcaption>

从上面的截图中，您会注意到第五个按钮在视图中没有显示出来，因为它没有主题和标签属性。因此，需要为 ThemedButton 组件设置默认属性。

对于使用 **React.createClass()** API 创建的组件，可以通过向对象文字添加一个名为 **getDefaultProps** 的方法来设置默认属性。

**getDefaultProps()** 方法应该返回一个对象，表示组件的默认属性集。这就是:

```
const ThemedButton = React.createClass({

  // Component display name
  displayName: 'ThemedButton',

  // render() method
  render() {
    const { theme, label, ...props } = this.props;
    return <button className={`btn btn-${theme}`} {...props}>{ label }</button>
  },

  // Set default props
  getDefaultProps() {
    return {
      theme: "secondary",
      label: "Button Text"
    };
  }

}) 
```

在此代码片段中，已经为 ThemedButton 组件设置了默认属性。主题道具如果不通过默认为“次要”，标签道具默认为“按钮文本”。

使用默认的道具设置，应用程序现在应该看起来像下面的截图:

[![](../Images/7d25d042f7285fc0b1d8fe200602c87b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--w2ZGVQzg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AyEHi9BqDrnODePbY.png) 

<figcaption>带有默认道具的主题按钮</figcaption>

### 类组件

在 React 的最新版本中，可以通过利用 ***ES6 类语法*** 来创建类组件。这是使用 ES6 类语法的 ThemedButton 组件的外观。

```
import React, { Component } from 'react';

class ThemedButton extends Component {

  // render() method
  render() {
    const { theme, label, ...props } = this.props;
    return <button className={`btn btn-${theme}`} {...props}>{ label }</button>
  }

} 
```

对于使用 ES6 类语法创建的 React 组件，可以通过向组件类添加名为 **defaultProps** 的 ***静态*** 属性来设置默认属性。

**defaultProps** 静态属性应该被设置为表示组件的默认属性的对象。

这可以通过在组件类本身上定义 **defaultProps** 来实现，在类体之外，如下面的代码片段所示:

```
class ThemedButton extends React.Component {
  render() {
    // ...implement render method
  }
}

// Set default props
ThemedButton.defaultProps = {
  theme: "secondary",
  label: "Button Text"
}; 
```

通过将 ***静态类属性和方法*** 添加到 ECMAScript 规范中，您可以选择指定**默认属性**，如下面的代码片段所示:

```
class ThemedButton extends React.Component {
  render() {
    // ...implement render method
  }

  // Set default props
  static defaultProps = {
    theme: "secondary",
    label: "Button Text"
  }
} 
```

### 功能组件

在 React 中，函数语法适用于只呈现元素而不跟踪其状态或生命周期的组件。这些组件通常被称为 ***功能组件*** 或 ***无状态功能组件*** 。

下面是将 ThemedButton 组件重写为无状态功能组件时的样子:

```
import React from 'react';

function ThemedButton(props) {
  const { theme, label, ...restProps } = props;
  return <button className={`btn btn-${theme}`} {...restProps}>{ label }</button>
} 
```

与类组件一样，通过向组件函数本身添加一个名为 **defaultProps** 的 ***静态*** 属性，可以在功能组件上设置默认属性。

```
function ThemedButton(props) {
  // ...render component
}

// Set default props
ThemedButton.defaultProps = {
  theme: "secondary",
  label: "Button Text"
}; 
```

或者，使用 ***ES6 对象析构语法*** ，可以用默认值析构功能组件的属性。*你可以从* [***这篇文章***](https://codeburst.io/es6-destructuring-the-complete-guide-7f842d08b98f) ***中了解更多 ES6 析构。*T15】**

下面是带有析构属性的 ThemedButton 组件的外观:

```
import React from 'react';

// METHOD 1:
// Default Props with destructuring
function ThemedButton(props) {
  const { theme = 'secondary', label = 'Button Text', ...restProps } = props;
  return <button className={`btn btn-${theme}`} {...restProps}>{ label }</button>
}

// METHOD 2:
// More compact destructured props
function ThemedButton({ theme = 'secondary', label = 'Button Text', ...restProps }) {
  return <button className={`btn btn-${theme}`} {...restProps}>{ label }</button>
} 
```

### 使用高阶元件

在 React 中，**高阶组件(HOC)** 基本上是一个函数，它将一个 React 组件作为其参数，并返回另一个 React 组件，通常是原始组件的增强。

高阶组件对于组件合成非常有用，有很多包提供可以与 React 组件一起使用的高阶组件——一个非常流行的包是 [**重组**](https://github.com/acdlite/recompose) 。

**Recompose** 是高阶组件的丰富集合，可以与 React 组件一起使用。它更像是反应过来的*洛达什*。你可以在本参考的[中了解更多关于**重组**提供的高阶组件和 API。](https://github.com/acdlite/recompose/blob/master/docs/API.md)

您可以运行下面的命令来安装**重组**作为您的项目的依赖:

```
npm install recompose --save 
```

**Recompose** 导出一个 **defaultProps** 函数，该函数返回一个高阶组件，该组件在传递给它的任何 React 组件上设置指定的默认属性，并返回修改后的 React 组件。

下面是如何使用来自 **recompose** 的 **defaultProps** 高阶组件重写 ThemedButton 组件。

```
import React from 'react';
import { defaultProps } from 'recompose';

// React Component
function ThemedButton(props) {
  const { theme, label, ...restProps } = props;
  return <button className={`btn btn-${theme}`} {...restProps}>{ label }</button>
}

// Default Props HOC
const withDefaultProps = defaultProps({
  theme: "secondary",
  label: "Button Text"
});

// Enhanced Component with default props
ThemedButton = withDefaultProps(ThemedButton); 
```

### 结论

使用默认道具可以给你的 React 组件带来很大的改进。在本指南中，您已经了解了为 React 应用程序中不同风格的 React 组件设置默认属性的几种方法。

#### 拍拍&跟着

如果你觉得这篇文章很有见地，如果你不介意的话，请随意鼓掌。

你也可以在 Medium ( [Glad Chinda](https://medium.com/u/ddcd0e9719e5) )上关注我，获取更多对你有帮助的有见地的文章。你也可以在推特上关注我( [@gladchinda](https://twitter.com/@gladchinda) )。

* * *

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](../Images/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *

关于 React 中默认道具的完整指南首先出现在[博客](https://blog.logrocket.com)上。