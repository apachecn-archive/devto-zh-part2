# 反应上下文 API

> 原文：<https://dev.to/gipcompany/react-context-api-3dnn>

## Background

*   在 React 中，作为向下属组件传递数据的手段，有`props`的功能。 但是，使用`props`的话，就有必须像桶继电器一样源源不断地传递想传递到孙子组件、曾孙组件、……这样的组件的数据的弱点。
*   为了消除其弱点，`react-redux`提供了无论从哪个组件都可以访问特定数据的结构，是使用过 Redux 的人大部分都会受益的`Provider`组件。
*   `Provider`所谓组件，顾名思义就是以`Provider`组件 wrap 的所有组件为目的传递特定的数据。
*   之后，React 在 v16.3 版本中向 Redux 出售吵架吗？ 添加了令人怀疑的功能。 发布了与`react-redux`的`Provider`功能大致相同的同名`Provider`组件。
*   本文想介绍包括 React 方面发布的`Provier`在内的[Context](https://reactjs.org/docs/context.html) API，以及使用`Context API`的 APP 应用程序的安装示例。
*   另外，“我想先看源代码！ ”的人请根据末尾的[源代码](#%E3%82%BD%E3%83%BC%E3%82%B9%E3%82%B3%E3%83%BC%E3%83%89)进行`git clone`。

## react APP 应用的创建

因为需要 react APP 应用，所以用以下命令简单地制作。 顺便说一下，`npx`是在`npm`的 v5.2.0 中导入的，像`create-react-app`这样的在全局范围内安装后使用的命令，是为了即使不安装也能够执行的命令。 不想导入全局范围内不常使用的命令的人，可以像这次这样用`npx`执行。

```
$ npx create-react-app counter
$ cd counter 
```

以防万一，先确认一下`package.json`的内容吧。 因为 React 的版本必须在`v16.3`以上才能确认以后的动作。

这次确认时的`package.json`是以下内容。 `react`的版本为`^16.3.2`，所以没有问题。

```
{
  "name": "counter",
  "version": "0.1.0",
  "private": true,
  "dependencies": {
    "react": "^16.3.2",
    "react-dom": "^16.3.2",
    "react-scripts": "1.1.4"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
} 
```

然后启动 react APP 应用程序。

```
$ npm start 
```

执行`npm start`后浏览器自动启动，应该会显示`Welcome to React`的熟悉画面。 确认了这个之后，返回编辑器，编辑文件。

## React Context API 导入的计数器 APP 的例子

以下是“React Context API 引入的计数器 APP”的示例。

首先编辑 react APP 应用的顶级文件`src/index.js`。

编辑前，我想应该是以下这样，暂时全部删除。

```
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import registerServiceWorker from './registerServiceWorker';

ReactDOM.render(<App />, document.getElementById('root'));
registerServiceWorker(); 
```

然后，将`src/index.js`改写如下。

```
import React from 'react';
import ReactDOM from 'react-dom';
import { CounterContext } from './contexts/counter'
import Counter from './components/counter'

class App extends React.Component {
  constructor(props) {
    super(props)

    this.state = {
      count: 0,
      increment: () => this.setState(state => ({count: state.count + 1})),
      decrement: () => this.setState(state => ({count: state.count - 1}))
    }
  }

  render() {
    return (
      <CounterContext.Provider value={this.state} >
        <Counter />
      </CounterContext.Provider>
    )
  }
}

ReactDOM.render(<App />, document.getElementById('root')); 
```

以下几点是重点。

*   context 的制作不在`src/index.js`中进行，而是分离为其他文件。 因为如果设想大规模的 APP 应用，恐怕不分离成其他文件的话会产生混乱。 这是因为 context 实际上可以创建无限个。
*   出于与 context 相同的原因，也将 component 分离为单独的文件。 这是写 react APP 的人应该已经掌握的习惯。
*   状态通过 React 的基本功能 state 进行管理。 状态转移也用 React 的`setState`进行。 所以和用 Redux 写的时候不同，state 的变更和诱发它的事件在组件上黏糊糊的一点，对习惯写 redux APP 的人来说，可能会觉得这是一个无论什么都想分离的洁癖患者来说相当恶心的代码。
*   在与 import 的 CounterContext 关联的名为`CounterContext.Provider`的组件中，wrap 接收想要传递的状态的组件。
*   在这次的情况下，表示为`CounterContext.Provider`而不是`Provider`可以是`Provider`，但是因为 Context 可能存在无数个 Provider，所以为了能够识别是哪个 Provider，使用`CounterContext.`这个前缀来识别名字空间的习惯
*   在 Provier 上用`value=`传递了想交给 Consumer 的状态。

### Counter 组件

然后创建 Counter 组件。

```
$ mkdir src/components
$ touch src/components/counter.js 
```

在编辑器中编辑:

```
import React from 'react';

import { CounterContext } from '../contexts/counter'

const Counter = () => (
  <CounterContext.Consumer>
    {
      ({ count, increment, decrement }) =>  {
        return (
          <React.Fragment>
            <div>count: {count}</div>
            <button onClick={increment}>+1</button>
            <button onClick={decrement}>-1</button>
          </React.Fragment>
        )
      }
    }
  </CounterContext.Consumer> )

export default Counter 
```

以下几点是重点。

*   计数器组件是具有 Consumer 组件的组件。 所以在开头导入了相应的上下文。
*   标记为`CounterContext.Consumer`是因为与标记为`CounterContext.Provider`的理由相同。 (如上所述)
*   Consumer 内部的 child 是函数，是必需的。 请写下函数。
*   Consumer 内部函数的参数，可以接收提供者传递的 value。 在这次的 APP 应用程序中，可以接收计数器的值、增量的函数、减量的函数。
*   Consumer 只需在适当的位置显示接收到的值，或者将接收到的处理传递给适当的事件处理程序即可。

### CounterContextの作成

最后制作计数器专用的`CounterContext`。

```
$ mkdir src/contexts
$ touch src/contexts/counter.js 
```

将`src/contexts/counter.js`编辑如下。

```
import { createContext } from 'react';

export const CounterContext = createContext({
  count: null,
  increment: () => {},
  decrement: () => {}
}) 
```

上下文是希望在 Provider 和 Consumer 之间共享的状态或处理。 如上所述，可以将对象作为默认值传递给 createContext 参数。 count 可以为 0，但初始化在这次的实现中采用了在组件上执行的策略。

## 演示画面

这样，我想如下画面所示，计数器 APP 将显示在浏览器中并动作。

[![デモ画面](img/9639d7c4e5a28bd3a3f086af892a2db9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qN_5aDxp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4n72h8my8bcnp6fh9gyl.gif)

## 源代码

本文涉及的动作得到确认的源代码在[GitHub](https://github.com/ProgrammingSamurai/react-recipes) 中公开。 写起来麻烦的人请按以下命令`git clone`。

```
$ git clone git@github.com:ProgrammingSamurai/react-recipes.git 
```

## Inform

前几天，实现了 Udemy 讲师出道。 “[面向前端工程师的 react redux APP 应用开发入门](https://goo.gl/M1V3sD)”课程正在上映中。 想接下来试试 React 的人请一定要开始试试！ 可以通过这里的[链接](https://goo.gl/M1V3sD)打 95 折购买。 也非常欢迎您的提问！ :)