# React 的上下文 API，实现一个简单的小吃店

> 原文：<https://dev.to/akshay5995/react-s-context-api-by-implementing-a-simple-snack-bar-190m>

在 React 中，数据是通过 props 自顶向下(从父到子)传递的，但是对于应用程序中许多组件需要的某些类型的数据来说，这可能很麻烦。上下文提供了一种在组件之间共享这些值的方法，而不必显式地在树的每一层传递一个属性。

我相信我们都有这样的需求，我们必须为我们的 react 应用程序实现小吃店。在本文中，我将向您展示如何使用 React 的上下文 API 实现简单的小吃店。在我们深入研究之前，先了解一些背景知识。React 16.3 之前的上下文 API 是作为实验 API 提供的。现在它不再是实验性的，已经作为 React 16.3+版本的一部分发布了一段时间。本文的目标是构建一个最小的小吃店组件，并理解如何使用上下文 API 及其用例。

这是一个比官方文档中提供的例子更复杂的例子。Context 旨在共享数据，这些数据可以被视为 React 组件树的“全局”数据。在我们的示例中，我们将使用上下文来传递数据和处理函数，这些数据和函数是控制在应用程序中的任何位置打开/关闭 SnackBar 以及 SnackBar 所需的数据所必需的。让我们使用 create-react-app 来引导一个名为 react-context-demo 的新应用程序。

文件名在上面的注释中提供，每个代码片段使用相同的文件名来创建相应的文件。

**创建上下文**

```
/* file: SnackBarContext.jsx */

// Default State of our SnackBar
const DEFAULT_STATE = {
  show: false, // boolean to control show/hide
  displayText: "", // text to be displayed in SnackBar
  timeOut: 2000, // time SnackBar should be visible
};

// Create our Context
const SnackBarContext = React.createContext(DEFAULT_STATE); 
```

创建的上下文(SnackBarContext)具有属性{ Provider，Consumer }。当 React 呈现一个上下文消费者时，它将从树中它上面最近的匹配提供者读取当前上下文值。

**提供商**

```
<Provider value={/* some value */} /> 
```

允许消费者订阅上下文更改的 React 组件。它接受一个*值*，该值将被传递给作为该提供者后代的消费者。

现在让我们创建一个名为 *SnackBarProvider* 的提供者。

```
/* file: SnackBarContext.jsx */

export class SnackBarProvider extends PureComponent {
  constructor(props) {
    super(props)
    // DEFAULT_STATE defined earlier
    this.state = Object.assign({}, DEFAULT_STATE)
    this.handleClose = this.handleClose.bind(this)
    this.handleOpen = this.handleOpen.bind(this)
  }
  handleOpen(displayText) {
    // Show the SnackBar with 'displayText'
    this.setState({
      show: true,
      displayText,
    })
    setTimeout(
      // To hide SnackBar after 2s
      () => this.setState({ show: false }),
      this.state.timeOut
    )
  }
  handleClose() {
    // Show the SnackBar with 'displayText'
    this.setState({
      show: false,
      displayText: "",
    })
  }
  render() {
    return (
      <SnackBarContext.Provider
        value={{
          ...this.state,
          handleOpen: this.handleOpen,
          handleClose: this.handleClose,
        }}
      >
        {this.props.children}
      </SnackBarContext.Provider>
    )
  }
} 
```

*SnackBarProvider* 将是一个有状态的组件，它将在 *SnackBarContext 中返回它的孩子(this.props.children)。提供者*提供必要的值(状态的数据和处理程序)传递给 *SnackBarContext。Consumer* 由我们的 SnackBar 组件使用，位于其子组件树的某个位置。(!重要)

在 *SnackBarProvider* 中，我们将`DEFAULT_STATE`分配给 state. `DEFAULT_STATE`将默认值传递给消费者。

我们需要 SnackBar 有状态地显示和隐藏。State 的 show 属性将是一个布尔值，用于显示或隐藏我们的 SnackBar。函数 handleClose 和 handleOpen 用作状态更改的处理程序，在我们的 SnackBar 中将会使用到。

handleOpen 将接受一个参数 displayText，即要在 SnackBar 中显示的文本，并将 displayText 和 show 设置为 true。并且，handleClose 会将显示更改为 false，并将文本显示为默认状态，即空字符串。

内部渲染我们将我们的 *SnackBarContext。Provider* 将我们的 SnackBarProvider 的状态(…this.state)和我们的处理程序(handleOpen 和 handleClose)的正确命名值传递给它。 *SnackBarContext。Provider* 将包含我们的 SnackBarProvider 的子元素，这样我们就可以使用 *SnackBarContext。消费者*沿着我们的子组件树访问这些值和函数。

现在我们已经完成了提供者的工作，让我们看看如何使用消费者。

**消费者**

```
<Consumer>
  {value => /* render something based on the context value */}
</Consumer> 
```

消费者把一个功能当成一个孩子。该函数的 value 参数包含传递给 closestProvider 的 prop 值。该函数接收当前上下文值并返回一个 React 节点。让我们看看如何在我们的案例中使用它。

我们将有一个 *SnackBar* 组件，它将使用来自我们的提供者的道具来控制它的可见性(显示/隐藏功能)。

```
const SnackBar = ({ show, handleClose, displayText }) => (
  <div className="snackBarHolder">
    {show && ( // controls visibility
      <div className="snackBar">
        <span>
          {
            displayText // text to be displayed
          }
        </span>
        <button onClick={() => handleClose()}>OK</button>
      </div>
    )}
  </div>
) 
```

