# 类组件与无状态功能组件

> 原文：<https://dev.to/iam_timsmith/class-components-vs-stateless-functional-components-51he>

当我第一次开始学习 React 时，我甚至没有意识到类组件和无状态功能组件之间的区别。我以为它们只是写同一件事的不同方式。

在某些方面，他们是。在许多方面，他们是不一样的。在本文中，我将解释两者之间的区别，以及何时以及为什么使用不同的类型。

### 什么是“类组件”？

类组件是利用 [ES6 类](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)来管理组件各个部分的组件。状态是我们在 React 中经常使用的东西，我会在后面的帖子中写更多。现在，只需要知道我们是如何在组件中管理数据的。除了状态之外，我们还可以创建自定义函数用于我们的组件，并利用生命周期方法。

当我们在组件中存储或管理数据时，这些东西会很有用。诸如此类的案例将是我们的类组件的主要用例。我提供了一个类组件的例子，它将使用 state:
呈现下面的“Hello World”

```
class HelloWorld extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      greeting: "Hello World"
    }
  }
  render() {
    return (
      <div>
        <p>{ this.state.greeting }</p>
      </div>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 什么是“无状态功能组件”？

我知道，我知道。“无状态功能组件”听起来很大很可怕，但我保证:不是的。无状态功能组件只是一个返回 JSX 的函数。它非常简单，但非常有用。

创建无状态功能组件有两种方法。两者都差不多，做同样的事情，只是简洁的问题。我将使用 [ES6 箭头函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)来创建组件。如果你没有用过，我强烈推荐你去看看 ES6。

#### 第一种方式:放入变量中

如果我们将所有的组件放在一个文件中，那么这应该是我们创建无状态功能组件的方式。当我们为每个组件拥有不同的文件时，选择如何简洁地创建我们的功能组件的能力开始发挥作用。下面的代码说明了我们如何在一个变量中创建一个函数组件，并将其导出用于我们应用程序的另一个区域。

```
const HelloWorld = (props) => (
  <div>
    <p>{ props.greeting }</p>
  </div>
);
export default HelloWorld;

===

<HelloWorld greeting="Hello World!" /> 
```

Enter fullscreen mode Exit fullscreen mode

#### 第二种方式:导出函数

当我们在一个文件中有一个无状态的功能组件时，我们不需要给组件命名。我知道，这样可以节省 10 个字符，但是我会尽我所能。我们可以简单地创建函数并像下面的代码那样导出它。

```
export default (props) => (
  <div>
    <p>{ props.greeting }</p>
  </div>
);

===

<HelloWorld greeting="Hello World!" /> 
```

Enter fullscreen mode Exit fullscreen mode

正如您所看到的，这两个功能组件看起来几乎相同，它们做同样的事情。真的只是个人喜好问题。

> 一个小提示:
> 对于 ES6 的箭头函数，我们可以使用花括号，并在里面放一个回车。为了保持简洁，我们
> 也可以把函数写在一行上。如果箭头后面没有花括号，函数
> 将自动返回箭头后面的内容。如果我们返回的 JSX 不止一行，我们可以像上面的代码一样用括号把代码括起来。

#### 该用哪个？

通常，我认为“最佳实践”是尽可能使用无状态功能组件来减少代码膨胀。在 [Syntax.fm](https://syntax.fm/) 上，他们一直在讨论只使用类组件，因为他们发现他们将许多组件从功能性组件改为类组件，并且不想继续转换。除非你正在构建一个庞大的应用程序，否则我不认为这真的会在性能方面造成任何问题，所以这完全取决于你。

我很想听听您对何时使用这些组件的看法。你多久使用一次无状态函数组件和类组件？