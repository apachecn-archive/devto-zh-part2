# 学习 React -第 3 部分-组件、状态和道具

> 原文：<https://dev.to/felipegalvao/learn-react---part-3---components-state-and-props-1h0g>

[最初发表在我的博客上](http://felipegalvao.com.br/blog/2018/09/24/learn-react-components-state-and-props/)

嘿伙计们。在我们上一篇学习 React 的帖子中，我们谈到了 JSX，这是一种 Javascript 语法扩展，它使得用 React 构建接口变得更加容易，从而产生了看起来像 Javascript 和 HTML 混合的代码。

在本帖中，我们将讨论 React 最酷的概念之一，组件。我们将学习如何创建组件、导入组件以及围绕这些组件组织应用程序的数据。

作为一个例子，我们将创建一个简单的带有递增和递减按钮的计数器。这个简单的应用程序将允许我们将所有与组件、状态和道具相关的概念付诸实践。

同样，我们将使用我们在 Learn React 系列的第一篇文章中使用的设置。你可以[点击这里](https://dev.to/felipegalvao/simple-setup-for-a-react-application-with-webpack-4--css--sass-bef)打开它，在这里你可以找到 Github 的资源库(或者你可以只[点击这里](https://github.com/felipegalvao/webpack-4-react-boilerplate)直接访问)，你可以克隆并跟上这个帖子。

## 成分

像往常一样，我将把我们将从这里开始的基本代码。这就是他现在所拥有的，如果你克隆了存储库就表明:

```
import React from "react";
import ReactDOM from "react-dom";

const Index = () => {
  return <div>Hello React!</div>; };

ReactDOM.render(<Index />, document.getElementById("index")); 
```

好的，首先你需要知道的是，使用上面的代码，你已经创建了你的第一个组件。当我们定义`Index`并让它返回一个 JSX 元素时，我们使用了创建组件的两种主要方式之一，即通过函数。好了，让我们开始组织我们的应用程序，将这个`Index`组件移动到它自己的文件中。我们将有一个主要组件，其中我们将导入和使用我们将在这篇文章中创建的其他组件。首先，在`src`文件夹中，我们将创建`components`文件夹，然后，在这个文件夹中，我们将创建`App.js`文件。这将是我们的主要组成部分。注意，这个文件的名字不是一个规则，你不需要叫它`App.js`，这只是个人喜好:

```
import React from "react";
import ReactDOM from "react-dom";

const App = () => {
  return <div>Hello React!</div>; };

export default App; 
```

现在，让我们导入并使用这个组件。在位于`src`文件夹内的`index.js`文件中，我们将删除`Index`定义，然后导入我们刚刚创建的`App`组件。然后，我们将它传递给`render`方法:

```
import React from "react";
import ReactDOM from "react-dom";

import App from "./components/App";

ReactDOM.render(<App />, document.getElementById("index")); 
```

如果你的 Webpack 服务器还没有运行，在你的终端中运行`yarn run start`，然后在 [中打开你的浏览器 http://localhost:8080/](http://localhost:8080/) (或者等待它自动打开，取决于你的 Webpack 设置)，惊叹你的应用没有变化。这是意料之中的，因为我们还没有改变任何东西，我们只是移动了一些东西，将一个组件放入它自己的文件中。

现在，让我们创建一个新组件，它将负责显示当前计数。在这第一步中，我们将手动定义当前计数，但是我们很快就会知道如何使它动态化。首先，让我们在`components`文件夹中创建`CountDisplay.js`文件。这是该文件/组件的初始代码:

```
import React from "react";
import ReactDOM from "react-dom";

const CountDisplay = () => {
  return <p>The count is 0</p> };

export default CountDisplay; 
```

正如我们在上一篇关于 JSX 的文章中看到的，我们可以使用`curly braces ({})`将变量的值包含在元素中。但是，如果我们可以将有关当前计数值的信息传递给这个组件，会怎么样呢？

## 小道具

Props，properties 的缩写，基本上是我们传递给组件的数据。它可以是一个字符串，一个数字，甚至一个函数。然后，接收该值的组件可以使用该值。首先，我们将所需的数据传递给组件，将其定义为一个属性，但使用我们想要的名称。让我们将`currentCount`传递给在`App.js`文件中被调用的`CountDisplay`组件。对于一个数值，我们在花括号内传递它。让我们看看它会是什么样子。：

```
import React from "react";
import ReactDOM from "react-dom";

import CountDisplay from "./CountDisplay";

const App = (props) => {
  return <CountDisplay 
    currentCount={3}
  />; };

export default App; 
```

现在，让我们在接收它的组件中获取这个值，`CountDisplay`。为此，我们需要做的第一件事是将`props`作为定义组件的函数的参数。然后，我们将可以访问传递给这个组件的任何道具。让我们运行一个`console.log`来看看`props`是什么样子的。`CountDisplay.js`文件中的代码将是这样的:

```
import React from "react";
import ReactDOM from "react-dom";

const CountDisplay = (props) => {
  console.log(props);
  return <p>The count is 0</p>; };

export default CountDisplay; 
```

正如您可以注意到的，当您刷新浏览器选项卡并打开它的控制台时，我们得到的是一个带有刚刚传递给这个组件的`countDisplay prop`的对象。让我们做一个快速测试，向这个组件传递一个额外的`prop`,如下所示:

```
const App = (props) => {
  return <CountDisplay 
    currentCount={3}
    name="Felipe"
  />;
}; 
```

当您再次刷新浏览器选项卡时，您将在浏览器的控制台中看到带有值为`currentCount`和`name`的`props`对象，即我们传递给该组件的`props`。

现在我们可以删除执行`console.log`的行和通过`name prop`的行，因为我们只是用它来做测试。然后，我们可以获取`CountDisplay`组件中的`currentCount`值，并将其替换到显示计数的段落中。这是你将拥有的:

```
import React from "react";
import ReactDOM from "react-dom";

const CountDisplay = (props) => {
  return <p>The count is { props.currentCount }</p>; };

export default CountDisplay; 
```

现在，您一定会问自己这如何帮助我们，因为我们刚刚改变了手动定义当前计数的位置。好了，现在我们来谈谈`state`。

## 状态

`State`就像道具一样，是组件要使用的数据。同样，它可以是一个字符串，一个数字，一个对象。与状态的区别在于，它对于组件是私有的，并且完全由组件控制。为了利用`state`，我们需要学习创建组件的第二种方法，即通过类。因此，我们将构建类组件，而不是我们到目前为止一直在构建的函数组件。为了创建一个类组件，我们需要扩展`React.Component`类。将我们的`App.js`功能组件转换为类组件，它将是这样的:

```
import React from "react";
import ReactDOM from "react-dom";

import CountDisplay from "./CountDisplay";

class App extends React.Component {
  render() {
    return (
      <CountDisplay 
        currentCount={3}
      />
    );
  }
}

export default App; 
```

现在，为了定义这个组件的`state`，我们必须为这个类定义一个`constructor`，在它的内部，使用`this.state`来定义一个对象，这个对象将具有状态的初始值:

```
import React from "react";
import ReactDOM from "react-dom";

import CountDisplay from "./CountDisplay";

class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = {currentCount: 1};
  }

  render() {
    return (
      <CountDisplay 
        currentCount={3}
      />
    );
  }
}

export default App; 
```

我在状态中将当前计数定义为 1，但是请注意，我们仍然将固定值 3 传递给组件，因此，现在没有任何变化。要使用保存在`state`中的值，我们需要做的就是用`this.state`获取它。对于这一步，代码如下:

```
import React from "react";
import ReactDOM from "react-dom";

import CountDisplay from "./CountDisplay";

class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = {currentCount: 1};
  }

  render() {
    return (
      <CountDisplay 
        currentCount={this.state.currentCount}
      />
    );
  }
}

export default App; 
```

现在，让我们来理解如何操作`state`首先，让我们创建两个按钮，一个增加，另一个减少。让我们使用`button`元素，您的`App.js`文件将如下所示:

```
import React from "react";
import ReactDOM from "react-dom";

import CountDisplay from "./CountDisplay";

class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = {currentCount: 1};
  }

  render() {
    return (
      <div>
        <button>
          +
        </button>
        <button>
          -
        </button>
        <CountDisplay 
          currentCount={this.state.currentCount}
        />
      </div>
    );
  }
}

export default App; 
```

现在，`button`元素上有一个名为`onClick`的属性。有了这个属性，我们可以定义当一个按钮被点击时会发生什么。让我们使用这个属性来运行一个函数，该函数将更新我们的`state`，根据按下的按钮将值增加或减少 1。首先，让我们创建递增和递减的类方法，然后，相应地将这些方法分配给每个按钮。为了更新这个组件的状态，我们使用了`this.setState`，其中`this`指的是组件实例，我们传递给这个方法一个对象，这个对象带有我们想要更改的键和新值。在这种情况下，我们将使用当前计数加 1 或减 1:

```
import React from "react";
import ReactDOM from "react-dom";

import CountDisplay from "./CountDisplay";

class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = {currentCount: 1};
  }

  handleIncrement(event) {
    this.setState({currentCount: this.state.currentCount + 1});
  }

  handleDecrement(event) {
    this.setState({currentCount: this.state.currentCount - 1});
  }

  render() {
    return (
      <div>
        <button onClick={this.handleIncrement.bind(this)}>
          +
        </button>
        <button onClick={this.handleDecrement.bind(this)}>
          -
        </button>
        <CountDisplay 
          currentCount={this.state.currentCount}
        />
      </div>
    );
  }
}

export default App; 
```

注意，除了将函数放入其中，我们还添加了`bind(this)`。这是必需的，这样我们就可以在函数中使用`this`。如果我们不这样做，`this`将会成为`undefined`的函数，而`setState`将无法工作。至少还有两种方法可以做到这一点。第一个是在`constructor`里面做`bind`。这是结果:

```
import React from "react";
import ReactDOM from "react-dom";

import CountDisplay from "./CountDisplay";

class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = {currentCount: 1};

    this.handleIncrement = this.handleIncrement.bind(this);
    this.handleDecrement = this.handleDecrement.bind(this);
  }

  handleIncrement(event) {
    this.setState({currentCount: this.state.currentCount + 1});
  }

  handleDecrement(event) {
    this.setState({currentCount: this.state.currentCount - 1});
  }

  render() {
    return (
      <div>
        <button onClick={this.handleIncrement}>
          +
        </button>
        <button onClick={this.handleDecrement}>
          -
        </button>
        <CountDisplay 
          currentCount={this.state.currentCount}
        />
      </div>
    );
  }
}

export default App; 
```

还有一种方法，箭头函数。如果你使用箭头函数，你不需要`bind`，因为箭头函数已经自动完成了。像这样:

```
import React from "react";
import ReactDOM from "react-dom";

import CountDisplay from "./CountDisplay";

class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = {currentCount: 1};
  }

  handleIncrement(event) {
    this.setState({currentCount: this.state.currentCount + 1});
  }

  handleDecrement(event) {
    this.setState({currentCount: this.state.currentCount - 1});
  }

  render() {
    return (
      <div>
        <button onClick={() => this.handleIncrement()}>
          +
        </button>
        <button onClick={() => this.handleDecrement()}>
          -
        </button>
        <CountDisplay 
          currentCount={this.state.currentCount}
        />
      </div>
    );
  }
}

export default App; 
```

所有这些绑定工作的方法，所以，请随意使用你喜欢的方法。

现在我们的组件正在工作。您可以使用按钮增加和减少计数。但是我们可以稍微重构一下。这个想法是让按钮成为它们自己组件的一部分。让我们看看如何构建我们的应用程序，以便在一个组件上调用一个函数时更新另一个组件的状态。

首先，让我们创建一个`CounterButtons.js`组件。现在，让我们提取`App.js`组件中的内容，并将其传递给我们创建的新组件。我们的`CounterButtons.js`组件将如下所示:

```
import React from "react";
import ReactDOM from "react-dom";

class CounterButtons extends React.Component {
  render() {
    return (
      <div>
        <button onClick={() => this.handleIncrement()}>
          +
        </button>
        <button onClick={() => this.handleDecrement()}>
          -
        </button>
      </div>
    );
  }
}

export default CounterButtons; 
```

在我们的`App.js`组件中，我们将有以下代码:

```
import React from "react";
import ReactDOM from "react-dom";

import CountDisplay from "./CountDisplay";
import CounterButtons from "./CounterButtons";

class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = {currentCount: 1};
  }

  handleIncrement(event) {
    this.setState({currentCount: this.state.currentCount + 1});
  }

  handleDecrement(event) {
    this.setState({currentCount: this.state.currentCount - 1});
  }

  render() {
    return (
      <div>
        <CounterButtons />
        <CountDisplay 
          currentCount={this.state.currentCount}
        />
      </div>
    );
  }
}

export default App; 
```

如果您现在进入浏览器并刷新，您的应用程序将被呈现，但是按钮将不起作用，因为它们不能访问更新`state`的方法。我们要做的是将函数作为`props`传递，并在我们创建的新组件中调用这些函数。我们开始吧。

首先，在`App.js`组件中，让我们通过`CounterButton`组件的`props`传递方法。让我们使用`onIncrement`和`onDecrement`作为`props`的名称，`App.js`组件将是这样的:

```
import React from "react";
import ReactDOM from "react-dom";

import CountDisplay from "./CountDisplay";
import CounterButtons from "./CounterButtons";

class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = {currentCount: 1};
  }

  handleIncrement() {
    this.setState({currentCount: this.state.currentCount + 1});
  }

  handleDecrement() {
    this.setState({currentCount: this.state.currentCount - 1});
  }

  render() {
    return (
      <div>
        <CounterButtons
          onIncrement={this.handleIncrement.bind(this)}
          onDecrement={this.handleDecrement.bind(this)}
        />
        <CountDisplay 
          currentCount={this.state.currentCount}
        />
      </div>
    );
  }
}

export default App; 
```

现在，在`CounterButtons.js`文件中，我们更改按钮上的`onClick`属性来调用通过`props`传递的函数。为了避免不得不使用`bind`，我将使用箭头函数，这样我们可以直接在`onClick`定义上调用函数。这就是`CounterButtons.js`组件的情况:

```
import React from "react";
import ReactDOM from "react-dom";

class CounterButtons extends React.Component {
  render() {
    return (
      <div>
        <button onClick={() => this.props.onIncrement()}>
          +
        </button>
        <button onClick={() => this.props.onDecrement()}>
          -
        </button>
      </div>
    );
  }
}

export default CounterButtons; 
```

在这种情况下，我们的应用程序非常简单，这似乎增加了不必要的复杂性，但在更大更复杂的应用程序中，概念的分离和良好定义的组件的组织是至关重要的。

## 结论

总结一下我们在这篇文章中看到的:

*   创建组件有两种方式，功能组件和类组件
*   道具是传递给组件供其使用的数据
*   状态是一个组件的私有数据，完全由它控制
*   只有类组件可以在 React 中使用状态
*   除了通过道具传递值，还可以传递函数

在这篇文章中，我希望我能够解释 React 中组件、道具和状态的主要概念。有什么问题，就在评论里留下吧。

干杯