# React 中设置状态的必要指南

> 原文：<https://dev.to/bnevilleoneill/an-imperative-guide-to-setstate-in-react-3j6a>

[![](../Images/545c17e207af529f0b4f59c6f327777c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qTokiiBF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AJLfnJyOTlxwknAB9oQ4FFg.png)

与道具一样，React 中的状态是用于存储数据并影响组件呈现或行为方式的对象。与 props 不同，状态完全在组件中管理，并且可以随着时间的推移而改变。

React 组件的状态变化可以是来自用户动作、网络活动、API 请求或特定应用行为的触发的结果。

有状态的组件被称为**有状态组件**，而没有状态的组件被称为**无状态组件**。

一个组件可以有一个初始状态集，访问它并更新它:

```
import React, { Component } from 'react';

class Food extends Component {
  constructor(props) {
    super(props)

    this.state = {
      fruits: ['apple', 'orange'],
      count: 0
    }
  }
} 
```

在上面的代码块中，我们设置了组件的初始状态。这是通过构造函数方法完成的。

如前所述，状态是普通的 Javascript 对象，这就是为什么 ***this.state*** 应该等于一个对象:

```
this.state = {
  fruits: ['apple', 'orange'],
  count: 0
} 
```

[![](../Images/2063f22e152ef36aa267c13ae9842601.png)T2】](https://logrocket.com/signup/)

#### 访问组件状态

**组件状态**可以像其他对象一样使用 this.state.property_name 进行访问。

要访问上面示例中的计数，我们可以通过 this.state.count :
来完成

```
render() {
  return (
    <div className = "container">
      <h2> Hello!!!</h2>
      <p> I have {this.state.count} fruit(s)</p>
    </div>
  );
} 
```

#### 更新组件状态

尽管从代码中的任何地方写入 this.state 在技术上是可能的，但它不会提示重新呈现，并且当您试图通过 this.state 访问值时，这将导致不稳定和不正确的状态值。

您应该直接写入 this.state 的唯一位置是组件的构造函数方法。

在其他地方使用 thesetState()方法，这样做将接受一个对象，该对象最终将合并到组件的现有状态中。

例如，这不会重新渲染组件:

```
// Wrong
this.state.name = 'Obaseki Nosa'; 
```

请改用 setState()。

#### 引入 setState()

setState()调度对组件状态对象的更改，并告诉 React 该组件及其子组件需要用更新后的状态重新呈现:

```
// Correct
this.setState({name: 'Obaseki Nosa'}); 
```

React 有意“等待”直到所有组件在它们的事件处理程序中调用 setState()，然后才开始重新呈现。这通过避免不必要的重新渲染来提高性能。

要知道 setState()可以被认为是一个更新组件的请求，而不是一个立即的命令。

这就是为什么试图在 setState()之后立即使用 this.state 会导致不正确的行为:

```
// Trying to change the value of this.state.count from previous example
this.setState({
  count: 4
});

console.log(this.state.count); // 0 
```

this.state.count 返回 0，因为即使已经用 setState()设置了该值，它也只是在尝试使用 this.state 的值之前被调度和重新呈现。

setState()将始终导致重新呈现，除非 shouldComponentUpdate()返回 false。

#### 在 React 生命周期方法中使用 setState()

在 React 的生命周期方法中调用 setState()需要一定程度的谨慎。有一些方法调用 setState()会导致不希望的结果，还有一些方法应该完全避免。

#### 渲染()

在这里调用 setState()使您的组件成为产生无限循环的竞争者。

render()函数应该是纯的，这意味着它不修改组件状态，每次调用它都返回相同的结果，并且它不直接与浏览器交互。

在这种情况下，请避免在这里使用 setState()。

#### 构造函数()

不应在构造函数()中调用 setState()。相反，如果您的组件需要使用本地状态，请在构造函数中将初始状态直接分配给 this.state。

#### componentidmount()

组件挂载后会立即调用 componentDidMount()。您可以在 componentDidMount()中立即调用 setState()。它将触发一次额外的渲染，但这将发生在浏览器更新屏幕之前，因此 render()将被调用两次。

#### componentDidUpdate()

componentDidUpdate()在更新发生后立即被调用。你**可以在这里立即调用****setState()**，但是要知道**必须用条件**包装，就像下面的例子，否则你会导致一个无限循环:

```
componentDidUpdate(prevProps, prevState) {
  let newName = 'Obaseki Nosa'
  // Don't forget to compare states
  if (prevState && prevState.name !== newName) {
    this.setState({name: newName});
  }
} 
```

#### componentWillUnmount()

你**不应该在这里调用** **setState()** ，因为组件永远不会被重新渲染。一旦组件实例被卸载，它将永远不会被再次装载。

#### 结论

1.  setState()是异步的，这意味着如果我们试图立即访问该值，就不能保证状态已经被更新
2.  你只能用 setState 改变状态，而 **React 将对这个改变做出反应**😉
3.  避免将状态对象与其他实例属性混淆。很容易假设您可以在构造函数中定义另一个对象，并尝试像使用 state 一样使用它，但是 state 实例是一个特殊的实例，因为 React 将管理它:

```
...
//constructor function above

this.state = {
  fruits: ['apple', 'orange'],
  count: 0
}

this.user = {
  name: 'Obaseki Nosa'
}

... 
```

虽然 this.state 和 this.user 都是在构造函数中初始化的对象，但是只有 this.state 与 setState()反应，并由 React 管理。

干杯！！！

* * *

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](../Images/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *

React 中 setState 命令式指南的帖子[最先出现在](https://blog.logrocket.com/an-imperative-guide-to-setstate-in-react-b837ceaf8304/)[日志博客](https://blog.logrocket.com)上。