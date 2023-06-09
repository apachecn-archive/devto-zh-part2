# Mint vs JavaScript(堆栈)

> 原文：<https://dev.to/gdotdesign/mint-vs-javascript-stack-15id>

在这篇文章中，我们将比较 Mint 和 JavaScript，更具体地说，我们将把它和等效的 JavaScript 栈进行比较，因为 Mint 也是一个框架。

这种比较将涵盖以下各层:

*   语言
*   类型检查
*   开发服务器
*   视角
*   状态
*   按指定路线发送
*   建立工作关系网
*   测试
*   错误消息
*   格式化
*   生产构建

每层都有以下部分:

*   带有示例的 JavaScript 实现
*   Mint 实现的一个例子
*   实现的比较
*   统计数据(如果适用)

注意:它涵盖了很多特性，所以这是一篇很长的文章。

# 语言

比较这两种语言是很棘手的，因为它们有很大的不同，所以我只是试着给它们下一个小定义。

这篇文章中有一些例子，可以让你对语法和语义有一个大致的了解。

### JavaScript

维基百科将其定义为:

> JavaScript(/ˈdʒɑːvəˌskrɪpt/),[7)通常缩写为 JS，是一种高级解释编程语言。它也是一种动态的、弱类型的、基于原型的和多范例的语言。

### 薄荷

Mint 还没有一个官方的定义，所以我尽量用一小段话概括一下:

> Mint 是一种编译成 JavaScript 的编程语言。专为编写客户端 web 应用程序而创建。它基本上是强类型的，带有一些面向对象的功能。

# 类型检查

这些语言应该支持某种类型的检查，这很重要，因为它使我们的代码更安全，错误更少。

### JavaScript

