# 什么是渲染道具？

> 原文：<https://dev.to/hirodeath/what-is-the-render-props-3nf8>

我猜你可能听说过渲染道具，因为 React team 在博客[中写了一篇这样的文章](https://reactjs.org/docs/render-props.html)。

这对我理解如何使用渲染道具真的很有帮助，尽管人们可能有点难以深入理解如何使用它。

所以这一次，我要简洁地描述如何使用渲染道具。

我将用来描述的示例代码是这里的[和](https://github.com/takahiro-saeki/react-new-api-examples)。

## 2 类渲染道具 API。

我认为有两种类型的 API 可以用来作为渲染道具。
第一种情况是**用它作为道具**中的道具。
看一下例子。

```
// src/containers/App/index.jsx

import React, { createContext } from 'react';
import List from '../../Components/List';

const App = () => (
  <List
    apiPath="//dog.ceo/api/breeds/list/all"
    render={
      ({list, isLoading}) => isLoading ? <div>now loading...</div> : <div>Done!</div>
    }/>
)

export default App; 
```

所以，正如你所看到的，你可以在道具中使用。
关键点在这里

```
render={
      ({list, isLoading}) => isLoading ? <div>now loading...</div> : <div>Done!</div>
    } 
```

如何使用像`list`和`isLoading`这样的论点？答案在下面！

```
// src/components/List/index.jsx
import React, { Component } from 'react';

export default class List extends Component {
  constructor(props) {
    super(props);
    this.state = {
      list: [],
      isLoading: false
    }
  }

  fetchApi = async () => {
    const res = await fetch(this.props.apiPath);
    const json = await res.json();

    await this.setState({
      list: json,
      isLoading: false,
    });
  }

  componentDidMount() {
    this.setState({ isLoading: true }, this.fetchApi);
  }

  render() {
    return this.props.render(this.state)
  }
} 
```

尤其是这一行。

```
 render() {
    return this.props.render(this.state)
  } 
```

所以，解决方法很简单，你需要在将要返回的返回方法中的`this.props.render()`中添加一个对象作为参数。
你必须记住`list`和`isLoading`参数是在`List`组件中声明。

如果你在`List`组件中添加了一些 key 和 property 状态，你可以用 property 作为一个来自 state 的参数。
当然，如果状态改变，参数 params 也会改变。

没有必要在`this.props.render()`函数中添加状态对象。但我认为你最好加上州名。你越是用它作为渲染道具`component`或者`utils`，你越会做。

## 童案

另一方面，你可以通过使用`this.props.children()`来使用其他类型的渲染道具！

让我们看看下面的例子。

```
// src/containers/ChildRender

import React, { Component } from 'react';
import Counter from '../../Components/Counter';

const ChildRender = () => (
  <Counter>
    {({count, increment, decrement, incrementAsync, incrementAsyncTen}) => (
      <section>
        <div>count: {count}</div>
        <button onClick={increment}>+</button>
        <button onClick={decrement}>-</button>
        <button onClick={incrementAsync}>incrementAsync</button>
        <button onClick={incrementAsyncTen}>incrementAsyncTen</button>
      </section>
    )}
  </Counter>
)

export default ChildRender; 
```

你可能会明白发生了什么。

我想看一看`Counter`组件的代码对你来说很容易。

```
// src/components/Counter

import React, { Component, cloneElement, Fragment } from 'react';

export default class Counter extends Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0,
      increment: this.increment,
      decrement: this.decrement,
      incrementAsync: this.incrementAsync,
      incrementAsyncTen: this.incrementAsyncTen
    }
  }

  increment = () => {
    this.setState(state => ({count: ++state.count}))
  }

  decrement = () => {
    this.setState(state => ({count: --state.count}))
  }

  incrementAsyncTen = () => {
    setTimeout(
      () => this.setState(state => ({count: state.count + 10})), 
      1000
    )
  }

  incrementAsync = () => {
    setTimeout(
      () => this.setState(state => ({count: ++state.count})), 
      1000
    )
  }

  render() {
    const { children } = this.props;
    return children({...this.props, ...this.state})
  }
} 
```

这很容易理解！而且我觉得它看起来跟[特设的图案](https://reactjs.org/docs/higher-order-components.html)很像。
你可以准备一个方法，你可以把它作为容器中`Counter`组件的一个参数，或者在你使用它的地方。

最后需要渲染`this.props.children()`！但是，你需要在参数中添加你想要添加的道具和状态或对象！

就是这样！

如果你对这些例子感兴趣，这里再举一个例子[和](https://github.com/takahiro-saeki/react-new-api-examples)。
请检查一下！

下一次，我应该描述如何使用 React.js 的门户 API！

感谢您的阅读！祝你有美好的一天！