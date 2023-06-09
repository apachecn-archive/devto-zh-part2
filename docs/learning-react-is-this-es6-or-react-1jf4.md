# 学习 React:这是 ES6 还是 React？

> 原文：<https://dev.to/kball/learning-react-is-this-es6-or-react-1jf4>

在最近一篇关于他学习 React 的努力的博客文章中，Brad Frost 强调了我从一个又一个开发者那里听到的一个问题:

> 因为 React 和 ES6 纠缠在一起，我很难消化任何给定的代码块。React 是怎么回事？什么只是 ES6 JavaScript？

了解什么是什么可以极大地帮助我们弄清楚要搜索什么以及在哪里学习它。下面我分解了很多你会在 React 教程和例子中看到的语法。

对于每一个特性，我都展示了几个它可能是什么样子的例子，确定了它来自哪里，给你一个什么叫做什么和它做什么的快速概述，并链接到一些可以帮助你了解它的资源。

### 箭头功能(ES6)

```
// example 1
this.handleChange = () => {
  this.doSomething();
  return;
}

// example 2
this.array.map(item => item.name); 
```

Enter fullscreen mode Exit fullscreen mode

[箭头功能](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)是 ES6 中最受欢迎的新功能之一。他们将一个巧妙的新语法与一个隐式的`bind`结合起来，使得误差函数中的`this`等于定义它们的上下文中的`this`。

新语法基本上由圆括号中的参数列表、后面的“粗箭头”(= >)和函数体组成。

为了简洁起见，如果只有一个参数，可以忽略括号。此外，如果函数体是返回值的单个语句，您可以忽略它周围的`{}`括号和`return`语句，从而得到上面第二个示例中的紧凑语法。这个例子从一个对象数组开始，返回每个对象的`name`值数组。

对于那些来自 Python 或 Ruby 等其他动态语言的函数，箭头函数的语义与 lambda 函数非常相似。

### 承诺(ES6)

```
api.get(someUrl).then((value) => {
    console.log(value);
}).catch((error) => {
    console.log(error);
}); 
```

Enter fullscreen mode Exit fullscreen mode

[承诺](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)是处理异步的一种替代机制，而不是传递回调函数。任何时候你在代码中看到`then`，你都可以非常确定你在处理一个承诺。

承诺本质上是一个价值的占位符。该值可能马上就存在，或者可能需要等到某个异步操作(如 AJAX 调用)之后才能被解析。不管怎样，如果您对一个承诺调用`then`并传递一个函数，当值可用时，该函数将被调用。

这种模式导致了一种比链式回调更自然的处理异步编程的模式，因为您可以传递、返回、链接这些对象，以及各种有趣的东西。