对于 JavaScript，有第三方工具用于类型检查，比如我们将使用的 [Flow](https://flow.org) ，但是在我们进行实际的类型检查之前，我们需要将我们的类型化 JavaScript(带有类型注释)编译成浏览器可以使用的常规 JavaScript(移除类型注释)。

为此，我们将使用[巴别塔](https://babeljs.io)，所以我们需要安装一些包来使它工作:`babel-core` `babel-preset-react`(出于某种原因，它包括流预置)我们还安装了`babel-preset-es2015`和`babel-preset-stage-0`来使高级 JavaScript 特性可用。

为了配置 babel，我们需要创建一个`.babelrc`文件:

```
{
  "presets": ["es2015", "react", "stage-0"]
} 
```

Enter fullscreen mode Exit fullscreen mode

我们还需要安装`flow-bin`来进行实际的类型检查，安装`flow-typed`来为我们使用的包定义类型。

### 薄荷

Mint 自带类型检查器，所以你不需要做任何事情，它会自动运行。

### 比较

要在 JavaScript 中进行类型检查，你需要一个第三方工具，在 Mint 中它是内置的。

### 统计数据

| 统计数字 | Java Script 语言 | 铸造 |
| --- | --- | --- |
| 代码行 | **4** | **0** |
| 第三方软件包 | **6**
`babel-core`
`babel-preset-react`
`babel-preset-es2015`
`babel-preset-stage-0`
`flow-bin`
`flow-typed` | **0** |

# 开发服务器

我们的开发环境应该能够对以下事情:

*   将我们的代码编译成一个文件
*   当源文件改变时重新编译代码，并刷新浏览器
*   从目录中提供静态文件
*   如果有语法或类型错误，则提供错误消息
*   如果路线与静态文件不匹配，退回到`index.html`文件

### JavaScript

为了编译我们的代码，我们可以将 [Webpack](https://webpack.js.org) 与`webpack` `webpack-cli`和`webpack-dev-server`包一起使用，而要使用 Babel，我们需要`babel-loader`包。

安装之后，我们使用`webpack.config.js`文件:
对它们进行配置

```
const path = require("path");

module.exports = {
  context: path.resolve(__dirname, "src"),
  mode: 'development',
  // The main.jsx will be compiled
  entry: {
    main: ["main.jsx"]
  },
  // This tells webpack how to resolve things
  resolve: {
    modules: [path.resolve("./src"), "node_modules"],
    extensions: [".jsx"]
  },
  module: {
    // This tells webpack to use babel
    rules: [
      {
        test: /\.jsx$/,
        use: {
          loader: 'babel-loader',
        }
      }
    ]
  },
  // Configuration for the development server
  devServer: {
    // Serve static files from the public folder
    contentBase: './public',
    // Fallback to the index.html
    historyApiFallback: {
      rewrites: [
        {
          from: '/./',
          to: '/'
        }
      ]
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在配置中:

*   我们正在使用带有`babel-loader`包的巴别塔来转换我们的代码
*   为服务器设置回退到`index.html`
*   指定要编译的文件和目录
*   指定静态文件目录
*   指定主文件

之后，我们需要创建实际的`public/index.html`文件，该文件将被提供:

```
<!DOCTYPE html>
<html>
<head>
  
</head>
<body>
  <div id="root"></div>
  <script type="text/javascript" src="/main.js"></script>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

之后，开发服务器可以启动:`webpack-dev-server`

### 薄荷

在 Mint 中，开发服务器内置在二进制文件中。在用`mint init`初始化我们的项目后，我们唯一需要做的就是用`mint start`启动它

### 比较

在 Mint 中，这是一个内置的特性，而在 JavaScript 中，你需要使用第三方包来实现相同的设置。

### 统计数据

| 统计数字 | Java Script 语言 | 铸造 |
| --- | --- | --- |
| 代码行 | **44** | **0** |
| 第三方软件包 | **4**`webpack``webpack-dev-server``webpack-cli`T3】 | **0** |

# 视图

对于视图层，需要满足以下条件:

*   样式应该作用于当前组件，并且应该用 CSS 编写，在同一个文件中
*   应该对组件的属性进行类型检查(最好是在编译时，但运行时也可以)，还应该提供它们的默认值

出于示例的目的，我们将实现一个简单的计数器组件:

*   它应该显示一个计数器
*   它应该有两个按钮，一个用于递减计数器，一个用于递增计数器
*   如果低于零度，背景颜色应该是红色(`orangered`)，如果高于十度，背景颜色应该是绿色(`limegreen`)

### JavaScript

Flow 自动检查属性类型，我们只需要为它们创建一个类型并使用它，对于默认属性我们可以使用静态类属性，对于样式化我们可以使用 [styled-components](https://github.com/styled-components/styled-components) 。

```
// @flow

import React, { Component } from "react";
import styled from "styled-components";

/* This is the styling for the base div. */
const BaseDiv = styled.div`
  background: ${props => props.background};
  border-radius: 5px;
  transition: 320ms;
  display: flex;
  padding: 20px;
  margin: 20px;
`

/* This is the styling for the counter span. */
const CounterSpan = styled.span`
  font-family: sans;
  font-size: 20px;
  padding: 0 20px;
`

/* These are the property type definitons. */
type Props = {
  onIncrement: () => void,
  onDecrement: () => void,
  counter: number
};

export default class Counter extends Component<Props> {
  /* These are the default property values. */
  static defaultProps = {
    onIncrement: () => null,
    onDecrement: () => null,
    counter: 0
  }

  /* This is a function to return the background color. */
  background () {
    const { counter } = this.props

    if (counter >= 10) {
      return "lightgreen"
    } else {
      if (counter < 0) {
        return "orangered"
      } else {
        return "#F2F2F2"
      }
    }
  }

  /* Renders the component. */
  render () {
    const { counter, onDecrement, onIncrement} = this.props

    return <BaseDiv background={this.background()}>
      <button onClick={() => onDecrement()}>
        Decrement
      </button>

      <CounterSpan>
        { counter }
      </CounterSpan>

      <button onClick={() => onIncrement()}>
        Increment
      </button>
    </BaseDiv>
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

此外，为了能够显示我们的计数器，我们需要将其添加到 DOM 中。

```
// @flow

/* Default react imports. */
import React, { Component } from "react";
import styled from "styled-components";
import ReactDom from "react-dom";

/* Import the counter component. */
import Counter from './counter.jsx';

/* The base style. */
const StyledDiv = styled.div`
  justify-content: center;
  flex-direction: column;
  align-items: center;
  font-family: sans;
  display: flex;
  height: 100vh;
`

/* This is our main component. */
class Main extends Component {
  render () {
    return <StyledDiv><Counter/></StyledDiv>
  }
}

/* Get the root element. */
const root = document.getElementById('root')

/* If there is a root element render the main component. */
if (root) { 
  ReactDOM.render(<Main/>, root) 
} 
```

Enter fullscreen mode Exit fullscreen mode

### 薄荷

在 Mint 中，你可以用类型和默认值逐个定义属性，样式是用`style`块完成的。

```
component Counter {
  /* Here we are defining the properties of the counter. */
  property onIncrement : Function(a) = () : Void => { void }
  property onDecrement : Function(a) = () : Void => { void }
  property counter : Number = 0

  /* This is the styling for the base div. */
  style base {
    background: {background};
    border-radius: 5px;
    transition: 320ms;
    display: flex;
    padding: 20px;
    margin: 20px;
  }

  /* This is the styling for the counter span. */
  style counter {
    font-family: sans;
    font-size: 20px;
    padding: 0 20px;
  }

  /* This is a computed property for the background color. */
  get background : String {
    if (counter >= 10) {
      "lightgreen"
    } else if (counter < 0) {
      "orangered"
    } else {
      "#F2F2F2"
    }
  }

  fun render : Html {
    <div::base>
      <button onClick={(event : Html.Event) : Void => { onDecrement() }}>
        <{ "Decrement" }>
      </button>

      <span::counter>
        <{ Number.toString(counter) }>
      </span>

      <button onClick={(event : Html.Event) : Void => { onIncrement() }}>
        <{ "Increment" }>
      </button>
    </div>
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

为了在屏幕上显示一些东西，我们需要定义`Main`组件:

```
component Main {
  style base {
    justify-content: center;
    flex-direction: column;
    align-items: center;
    font-family: sans;
    display: flex;
    height: 100vh;
  }

  fun render : Html {
    <div::base>
      <Counter/>
    </div>
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 比较

这两种实现遵循几乎相同的语义，看起来非常相似，但也有一些不同之处:

*   在 JavaScript 中有样式化的元素(不同的组件)，在 Mint 中一个`style`可以单独应用于一个元素
*   在 JavaScript 中，样式的值需要显式传递，在 Mint 中,`style`块使用与组件的函数和计算属性相同的作用域
*   在 JavaScript 中，属性是在两个块中定义的，在 Mint 中是逐个定义的。
*   在 JavaScript 中，使用不同的类名(不同的背景颜色)为每个版本的样式复制静态 CSS，在 Mint 中只有一个使用 CSS 变量的选择器
*   在 JavaScript 中文本内容是隐式的，在 Mint 中是显式的

### 统计数据

| 统计数字 | Java Script 语言 | 铸造 |
| --- | --- | --- |
| 代码行 | **60** | **52** |
| 第三方软件包 | **3**
`react` `react-dom` `styled-components`
 | **0** |

# 状态

对于状态，我们需要一个全局可访问的实体，它包含应用程序的状态(计数器)和让我们改变它的函数，因为没有更好的术语，我们称它为**存储**。

### JavaScript

对于 JavaScript 来说，有很多使用 store 范型在应用程序中处理数据的框架: [Redux](https://redux.js.org) 、 [Redux Saga](https://redux-saga.js.org) 、 [Mobx](https://github.com/mobxjs/mobx) 仅举几个例子，这里我们将使用 Redux。

在一个新文件中，我们创建了**动作**、**动作创建者**、**缩减者**，最后是**存储库**。

```
// @flow

import { createStore } from "redux";

/* These are the actions we can take. */
const INCREMENT = "INCREMENT";
const DECREMENT = "DECREMENT";

/* The type of the state. */
type State = {|
  counter: number
|};

/* The type of the action. */
type Action = {|
  payload: null,
  type: string
|};

/* This is the initial state. */
const initialState: State = {
  counter: 0
};

/* This is the reducer which steps the state forward. */
const reducer = function(state: State = initialState, action: Action): State {
  switch (action.type) {
    case INCREMENT:
      return { ...state, counter: state.counter + 1 };

    case DECREMENT:
      return { ...state, counter: state.counter - 1 };

    default:
      return state;
  }
};

/* This is an action creater for the increment action. */
export const increment = (): Action => {
  return {
    type: INCREMENT,
    payload: null
  };
};

/* This is an action creater for the decrement action. */
export const decrement = (): Action => {
  return {
    type: DECREMENT,
    payload: null
  };
};

/* This is a function which creates a store. */
export const store = createStore(reducer, initialState); 
```

Enter fullscreen mode Exit fullscreen mode

之后，我们需要将商店连接到我们的组件:

```
...

/* We need to import the action creators and the store from the other file. */
import { increment, decrement } from "./store.jsx";

/* The connect comes from the react-redux package. */
import { connect } from "react-redux";

...

/* This is our main component which is connected to the store. */
class Main extends Component {
  render() {
    const { counter, onIncrement, onDecrement } = this.props;

    return (
      <div>
        <Counter
          onIncrement={onIncrement}
          onDecrement={onDecrement}
          counter={counter}
        />
      </div>
    );
  }
}

/* We need to map the state from the store to our components properties. */
const mapStateToProps = state => {
  return {
    counter: state.counter
  };
};

/* We need to map the actions from the store to our components properties. */
const mapDispatchToProps = dispatch => {
  return {
    onIncrement: () => {
      dispatch(increment());
    },
    onDecrement: () => {
      dispatch(decrement());
    }
  };
};

/*
Finally we are creating a new component by connecting the store the original one, using the two functions above.
*/
export const App = connect(
  mapStateToProps,
  mapDispatchToProps
)(Main); 
```

Enter fullscreen mode Exit fullscreen mode

### 薄荷

在 Mint 中，使用`store`只需要做两件事:

声明它们:

```
store Store {
  /* The data lives in states. */
  state counter : Number = 0

  /* A store can have any number of functions. */
  fun increment : Promise(Never, Void) {
    /* The next statements steps the state forward based on the previous state. */
    next { counter = counter + 1 }
  }

  fun decrement : Promise(Never, Void) {
    next { counter = counter - 1 }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

并将它们连接到一个组件:

```
component Main {
  /* 
  We are connecting to the store and explicitly exposing 
  it's properties and functions to be available for the 
  component.
  */
  connect Store exposing { counter, increment, decrement }

  ...

  fun render : Html {
    <div::base>
      <Counter 
        onIncrement={increment} 
        onDecrement={decrement}
        counter={counter}/>
    </div>
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 比较

这两种方法的基本思想是相同的，尽管 Redux 实现更加复杂:

*   还有更多类型的实体(动作、动作创建者、存储、缩减者)
*   将状态和动作映射到属性
*   需要正确使用的外部功能(`connect`、`createStore`)
*   动作有特定的类型，通常有一个名称

在 Mint 中，所有的东西都被类型化检查，甚至是组件和存储之间的连接，因此，举例来说，如果我们碰巧暴露了存储上没有的东西，我们会得到一个很好的错误消息。

这两种实现之间有显著的差异:

*   Redux 使用组件属性来传递动作和数据，而在 Mint 中，它在组件范围内可用
*   在 Redux 中有一个[特设组件](https://reactjs.org/docs/higher-order-components.html)，它将商店连接到基本组件

### 统计数据

| 名字 | Java Script 语言 | 铸造 |
| --- | --- | --- |
| 代码行 | **103** | **13** |
| 第三方软件包 | **2** `redux` `react-redux` | **0** |

# 路由

我们用于测试目的的示例应用程序应该实现三条路线:

*   `/`显示计数器的起始值为`0`
*   `/10`(或任意数字)显示计数器的起始值，该值来自路径
*   `/about`显示应用程序的一些信息(虚拟文本就足够了)

应该有所有三个路由的链接，应用程序应该处理。

### JavaScript

在这种情况下，我们将使用[反应路由器](https://reacttraining.com/react-router/web/guides/philosophy)。

为了使路由工作，我们需要采取许多步骤。

首先，我们需要修改我们的存储，以便能够直接设置计数:

```
...

/* Add a new action. */
const SET = "SET";

...

/* Update the Action type. */
export type Action = {|
  payload: number | null,
  type: string
|};

...
    /* Add a new branch in the reducer for the given action. */
    case SET:
      return { ...state, counter: action.payload || 0 };
...

/* Create an action creator for the new action. */
export const set = (payload : number): Action => {
  return {
    payload: payload,
    type: SET
  };
}; 
```

Enter fullscreen mode Exit fullscreen mode

然后我们需要创建一个新的组件来处理路由，我们称之为`Page`

```
/* Import the necessary entitites. */
import { BrowserRouter as Router, Route, Link } from 'react-router-dom'

/* Import to store creator. */
import { store } from "./store.jsx";

/* 
Create a functional component which parses the count from the route
and passes it to the App component.
*/
const RoutedApp = (props) =>  {
  const parsed = Number.parseInt(props.match.params.count)
  const initialCount = parsed || 0

  /* The key is needed because the `componentDidMount` function sets the counter. */
  return <App 
    key={initialCount.toString()}
    initialCount={ initialCount} 
    store={store} /> }

/* Create a functional component which has to rooting. */
const Page = () =>
  <Router>
    <div>
      <ul>
        <li><Link to="/">0</Link></li>
        <li><Link to="/1">1</Link></li>
        <li><Link to="/about">About</Link></li>
      </ul> 
      <hr/>

      <Route exact path="/about" component={<div></div>}/>
      <Route exact path="/" render={RoutedApp}/>
      <Route path="/:count" component={RoutedApp}/>
    </div>
  </Router> 
```

Enter fullscreen mode Exit fullscreen mode

然后我们需要修改我们的`App`组件，以便在它加载时设置`counter`。

```
/* Expose the set function from the store. */
import { increment, decrement, set } from "./store.jsx";

class Main extends Component {
  /* When mounted set the counter. */
  componentDidMount () {
    this.props.set(this.props.initialCount)
  }

  ...
}

const mapDispatchToProps = dispatch => {
  ... 
  /* Add a prop to dispatch the set action. */
  set: (payload : number) => {
    dispatch(set(payload));
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 薄荷

首先，我们需要在我们的存储中添加一个函数来设置`counter`和一个`state`来表示哪个页面是活动页面，并添加一个函数来设置`page` :

```
store Store {
  /* Create a new state for the page. */
  state page : String = ""

  ...

  fun setCounter (counter : Number) : Promise(Never, Void) {
    next { counter = counter }
  }

  fun setPage (page : String) : Promise(Never, Void) {
    next { page = page }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我们使用`routes`顶级块:
来处理路径

```
/* In a routes block you can define the routes of the application. */
routes {
  /* 
  This matches the /about path, needs to be first because 
  routes are matched from top to bottom. 
  */
  /about {
    /* We can directly call store functions. */
    Store.setPage("about")
  }

  /* This matches the index path. */
  / {
    /* Sequence allows us to do more things in sequence. */
    sequence {
      Store.setCounter(0)
      Store.setPage("counter")
    }
  }

  /* This matches the /10 path. */
  /:value (value : String) {
    sequence {
      /* Here we convert a string to a number safely. */
      counter = 
        value
        |> Number.fromString()
        |> Maybe.withDefault(0)

        Store.setCounter(counter)
        Store.setPage("counter")
    }
  }  
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我们需要修改我们的主要组件:

```
component Main {
  /* Expose the page property. */
  connect Store exposing { counter, increment, decrement, page }

  ...

  get content : Html {
    /* Decide what to render based on the page. */
    case (page) {
      "counter" =>
        <Counter
          onIncrement={increment}
          onDecrement={decrement}
          counter={counter}/>

      "about" =>
        <div>
          <{ "about" }>
        </div>

      =>
        <div>
          <{ "" }>
        </div>
    }
  }

  fun render : Html {
    <div::base>
      <ul>
        <li>
          <a href="/">
            <{ "/0" }>
          </a>
        </li>

        <li>
          <a href="/10">
            <{ "/10" }>
          </a>
        </li>

        <li>
          <a href="/about">
            <{ "/about" }>
          </a>
        </li>
      </ul>

      <{ content }>
    </div>
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 比较

这两种路由实现有着根本的不同:

*   在 JavaScript 中，它是基于组件的(当路径改变时渲染一些东西)，而在 Mint 中，它是基于动作的(当路径改变时做一些事情)
*   在 JavaScript 中，路由不与商店交互，而在 Mint 中，它与商店交互
*   在 JavaScript 中，路由由开发人员处理，而在 Mint 中由运行时处理
*   在 JavaScript 中，链接需要一个组件，在 Mint 中，它们是普通的`a`标签，运行时处理导航

### 统计数据

| 名字 | Java Script 语言 | 铸造 |
| --- | --- | --- |
| 代码行 | **68** | **47** |
| 第三方软件包 | **1**T0】 | **0** |

# 联网

为了演示如何从网络上获取内容，应该在 about 页面上显示一个简单的文本文件内容，它是动态获取的。

### JavaScript

在 JavaScript 中，使用[获取 API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 可以直接做到这一点。

我们将为此创建一个有状态的组件:

```
/* Import React and Component. */
import React, { Component } from "react";

/* The component for the about page. */
export default class About extends Component {
  /* In the constructor set the initial state. */
  constructor(props) {
    super(props)

    this.state = {
      /* This field is for tracking the status of the request. */
      status: "INITIAL",
      /* The content which will be displayed once loaded. */
      content: ""
    }
  }

  /* When the component is mounted. */
  componentDidMount() {
    /* Set the status as loading. */
    this.setState({ status: "LOADING" }, () => {
      /* Fetch the data. */
      fetch('/about.txt')
      .then((response) => {
        /* Get the text. */
        response
        .text()
        .then((body) => {
          /* Set the status to loaded and content. */
          this.setState({
            status: "LOADED",
            content: body
          })
        })
      })
      .catch(() => {
        /* On an error set the status. */
        this.setState({
          status: "ERRORED",
          content: ""
        })
      })
    })
  }

  render () {
    /* Based on the status render things. */
    switch (this.state.status) {
      case "LOADING":
        return <div>Loading...</div>
      case "ERRORED":
        return <div>Could not load the content...</div>
      case "LOADED":
        return <div>{this.state.content}</div>
      default:
        return false
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在我们有了需要更新的组件或`Page`组件之后，就可以包含它了:

```
...
<Route exact path="/about" component={About}/> ... 
```

Enter fullscreen mode Exit fullscreen mode

### 薄荷

在 Mint 中，我们需要使用标准库`mint-core`中的`Http`模块，该模块在项目初始化时自动安装。

```
/* Define an enum for the status. */
enum Status {
  Initial
  Loading
  Loaded
  Errored
}

/* The component for the about page. */
component About {
  /* A state to track the status. */
  state status : Status = Status::Initial

  /* A state for the content. */
  state content : String = ""

  /* When the component is mounted. */
  fun componentDidMount : Promise(Never, Void) {
    /* In a sequence expression statements are executed asynchronously in sequence. */
    sequence {
      /* Set the status to loading. */
      next { status = Status::Loading }

      /*
      Get the response and unpack it from a
      Result(Http.ErrorResponse, Http.Response).
      */
      response =
        "/about.txt"
        |> Http.get()
        |> Http.send()

      /* Set the status to loaded and the content. */
      next
        {
          status = Status::Loaded,
          content = response.body
        }
    } catch Http.ErrorResponse => error {
      /* On an error set the status to errored. */
      next
        {
          status = Status::Errored,
          content = ""
        }
    }
  }

  /* Renders the component. */
  fun render : Html {
    /* Renders things based on status. */
    case (status) {
      Status::Initial => Html.empty()

      Status::Loading =>
        <div>
          <{ "Loading..." }>
        </div>

      Status::Errored =>
        <div>
          <{ "Could not load the content..." }>
        </div>

      Status::Loaded =>
        <div>
          <{ content }>
        </div>
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们也需要更新`Main`组件来显示它:

```
...
"about" => <About/>
... 
```

Enter fullscreen mode Exit fullscreen mode

### 比较

实施基本上遵循相同的步骤，但有所不同:

*   在 JavaScript 中，我们可以对异步任务使用承诺，在 Mint 中，这是一种使用`sequence`表达式的语言特性
*   在 JavaScript 中，我们可以省略错误处理，在 Mint 中，错误处理是通过漂亮的错误消息来实现的
*   在 JavaScript 中，我们需要使用`this.state`和`this.setState`进行状态处理，在 Mint 中，这是一个内置特性，使用州名和`next`关键字
*   在 JavaScript 中我们需要使用字符串来表示`status`,在 Mint 中我们可以使用`enum`

### 统计数据

| 统计数字 | Java Script 语言 | 铸造 |
| --- | --- | --- |
| 代码行 | **60** | **72** |
| 第三方软件包 | **0** | **1**T0】 |

# 测试

我们将为`Counter`组件编写三个简单的测试:

*   正确显示计数器
*   点击增量按钮增加计数器
*   点击减少按钮减少计数器

### JavaScript

我们将使用 [Jest](https://jestjs.io) 和[酶](https://github.com/airbnb/enzyme)来测试`Counter`成分。我们还需要添加`enzyme-adapter-react-16`来让酶与 React 一起工作，我们还需要在`package.json`中为 Jest 设置一些配置来避免错误:

```
...
 "jest": {
    "testURL": "http://localhost/"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以为我们的组件创建一个测试文件:

```
/* Import things. */
import Adapter from 'enzyme-adapter-react-16';
import React, { Component } from 'react';
import Enzyme, { mount } from 'enzyme';

/* Configure enzyme. */
Enzyme.configure({adapter: new Adapter()});

/* Import our Counter component. */
import Counter from './counter.jsx';

/* A test component which handles the state. */
class TestComponent extends Component {
  constructor(props) {
    super(props)
    this.state = { counter: 0 }
  }

  increment() {
    this.setState({ counter: this.state.counter + 1 })
  }

  decrement() {
    this.setState({ counter: this.state.counter - 1 })
  }

  render() {
    return <Counter
      onIncrement={() => this.increment()}
      onDecrement={() => this.decrement()}
      counter={this.state.counter}
      />
  }
}

it('displays the counter', () => {
  const counter = mount(<TestComponent/>);

  expect(counter.find('span').text()).toEqual('0');
});

it('decrements the counter', () => {
  const counter = mount(<TestComponent/>);

  expect(counter.find('span').text()).toEqual('0');

  // Simulate a click and update the view.
  counter.find('button').first().simulate("click")
  counter.update()

  expect(counter.find('span').text()).toEqual('-1');
});

it('increments the counter', () => {
  const counter = mount(<TestComponent/>);

  expect(counter.find('span').text()).toEqual('0');

  counter.find('button').last().simulate("click")
  counter.update()

  expect(counter.find('span').text()).toEqual('1');
}); 
```

Enter fullscreen mode Exit fullscreen mode

要运行我们刚刚运行的测试:`jest`

### 薄荷

在 Mint 中，该语言有两个专门用于测试的关键字:`suite`和`test`，用它们我们可以很容易地创建测试:

```
/* Create component for testing the counter which contains the state. */
component TestCounter {
  state counter : Number = 0

  fun render : Html {
    <Counter
      onIncrement={() : Promise(Never, Void) => { next { counter = counter + 1 } }}
      onDecrement={() : Promise(Never, Void) => { next { counter = counter - 1 } }}
      counter={counter}/>
  }
}

/* A suite is a group of tests. */
suite "Counter" {
  test "Displays counter" {
    /*
    We are using the Test.Html module for testing. The with keyword
    allows us to call its functions in the current scope.
    */
    with Test.Html {
      <TestCounter/>
      |> start()
      |> assertTextOf("span", "0")
    }
  }

  test "Clicking on increment increments the counter" {
    with Test.Html {
      <TestCounter/>
      |> start()
      |> assertTextOf("span", "0")
      |> triggerClick("button:last-child")
      |> assertTextOf("span", "1")
    }
  }

  test "Clicking on decrement decrements the counter" {
    with Test.Html {
      <TestCounter/>
      |> start()
      |> assertTextOf("span", "0")
      |> triggerClick("button")
      |> assertTextOf("span", "-1")
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

要运行测试，只需用测试命令调用二进制文件:`mint test`

### 比较

两种实现都是集成测试:

*   在 JavaScript 中运行测试我们需要第三方包，在 Mint 中它是内置的
*   在 JavaScript 中，Jest 使用 nodejs 运行测试，而 Mint 在实际的浏览器中运行测试
*   Mint 中有一个测试服务器，允许在浏览器中进行手动测试

### 统计数据

| 名字 | Java Script 语言 | 铸造 |
| --- | --- | --- |
| 代码行 | **62** | **47** |
| 第三方软件包 | **3** `jest` `enzyme` `enzyme-adapter-react-16` | **0** |

# 错误信息

我们的开发环境应该提供易于理解的错误消息。

### JavaScript

在 JavaScript 中，我们需要处理三种类型的错误，其中只有一种可以在浏览器中显示:

*   来自 Webpack 的编译时错误
*   流程中的类型错误
*   来自浏览器的运行时错误

为了启用编译时错误，我们需要在我们的`webpack.config.js` :
中添加下面一行

```
...
devServer: {
  overlay: true
... 
```

Enter fullscreen mode Exit fullscreen mode

[![Webpack Error](img/bfe6af0df55b551142bcb8a3c7b5ca0c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1uwh0i5A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cy54bdmr5obhn9peknk9.png)

流错误只能在运行二进制文件后显示在控制台中:

[![Flow Error](img/0866ad0e84f4a6f01e6c3745a46d586a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gNzCjxzt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qahizh3w69wslpsq25er.png)

在浏览器控制台中可以看到运行时错误。

### 薄荷

在 Mint 中有许多错误类型(语法、类型等...)但它们都以相同的方式显示在控制台(仅运行控制台命令时)或浏览器中，但内容完全相同:

[![Mint Browser Error](img/5deea04b96f16352492153d68c8681f2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sevNHG8L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/p8ynymgipizqwbzb40qt.png)

[![Mint Console Error](img/f2564b55393bf6653bfef45249d18779.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Kb3abFOk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/19hxd5bir8wi134be0l9.png)

在浏览器控制台中可以看到运行时错误，尽管由于类型系统的原因它们不应该发生。

### 比较

新的错误倾向于提供更多的信息，例如，当错误调用一个函数时，消息显示该函数被调用的位置及其源代码。

# 格式化

将我们的源代码格式化成特定的风格是一种标准的做法，我们的环境应该支持这一点。

### JavaScript

为了格式化我们的 JavaScript 文件，我们只需要安装更漂亮的，它可以处理许多语言，而不仅仅是 JavaScript。

安装后，我们只需要调用`prettier src/* --write`，我们的代码就被格式化了。

### 薄荷

Mint 有一个内置的格式化程序，可以用`mint format`命令调用，开发服务器也可以配置为当文件用`--auto-format`参数改变时格式化文件。

### 比较

在两种语言中格式化我们的代码同样简单唯一的区别是在 JavaScript 中它是第三方工具。

### 统计数据

| 名字 | Java Script 语言 | 铸造 |
| --- | --- | --- |
| 代码行 | **0** | **0** |
| 第三方软件包 | **1**T0】 | **0** |

# 建立生产文件

我们的应用程序已经准备好部署到生产环境中，但是为此我们需要生成压缩和缩小的文件。此外，这将是很好的从一个基本图标生成图标。

### JavaScript

为了缩小我们的 JavaScript 输出，我们将通过`uglifyjs-webpack-plugin`插件使用 [UglifyJs](https://github.com/mishoo/UglifyJS2) 。为了生成图标，我们需要安装`html-webpack-plugin`和`favicons-webpack-plugin`插件。

安装之后，我们需要在我们的`webpack.config.js`文件中配置它们:

```
/* Import them. */
const FaviconsWebpackPlugin = require('favicons-webpack-plugin');
const UglifyJsPlugin = require('uglifyjs-webpack-plugin');
const HtmlWebpackPlugin = require('html-webpack-plugin');
...
  plugins: [
    new FaviconsWebpackPlugin('../public/logo.png'),
    new HtmlWebpackPlugin({
      template: '../public/index.html'
    })
  ],
  optimization: {
    minimizer: [
      new UglifyJsPlugin()
    ]
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们还需要为构建`webpack.prod.config.js` :
创建一个新的配置

```
const common = require('./webpack.config.js');

module.exports = {
  ...common,
  mode: 'production'
} 
```

Enter fullscreen mode Exit fullscreen mode

之后，只需调用`webpack --config webpack.prod.config.js`将我们的生产文件构建到`dist`目录中。

### 薄荷

目前 Mint 不缩小生产文件，尽管它可以用任何工具缩小。计划很快实施。

要生成 favicons，我们只需要为应用程序指定图标(该功能需要安装 ImageMagick):

```
...
  "application": {
    ...
    "icon": "assets/logo.png"
  }
... 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们可以使用`mint build`命令将生产文件生成到`dist`目录中。

#### 奖励:渐进式网络应用

Mint 默认为所有应用程序生成`manifest.json`和一个服务工人，我们需要做的就是在`mint.json`文件中设置相应的字段:

```
{
  ...
  "application": {
    ...
    "icon": "assets/logo.png",
    "name": "Counter",
    "orientation": "portrait",
    "display": "standalone",
    "theme-color": "#FFF",
    "meta": {
      "viewport": "width=device-width, initial-scale=1, shrink-to-fit=no",
      "description": "A simple counter example",
      "charset": "utf-8"
    }
  }
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

再次构建并部署到一个安全的网站(使用 https)后，它就可以安装在手机上了。

### 比较

使用 JavaScript 构建生产文件需要第三方工具，在 Mint 中这是一个内置特性。

### 统计数据

| 名字 | Java Script 语言 | 铸造 |
| --- | --- | --- |
| 代码行 | **14** | **1** |
| 第三方软件包 | **3**
`html-webpack-plugin`
`uglifyjs-webpack-plugin`
`favicons-webpack-plugin` | **0** |

# 总体统计

在这里，您可以找到两个实现的最终统计数据(它们都是在相同的条件下在一台机器上收集的):

| 名字 | Java Script 语言 | 铸造 |
| --- | --- | --- |
| 代码行(`wc -l`) | **第 408 章** | **258** |
| 生产构建时间 | **21.36 秒** | **854 毫秒** |
| 使用的命令行实用程序 | **6** | **1** |
| 第三方软件包 | **24** | **1** |
| 所有已安装的软件包 | **第 1426 章** | **1** |
| 包装尺寸(`node_modules` / `.mint`) | **296 MB** | **744 kB** |
| 捆绑大小(`dist`目录) | **1.3 MB** | **315 kB** |
| 束尺寸(`.js`) | **212 kB** (缩小) | **204 kB** (未提交)
**176 kB** (缩小) |

正如你在上面看到的，主要的区别在于第三方包和包的大小。在 JavaScript 中它更大，因为它也包含工具。

# 结束思绪

这部分很可能是主观的(因为我是 Mint 的作者),所以照原样接受吧。

在我看来，这确实显示了今天的前端开发是如何过度设计的(如此简单的应用程序需要 1426 个包？？).同样，开发人员需要学习 web 开发的基础知识(HTML、CSS、JavaScript)是不够的，他们还需要学习所有这些依赖项，这些依赖项都有自己的文档，可能会让人不知所措。

这就是我创建 Mint 的基本原因，这样可以更容易地编写 web 应用程序，而不会有任何麻烦。我希望这篇文章能展示 Mint 能做什么，以及它有多容易使用。

如果我激起了你的兴趣，你可以在[网站](https://www.mint-lang.com)上找到开始使用的所有信息，或者如果你喜欢投稿，请查看 Github 资源库:

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [薄荷-郎](https://github.com/mint-lang) / [薄荷](https://github.com/mint-lang/mint)

### 🍃一种令人耳目一新的前端 web 编程语言。

<article class="markdown-body entry-content container-lg" itemprop="text">

# [![Mint](img/d7f8604681aad5847b954929f5718067.png)T2】](https://github.com/mint-lang/mintsrc/assets/mint-logo.svg)

一种令人耳目一新的前端 web 编程语言，旨在从语言层面解决**单页面应用(spa)**最常见的问题:

*   可重用组件
*   式样
*   按指定路线发送
*   全局和本地状态处理
*   可能失败的同步和异步计算

同时关注:

*   开发者快乐
*   快速编译
*   可读性

## 项目状态

该项目正在开发中，我们仍在调整语言和标准库。

有两个更大的应用可以用作示例/学习材料:

*   [real world . io](https://github.com/gothinkster/realworld)(~ 3300 LOC)——[DEMO](https://mint-realworld.netlify.com)[来源](https://github.com/mint-lang/mint-realworld)
*   旧造币厂网站(~3100 LOC) [来源](https://github.com/mint-lang/mint-website)

如果您能参加这个简短的[调查](https://cybergusztav.typeform.com/to/J5mBcK)来提供您对 Mint 的反馈，那就太好了。

## 安装

[遵循这些说明](https://www.mint-lang.com/install)

## 证明文件

*   [学习指南](https://www.mint-lang.com/guide)
*   [API 文件](https://www.mint-lang.com/api)

## 社区

问题或建议？在[上询问吉特频道](https://gitter.im/mint-lang/Lobby)、[不和谐](https://discord.gg/KvKr5UZKhY)，或者[频谱](https://spectrum.chat/mint-lang)。

此外，访问 [Awesome Mint](https://github.com/egajda/awesome-mint) ，查看更多指南、教程和示例。

## 贡献的

…

</article>

[View on GitHub](https://github.com/mint-lang/mint)

这两个应用程序的完整代码可以在这里找到(在不同的分支上):

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [薄荷-郎](https://github.com/mint-lang) / [薄荷-vs-x](https://github.com/mint-lang/mint-vs-x)

<article class="markdown-body entry-content container-lg" itemprop="text">

# 薄荷 Vs. X

这个存储库包含一个示例应用程序的实现，该应用程序在比较 Mint 和流行的前端框架/语言的“Mint vs X”博客帖子中使用

## 履行

所有的实现都存在于不同的分支中:

*   [Mint](https://github.com/mint-lang/mint-vs-x/tree/mint)-Mint 中的基础实现
*   使用 React + Redux 的 JavaScript 实现

## 博客帖子

*   [Mint vs JavaScript (stack)](https://dev.to/gdotdesign/mint-vs-javascript)

</article>

[View on GitHub](https://github.com/mint-lang/mint-vs-x)

此外，如果您对更多用 Mint 编写的应用程序感兴趣，我建议您查看以下资源库:

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [薄荷-郎](https://github.com/mint-lang) / [薄荷-网站](https://github.com/mint-lang/mint-website)

### Mint 编程语言的网站

<article class="markdown-body entry-content container-lg" itemprop="text">

# 薄荷网站

这是 Mint 编程语言网站的源代码。

[![Screenshot of the website](img/317124a18ed4fc2a04d0b433df6bd234.png)T2】](https://github.com/mint-lang/mint-website./screenshots/01.png)

## 砍

网站是用薄荷搭建的。按照以下步骤启动并运行:

1.  安装薄荷([https://www.mint-lang.com/install](https://www.mint-lang.com/install))
2.  克隆存储库
3.  使用`mint install`安装依赖项
4.  启动开发服务器`mint start`
5.  就是这样！

## 贡献的

欢迎您贡献任何东西:

1.  叉吧([https://github.com/mint-lang/mint-website/fork](https://github.com/mint-lang/mint-website/fork))
2.  创建您的特性分支(git checkout -b my-new-feature)
3.  提交您的更改(git commit -am '添加一些功能')
4.  推到分支(git push origin my-new-feature)
5.  创建新的拉式请求

## 许可证

用它

</article>

[View on GitHub](https://github.com/mint-lang/mint-website)

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [薄荷-郎](https://github.com/mint-lang) / [薄荷-现实世界](https://github.com/mint-lang/mint-realworld)

### https://realworld.io 前端的 Mint 实现。

<article class="markdown-body entry-content container-lg" itemprop="text">

# 就像真实世界一样

> ### Mint code base containing real-world examples (CRUD, auth, advanced mode, etc.) conforms to [real-world](https://github.com/gothinkster/realworld) specification and API.

### [试玩](https://realworld.mint-lang.com) [现实世界](https://github.com/gothinkster/realworld)

创建这个代码库是为了展示用 [**Mint**](https://www.mint-lang.com) 构建的完全成熟的 fullstack 应用程序，包括 CRUD 操作、认证、路由、分页等等。

我们竭尽全力坚持 **Mint** 社区风格指南&最佳实践。

有关如何与其他前端/后端一起工作的更多信息，请访问 [RealWorld](https://github.com/gothinkster/realworld) repo。

# 它是如何工作的

这个实现只使用了 **Mint** 语言和它的标准库，没有任何第三方依赖。

要了解更多关于薄荷的信息，请查看 T2 指南

# 差异

与其他实现有一些不同:

*   因为 Mint 有一个内置的 HTML 元素样式，我们想展示它，所以应用程序的设计与原来的有很大的不同
*   最终结果也是一个进步的网络…

</article>

[View on GitHub](https://github.com/mint-lang/mint-realworld)