# React 中初始化状态的位置

> 原文：<https://dev.to/dceddia/where-to-initialize-state-in-react-c03>

啊，许多初始化状态的方法…可能会让人困惑。是直接把`state = {...}`放在类里面，还是写一个构造函数，在构造函数里面说`this.state = { ... }`？你需要一个构造函数吗？

## 2 种方式初始化状态

在 React 组件中有两种初始化状态的方法:在构造函数内部，和直接在类内部。这里有几个例子。

## 构造函数内部

构造函数内部的初始化状态如下:

```
class App extends React.Component {
  constructor(props) {
    // Required step: always call the parent class' constructor
    super(props);

    // Set the state directly. Use props if necessary.
    this.state = {
      loggedIn: false,
      currentState: "not-panic"
      someDefaultThing: this.props.whatever
    }
  }

  render() {
    // whatever you like
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

当组件类被创建时，构造函数是第一个被调用的方法，所以它是初始化所有东西的正确地方——包括状态。类实例已经在内存中创建，所以您可以使用`this`来设置它的属性。

这是*的一个地方*，在这里`this.state`在等号的左边是可以接受的。在其他地方，你应该总是使用`this.setState`而不是`this.state.whatever = ...`——这样，React 将知道你已经改变了一些东西，它可以重新呈现组件。

当你写一个构造函数时需要注意的一件重要事情是确保调用父类的构造函数:上面例子中的`super(props)`行。默认的构造函数(当你创建一个类时由 JS 提供)自动调用`super`并传递任何参数。

通过编写自己的构造函数，您覆盖了默认行为，除非您自己调用`super`，否则如果父类需要做一些初始化，这可能会导致错误。

#### 是否需要建造师？

您不需要编写一个，因为 JS 提供了一个默认的构造函数。要了解这是如何工作的，请尝试在您的浏览器控制台中运行这三行代码:

```
class Parent { constructor(arg) { console.log('constructing Parent with', arg) } }
class Child extends Parent {}
new Child(5); 
```

Enter fullscreen mode Exit fullscreen mode

请注意，当您创建一个新的子节点时，它是如何打印“用 5 构造父节点”的，即使子节点没有显式定义的构造函数，也没有用`super(arg)`显式调用父节点。当您没有定义自己的构造函数时，JS 会自动处理这个`super`调用。

## 直接在类内

第二种初始化状态的方法是直接在类定义中使用类属性。看起来是这样的:

```
class App extends React.Component {
  state = {
    loggedIn: false,
    currentState: "not-panic",
    someDefaultThing: this.props.whatever
  }

  render() {
    // whatever you like
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

漂亮又干净！这里需要注意一些事情:

*   没有构造函数
*   直接引用`state`属性。不是`this.state`，只是`state`。
*   范围在类内部，但不在方法内部。
*   还是可以参考`this.props`(和`this.context`)。
*   这是一个类*实例*属性，与静态属性相反，静态属性可能用于属性类型(例如`static propTypes = {...}`)。

当我写这篇文章的时候，class property 语法是第三阶段的提议，所以它还不是 JS 官方规范的一部分。要使用它，你需要启用[巴别塔的类属性转换](https://babeljs.io/docs/plugins/transform-class-properties/)。

但是！如果您正在使用 Create React App 来引导您的项目，它已经打开了 class properties 转换，您现在就可以开始使用这个 class property 语法。

## 哪个好？建造师还是不是？

像所有的事情一样，这取决于你。

我呢，更喜欢阶级属性的干净样子。我不喜欢构造函数的额外样板文件，也不喜欢不得不记得调用`super(props)`(尽管 ESlint 可以提醒你这样做，Create React App 的配置开箱即用)。

您可能已经看到事件处理函数被绑定在构造函数中，并且可能认为需要构造函数来实现这一点。我说的代码是这样的:

```
class Thing extends React.Component {
  constructor(props) {
    super(props);
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick(event) {
    // do stuff
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

class properties 特性支持的另一种语法可以使这个构造函数变得不必要:您可以将一个属性设置为一个 arrow 函数，arrow 函数继承了 class 实例的`this`绑定，因此您不必显式绑定它。看起来是这样的:

```
class Thing extends React.Component {
  // This is all you need to do:
  handleClick = (event) => {
    // do stuff
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这乍一看可能有点奇怪，但是你可以这样想:

```
// This statement:
const add = (a, b) => console.log(a + b);

// Can be thought of as assigning an arrow function to `add`:
const add = arrowFunction;

// where `arrowFunction` is expanded to:
(a, b) => console.log(a + b) 
```

Enter fullscreen mode Exit fullscreen mode

记住这一点，再看一下上面的`class Thing`例子。希望它看起来不那么奇怪。如果你还是讨厌它，给它一点时间，多写一些箭头函数。一开始我也有同样的问题。你的眼睛会适应的:)

[React](https://daveceddia.com/where-initialize-state-react/)中在哪里初始化状态最初由戴夫·塞德迪亚于 2018 年 3 月 29 日在[戴夫·塞德迪亚](https://daveceddia.com)发表。

[代码项目](http://www.codeproject.com)