下面很好的介绍一下它们的用法:[ES6 承诺简单指南](https://codeburst.io/a-simple-guide-to-es6-promises-d71bacd2e13a)。

### 设和常数(ES6)

```
const temperature = this.props.temperature;
let iterator = 1; 
```

Enter fullscreen mode Exit fullscreen mode

当 JavaScript 首次标准化时，只有一个关键字用于声明变量:`var`。`var`的语义也有点奇怪——作用域很奇怪，你可以用人们难以理解的方式隐藏变量。

ES6 解决了这个问题，定义了两个新的关键字来用更简单的语义声明变量。`let`和`const`都有*块范围*，这更接近大多数开发人员的自然预期。`const`关键字暗示引用不能改变(尽管如果引用是一个对象或数组，内部可能会改变——这不是不变性)，而`let`允许引用改变。

大多数开发人员已经习惯于只使用`let`和`const`，其中`var`是遗留代码的强烈代码味道。

你可以在 Wes Bos 的这篇精彩帖子里了解更多的不同: [Let vs Const](https://wesbos.com/let-vs-const/) 。

### 对象析构(ES6)

```
// example 1
const { props, state } = this;

// example 2
const [first, second] = array; 
```

Enter fullscreen mode Exit fullscreen mode

[对象析构](https://hacks.mozilla.org/2015/05/es6-in-depth-destructuring/)是一种机制，以一种看起来像对象字面语法的方式，直接将对象或数组的属性赋给变量。

到目前为止，就我所见，最常见的用法是挑选一个对象的特定属性用于函数内部，如上面的第一个例子。

析构也支持默认值，并且可以在函数参数内部使用，从而产生如下强大的模式:[现代 JavaScript 中的优雅模式:RORO](https://medium.freecodecamp.org/elegant-patterns-in-modern-javascript-roro-be01e7669cbd) 。

### 类(ES6)

```
class MyClass extends BaseClass {
  constructor() {
    super();
    this.doSomething();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

`class`和`extends`关键字和特殊功能`constructor`是 [ES6 类](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)的一部分。这些类是以面向对象的方式开发 JavaScript 的新语法，感觉更类似于其他面向对象语言。

这种新语法不是面向对象的新方法——在它的下面，JavaScript 仍然使用原型继承模型——但是它们使得推理正在发生的事情变得更加容易。

这里有几篇对 Javascript 类有不同看法的好文章:[JavaScript:Prototype vs Class](https://medium.com/@parsyval/javascript-prototype-vs-class-a7015d5473b)和[让我们揭开 JavaScript 的“新”关键字](https://medium.freecodecamp.org/demystifying-javascripts-new-keyword-874df126184c)的神秘面纱。

### 道具和状态(反应过来)

```
constructor(props) {
  super(props);
  this.state = {date: new Date()};
} 
```

Enter fullscreen mode Exit fullscreen mode

`props`和`state`的概念由 React 形式化，并逐渐被整个面向组件的框架所接受。

首先，`props`指的是传入组件并影响组件行为的数据。组件不能直接改变`props`——它不拥有这个数据——但是它*对道具的改变做出反应*,所以如果道具改变了，组件会重新渲染。

接下来，`state`是指组件本身控制的数据。这种状态可能直接影响组件的布局，或者只是用于内部簿记——这取决于组件。该组件拥有这些数据并可以对其进行修改，但是*也会自动对状态变化做出反应，并在状态变化时重新渲染。变更需要通过一个指定的方法`setState`来实现，这将在下面介绍。*

下面是道具和状态的精彩介绍: [ReactJS:道具 vs .状态](http://lucybain.com/blog/2016/react-state-vs-pros/)。

### setState(反应)

```
this.setState({isLoggedIn: true}); 
```

Enter fullscreen mode Exit fullscreen mode

除了在组件的构造函数中，对 React 组件的`state`的更改需要通过`setState`方法进行。这允许 React 批量处理这些更改，并在更改时自动触发组件的重新呈现。

### 展开/静止(ES6)

```
// example 1
const image = { ...image, ...attributes };

// example 2
this.setState({ ...this.state.image, ...attributes }); 
```

Enter fullscreen mode Exit fullscreen mode

[Spread 操作符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)，又名`...`操作符，本质上是接受一个数组或一个对象，并将其扩展到它的项目集。这让你可以做一些有趣的事情，比如合并对象，或者用非常紧凑的语法创建对象的浅层副本。

我在这里写了 spread 操作符的分解:[理解 JavaScript 中的 Spread 操作符](https://dev.to/kball/understanding-the-spread-operator-in-javascript-2c3m-temp-slug-7893040)。

### 模板文字(ES6)

```
const str = `What's going on, ${name}?`; 
```

Enter fullscreen mode Exit fullscreen mode

模板文字是 JavaScript 中创建字符串的一种新方式。本质上，它们允许您将 javascript 直接插入到字符串中，而不需要像以前那样做大量的字符串连接和添加。

模板文字是反斜线

``, and then anything inside of a`$ { } '内的字符串，被计算为 JavaScript。

最常见的用法是简单地放入变量，但是任何一个 JavaScript 表达式都可以在`${}`中，包括函数——返回值将被注入到字符串中。

这里有一个关于 CSS 技巧的关于模板字面量及其相关特性标记模板的很好的介绍:[模板字面量](https://css-tricks.com/template-literals/)。

### 生命周期挂钩(React)

```
 componentWillMount() {
}

componentDidMount() {
}

componentWillReceiveProps() {
}

componentWillUnmount() {
} 
```

Enter fullscreen mode Exit fullscreen mode

像这样的函数——在 React 中以一致的方式命名，以`component`开头，然后是 Will 或 Did，以及一些描述，被称为生命周期挂钩。

这些函数允许您在组件生命周期的特定时间执行逻辑。React 自动为你做了很多，但有时你需要在过程中的特定点推动它做更多。

一些最常见的用途是在组件挂载时加载数据，或者为某个本地状态获取 props 的副本。

下面介绍一些最常见的生命周期挂钩: [30 天的反应:生命周期挂钩](https://www.fullstackreact.com/30-days-of-react/day-7/)。

### JSX(反应)

```
 // example 1
const element = <h1>Hello, world!</h1>;

// example 2
const element = (
  <h1>
    Hello, {formatName(user)}!
  </h1>
); 
```

Enter fullscreen mode Exit fullscreen mode

JSX 是 JavaScript 的语法扩展，允许将类似 HTML 的模板直接嵌入到 JavaScript 中。

这是 React 最有争议的部分之一，它让许多长期的 web 开发人员望而却步，同时被倡导者吹捧为极大地提高了生产率。没有 JSX 也可以使用 React，但是推荐使用 JSX。

了解 JSX 的两个要点。首先，如果只有一行标记(像上面的第一个例子)，可以直接将标记放入 JavaScript 中，而如果有多行，则需要用括号将其括起来。

第二，单括号内的任何内容，`{}`都被认为是 JavaScript。这允许将逻辑或动态值轻松嵌入到您的标记中。

这里有一个关于 JSX 的深度教程: [JSX 深度](https://reactjs.org/docs/jsx-in-depth.html)。

### 包装完毕

我相信 React 和 ES6 都有更多令人困惑的元素。如果你遇到一个我没有提到的问题，不要犹豫，在下面的评论中联系我，或者在 Twitter 上联系我，我会帮助你理解它并把它添加到列表中。

* * *

又及——如果你对这类话题感兴趣，我会发送一份名为“[星期五前端](https://zendev.com/friday-frontend.html)”的每周时事通讯。每周五我会发出 15 个链接，链接到 CSS/SCSS、JavaScript 和其他各种精彩的前端新闻中的最佳文章、教程和公告。在这里报名:【https://zendev.com/friday-frontend.html T2】