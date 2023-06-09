# 仅使用 ES5 特性创建 React 组件

> 原文：<https://dev.to/vonheikemen/creating-react-components-using-only-es5-features-52a1>

这篇文章将只是一个友好的提醒，反应“这只是 javascript”。没有构建步骤也可以使用。用它来创建一个微小的交互组件是没问题的。而且不想用也可以不用 ES6+。

### 快速回顾 createElement 函数

React 公开了一个名为 createElement 的函数，它是每个组件的基础。JSX 实际上是翻译成`React.createElement`调用的语法糖。大概是这样的:

```
React.createElement(
    'tagName',       // HTML tag name or a custom component 
    props,           // HTML attributes, custom properties, events, etc...
    children         // a string or a list of components or almost anything
); 
```

Enter fullscreen mode Exit fullscreen mode

### 无状态组件

这种类型的组件只接受`props`并返回一个 React 元素。它们不管理自己的状态，也没有生命周期方法。它们非常适合呈现来自其他来源的数据。如果没有任何构建步骤，它可能看起来像这样:

```
function Welcome(props) {
    return React.createElement('div', null, 'Hello, ' + props.name);
}

ReactDOM.render(Welcome({ name: 'world' }), container); 
```

Enter fullscreen mode Exit fullscreen mode

### 类组件

这些组件可以管理自己的内部状态，并拥有生命周期方法。事情是这样的，在 ES5 中我们不能使用`class`关键字来创建一个组件，而是必须模仿那个行为。

我们首先需要处理的是遗产。通常你会用`class MyComponent extends React.Component`来做这件事。我们将走另一条路，我们将通过用来自`React.Component`的对象覆盖我们组件的`prototype`对象来“强制”继承，就像这样:

```
function MyComponent(props) {
    React.Component.constructor.call(this);

    // ... code
}

MyComponent.prototype = Object.create(React.Component.prototype); 
```

Enter fullscreen mode Exit fullscreen mode

这里与我们的无状态组件不同的是，我们用自定义组件的上下文调用`React.Component`构造函数，并且我们确保原型是来自`React.Component`的那个。有了这些，我们的组件就可以使用生命周期方法和 setState 方法了。

### 示例代码

现在，利用我们新发现的知识，我们可以创建一些组件，而不需要设置构建步骤，也不需要过多担心浏览器支持。让我们做一个简单的计时器:

```
var container   = document.getElementById('app');

// h is short for hyperscript and it makes everything a little bit easier
var h           = React.createElement;

// This is how we inherit methods like setState from React.Component
Timer.prototype = Object.create(React.Component.prototype);

function Timer(props) {
  React.Component.constructor.call(this);
  var self = this;

  self.state = { seconds: 0 };

  self.tick = function() {
    self.setState(function(prevState) {
      return { seconds: prevState.seconds + 1 };
    });
  };

  self.componentDidMount = function() {
    self.interval = setInterval(self.tick, 1000);
  };

  self.componentWillUnmount = function() {
     clearInterval(self.interval);
  };

  self.render = function() {
    return h('div', null, 'seconds: ', self.state.seconds);
  }
}

ReactDOM.render(h(Timer), container); 
```

Enter fullscreen mode Exit fullscreen mode

注意这里我在一个`self`变量中分配了`this`上下文，以避免使用函数原型的`bind`方法。为了保持一致性，我在任何地方都使用它，尽管我认为只有在`self.tick`中才有必要，因为在`setInterval`中使用它时，我会失去`this`的上下文。

### 最后一件事

人们一开始没有注意到的一点是，一个元素的子元素也是参数。您可以传递组件、字符串、对象或函数。说到底`children`是个道具。

我给你看样东西。
[https://codepen.io/VonHeikemen/embed/LBLZOe?height=600&default-tab=result&embed-version=2](https://codepen.io/VonHeikemen/embed/LBLZOe?height=600&default-tab=result&embed-version=2)T2】

### 结论

这很有效，而且没那么糟糕。这只是向您展示了您可以使用 React 和良好的旧脚本标签将其嵌入到任何站点中。另一个例子是，React 组件是函数调用，你可以做任何你能想到的事情。真的，你可以做任何事情，包括搬起石头砸自己的脚，所以要小心。

### 其他资源

[待办事项示例](https://codepen.io/VonHeikemen/pen/YjQWYv)
[定时器示例](https://codepen.io/VonHeikemen/pen/GBEmBM)
[React 没有构建步骤](https://medium.com/@alexkrolick/writing-react-components-for-3rd-party-embedding-50331c18e26)
[React 只是 JavaScript](https://medium.com/yld-engineering-blog/react-is-just-javascript-88600553269c)

* * *

感谢您的阅读。如果你觉得这篇文章有用，并想支持我的努力，请给我买一杯☕咖啡。

[![buy me a coffee](img/9e6f0f1293ebfd51e2e5cab78e6e6a08.png)T2】](https://www.buymeacoffee.com/vonheikemen)