# 与 React 门户共享上下文

> 原文：<https://dev.to/mickmister/sharing-context-with-react-portals-4dki>

React 中的状态管理是互联网上广泛讨论的话题。虽然大型应用程序最常见的方法似乎是 Redux，但是对 React 上下文 API 的改进已经引起了很多关注。使用渲染道具，我们现在可以轻松地访问树中的状态。对于单页面应用程序，这是理想的，因为整个应用程序是一棵树。

但是，如果您在整个页面中挂载几个组件树(在我的例子中，我使用 Ruby on Rails 和 Webpacker React 来呈现组件)，不同的树不能使用同一个提供者作为共同的祖先。因此，它们不能共享同一个状态。那么，我们如何让不同的树很好地一起玩呢？

### React 上下文+ React 门户

使用非常类似于:
的语法

```
ReactDOM.render(<MyComponent />, domElement); 
```

我们可以使用

```
const PortalComponent = React.createPortal(<MyComponent />, domElement); 
```

在同一个 React 树中，在 DOM 的不同隔离部分中呈现多个组件。使用`React.createPortal()`我们可以创建一个新的 React 组件，在指定的 DOM 节点上呈现。

与`ReactDOM.render()`相比，`createPortal()`的不同之处在于，组件在调用`createPortal()`时不会被渲染。我们使用`React.createPortal()`(一个新的 React 组件)的返回值，并在我们的树中呈现它。这里有一个例子，我们在页面中使用不同的入口点来渲染组件:

```
/* first let's define a context to share */
/* important_value_context.jsx */

import React from 'react'

const Context = React.createContext({})

export class ImportantValueProvider extends React.PureComponent {

  state = {
    importantValue: 'Cake is good',
    updateImportantValue: this.updateImportantValue,
  }

  updateImportantValue = importantValue => this.setState({importantValue})

  render() {
    return (
      <Context.Provider value={this.state}>
        {this.props.children}
      </Context.Provider>
    )
  }
}

export default Context

/* main.jsx */

import React from 'react'
import ReactDOM from 'react-dom'

import {ImportantValueProvider} from 'path/to/important_value_context'
import CoolComponent from 'path/to/cool_component'
import SuperCoolComponent from 'path/to/super_cool_component'

const Main = () => {

  const ComponentA = () => ReactDOM.createPortal(
    <CoolComponent />,
    document.getElementById('banner'),
  )

  const ComponentB = () => ReactDOM.createPortal(
    <SuperCoolComponent />,
    document.getElementById('footer'),
  )

  return (
    <ImportantValueProvider>
      <ComponentA />
      <ComponentB />
    </ImportantValueProvider>
  )
}

ReactDOM.render(
  <Main />,
  document.getElementById('main'),
) 
```

我们没有对`ReactDOM.render()`进行两次调用，而是创建了两个门户并在我们的顶级提供者下呈现这两个门户。`ComponentA`和`ComponentB`将被呈现在 DOM 中的两个不同的点上，但是由于门户，它们共享同一个 React 树。它们都可以导入`ImportantValueContext`并引用它的消费者来与同一个`ImportantValueContext`实例通信。

```
/* cool_component.jsx */

import React from 'react'

import ImportantValueContext from 'path/to/important_value_context'

class CoolComponent extends React.PureComponent {
  render() {
    return (
      <ImportantValueContext.Consumer>
        {sharedState => (
          <div>
            <input
              value={sharedState.importantValue}
              onChange={e => sharedState.updateImportantValue(e.target.value)}
            />
          </div>
        )}
      </ImportantValueContext.Consumer>
    )
  }
}

export default CoolComponent

/* super_cool_component.jsx */

import React from 'react'

import ImportantValueContext from 'path/to/important_value_context'

class SuperCoolComponent extends React.PureComponent {
  render() {
    return (
      <ImportantValueContext.Consumer>
        {sharedState => (
          <h1>{sharedState.importantValue}</h1>
        )}
      </ImportantValueContext.Consumer>
    )
  }
}

export default SuperCoolComponent 
```

现在`CoolComponent`和`SuperCoolComponent`可以与同一个重要值提供者共享状态。如果你打算在一个现有的网页中加入组件，这是一个好消息...如果你喜欢那种东西。

注意:这也适用于使用 Redux 提供者，尽管使用上下文是我的用例，所以我把重点放在这一点上。