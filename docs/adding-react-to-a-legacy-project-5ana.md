# 将 React 添加到旧项目

> 原文：<https://dev.to/matthewbdaly/adding-react-to-a-legacy-project-5ana>

我目前正在做的项目是一个教科书般的例子，它说明了当一个项目使用 jQuery 时会发生什么，而它实际上应该使用一个适当的 Javascript 框架，或者它开始时只是使用 jQuery，然后变得不成比例。历史上，它只是在需要添加新功能时才工作，这也没有帮助，这意味着它不是重构代码库，而是复制和粘贴。结果，有许多重复的代码需要重构，还有大量可怕的 jQuery 代码。

当我第一次接手这个项目时，我将 Laravel Mix 集成到其中，这样我就有办法将一些常见的功能重构到单独的文件中，并在构建过程中需要它们，以及使用 ES6。然而，这只是第一步，因为它没有解决重复的样板代码被复制和粘贴的根本问题。我需要的是一个重构来使用一些更固执己见的东西。碰巧的是，我被要求向 admin 添加几个 modal，因为从重复代码的角度来看，modal 是 admin 中最糟糕的部分之一，所以它们是使用更合适的库实现的强有力的候选对象。

我看了几个选项:

*   我过去曾经非常成功地使用过 Angular 1，但是我真的不想使用一个正在被淘汰的框架，并且很难将其改造成一个遗留应用程序
*   Angular 2+得到了积极的维护，但是将它改造成一个遗留的应用程序还是很困难的。此外，对 TypeScript 的需求会使它成为问题。
*   Vue 是一种可能性，但对于这个用例来说，它做得有点太多了，而且还不清楚如何将其改造到现有的应用程序中

最终，我选择了 React.js，原因如下:

*   它在 Laravel Mix 中有一个预置，很容易上手。
*   它的目标非常有限——React 密切关注于视图层，只处理渲染和事件处理，因此它正好满足了我在这种情况下的需求。
*   它在遗留应用程序中有着良好的使用记录——毕竟，它是由脸书创建的，他们是增量式添加的。
*   测试很容易——Jest 的快照测试可以很容易地验证呈现的内容没有改变，使用 Enzyme 可以直接测试与组件的交互
*   高阶组件提供了一种在组件间共享功能的直接方式，我需要这种方式来允许不同的模态以相同的方式处理另一个模态。
*   通过为通用用户界面元素创建一系列组件，我可以在以后的工作中重用这些组件，从而节省时间和精力。

然而，我还不清楚如何将 React 集成到一个遗留应用程序中。最后，我想出了一个可行的方法。

通常，我会为我的应用程序创建一个单独的根，就像这样:

```
import React from 'react';
import ReactDOM from 'react-dom';
import App from './components/App';

ReactDOM.render(
    <App />,
    document.getElementById('root')
); 
```

Enter fullscreen mode Exit fullscreen mode

然而，这不是一个选项。现有的模型使用 jQuery 和 Bootstrap，而新的模型必须使用它们。因此，我只需要用 React 管理 UI 的某些部分，其余部分就不用动了。这里有一个我最后如何渲染模态的例子:

```
import React from 'react';
import ReactDOM from 'react-dom';
import higherOrderComponent from './components/higherOrderComponent';
import modalComponent from './components/modalComponent';

const Modal = higherOrderComponent(modalComponent);

window.componentWrapper = ReactDOM.render(
  <Modal />,
  document.getElementById('modalTarget')
);

window.componentWrapper.setState({
  foo: 'bar'
}); 
```

Enter fullscreen mode Exit fullscreen mode

通过将重复的功能提取到更高阶的组件中，我可以很容易地将新的模态包装在该组件中，并在模态之间共享该功能。然后，我可以在不同的目标元素中呈现每个组件，并将其赋给名称空间`window`中的一个变量。ID 为`modalTarget`的 div 需要被添加到适当的位置，但除此之外，HTML 不需要被修改，因为所需的标记在 React 组件中。

然后，当我需要改变组件状态中的一个值时，我可以调用`window.componentWrapper.setState({})`，传递要设置的值，这些值会像往常一样向下传播到子组件。我还可以在页面上呈现多个不同的模态组件，并分别引用每个组件来设置状态。

我不建议在一个新建项目中使用这种方法——状态不是你应该在这样的组件之外设置的东西，通常我不会这样做。然而，对于这个特殊的用例来说，这似乎是最简单的方法。随着时间的推移，我将移植越来越多的 UI 来作出反应，最终它将不再是必要的，因为我将把应用程序状态存储在 Redux 之类的东西中。