*show* 将控制可见性，SnackBar 中的按钮将使用 handleClose 来强制隐藏 SnackBar，displayText 是将在 SnackBar 中显示的主要文本。

因此，我们知道 SnackBar 组件需要 props show、handleClose、displayText 才能按要求工作。让我们看看如何使用 SnackBarContext。消费者获得所需的道具。

所以我们将创建一个*高阶组件*，它将一个组件作为参数，并将来自提供者的值作为道具传递给该组件。姑且称之为 *withSnackBarConsumer* 。

```
/* file: SnackBarContext.jsx */

export const withSnackBarConsumer = WrappedComponent => {
  const WrapSnackBarConsumer = props => (
    <SnackBarContext.Consumer>
      {({ show, displayText, handleOpen, handleClose }) => {
        const snackBarProps = {
          show,
          displayText,
          handleOpen,
          handleClose,
        }
        return <WrappedComponent {...snackBarProps} {...props} />
      }}
    </SnackBarContext.Consumer>
  )
  return WrapSnackBarConsumer
} 
```

这里， *withSnackBarConsumer* 将接受一个 WrappedComponent 作为参数，并返回 WrapSnackBarConsumer，它用 SnackBarContext 包装 WrappedComponent。消费者通过使用我们的函数作为消费者的子签名。

通过使用消费者，我们获得由提供者提供的值(show、displayText、handleOpen、handleClose ),并将这些值(snackBarProps)作为道具传递给我们的 WrappedComponent。我们可以使用 withSnackBarConsumer 来包装我们的 SnackBar，就像:

```
*/* file: SnackBar.jsx */*

import { withSnackBarConsumer } from './SnackBarContext';

const SnackBar = ({ show, handleClose, displayText }) => (
  <div className="snackBarHolder">
   {
    show // controls visibility
    && (
      <div className="snackBar" >
       <span>{
         displayText // text to be displayed
        }</span>
       <button onClick={() => handleClose()}>
          OK
        </button>
      </div>
     )
   }
  </div>
);

export default withSnackBarConsumer(SnackBar); 
```

现在我们已经把*连上了*(就像 Redux 一样，嗯？)我们的 SnackBar 到 SnackBarProvider 提供的值。

我们可以开始讨论如何使用相同的 withSnackBarConsumer 在我们的应用程序中提供其他组件，以便使用 handleChange 使我们的 SnackBar 可见。

让我们使用 *withSnackBarConsumer* HOC 创建一个 SnackBarControl a 按钮。

```
/* file: SnackBarControl.jsx */

import { withSnackBarConsumer } from "./SnackBarContext";

const SnackBarControl = ({ text, handleOpen }) => (
  <button onClick={() => handleOpen(text, buttonText)}>Show me!</button>
);

export default withSnackBarConsumer(SnackBarControl); 
```

SnackBarControl 使用来自 SnackBarProvider 的 handleOpen。我们使用我们的 withSnackBarConsumer 将 SnackBarControl 连接到 handleOpen。

现在我们有了 SnackBarControl、SnackBar、SnackBarProvider，让我们来看看它的运行情况。

```
/* file: App.js from create-react-app */

import React, { Component } from 'react';
import { SnackBarProvider } from './SnackBarContext';
import SnackBarControl from './SnackBarControl.jsx';
import logo from './logo.svg';
import './App.css';

class App extends Component {
  render() {
    return (
      <SnackBarProvider>
         <div className="App">
            <header className="App-header">
             <img src={logo} className="App-logo" alt="logo" />
             <h1 className="App-title">Welcome to React</h1
             </header>
             <SnackBarButton text="Hey There!"/>
          </div>
        <SnackBar />
     </SnackBarProvider>
     );
   }
}

export default App; 
```

在 App.css 中为您的 SnackBar 添加这些样式，使它看起来像一个真正的 snackBar！

```
/* file: App.css from create-react-app */

.SnackBarHolder {
  position: fixed;
  z-index: 50;
  bottom: 20px;
  left: 50%;
  -webkit-animation: fadein 0.5s, fadeout 0.5s 2.5s;
  -moz-animation: fadein 0.5s, fadeout 0.5s 2.5s;
  -ms-animation: fadein 0.5s, fadeout 0.5s 2.5s;
  -o-animation: fadein 0.5s, fadeout 0.5s 2.5s;
  animation: fadein 0.5s, fadeout 0.5s 2.5s;
}
@-webkit-keyframes fadein {
  from {
    bottom: 0;
    opacity: 0;
  }
  to {
    bottom: 20px;
    opacity: 1;
  }
}
@keyframes fadein {
  from {
    bottom: 0;
    opacity: 0;
  }
  to {
    bottom: 20px;
    opacity: 1;
  }
}
@-webkit-keyframes fadeout {
  from {
    bottom: 20px;
    opacity: 1;
  }
  to {
    bottom: 0;
    opacity: 0;
  }
}
@keyframes fadeout {
  from {
    bottom: 20px;
    opacity: 1;
  }
  to {
    bottom: 0;
    opacity: 0;
  }
}
.SnackBar {
  width: 250px;
  padding: 10px;
  background: black;
  display: flex;
  align-items: center;
  justify-content: space-between;
  box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19);
}
.SnackBar > span {
  color: white;
}
.SnackBar > button {
  background: transparent;
  color: #00b4d2;
  outline: none;
  border: 0;
  cursor: pointer;
} 
```

点击*演示！*去看神奇的✨.

恭喜，您已经使用 React 的上下文 API 成功实现了一个简单的小吃店。

请务必阅读[官方文件](https://reactjs.org/docs/context.html)以便更好地理解。