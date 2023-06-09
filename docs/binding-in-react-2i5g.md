# 反应中的结合

> 原文：<https://dev.to/superkarolis/binding-in-react-2i5g>

在 React v0.13 发布之前，在 React 组件中使用方法非常简单，因为传统的`React.createClass`会自动将用户定义的方法绑定到正确的`this`上下文中。

但是，自从 React 组件中引入了 ES6 类之后，方法就不再自动绑定了。因此，我们至少有四种方法来处理 React 中的`this`上下文。让我们考虑一下各自的优缺点。

## 在渲染中绑定

这种方法的工作原理是在每个`render`调用中将`this`分配给一个给定的函数。这种方法的缺点是在每次渲染时都要重新分配函数。虽然对于大多数应用程序来说，这样做对性能的影响可以忽略不计，但还是要记住这一点。

```
class LogThis extends Component {
    handleClick() {
        console.log('this is:', this);
    }

    render() {
        return (
            <button onClick={this.handleClick.bind(this)}>
                Click me
            </button>
        );
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 箭头功能在渲染

这种方法利用了 ES6 胖箭头函数，它通过使用词法作用域来帮助保存`this`的上下文。对于那些不熟悉词法范围的人来说，这仅仅意味着一个函数使用了包含 arrow 函数的代码中的`this`。

由于`render`函数中的`this`总是引用包含的 React 组件，而胖箭头函数使用词法作用域，`handleClick`保留组件的`this`。

它仍然有前一种方法的缺点，即在每个重新呈现器上创建不同的回调，因此可能会导致性能问题。

```
class LogThis extends Component {
    handleClick() {
        console.log('this is:', this);
    }

    render() {
        return (
            <button onClick={e => this.handleClick(e)}>
                Click me
            </button>
        );
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 绑定在构造函数中

避免前两种方法性能下降的一种方法是绑定构造函数。这种方法目前被官方 React 文档推荐，也是我在项目中采用的方法。

这种方法的主要缺点是必须在构造函数中重复绑定每个函数。如果要绑定的组件中有几个函数，可能会很快变得难看。

```
class LogThis extends Component {
    constructor(props) {
        super(props);
        this.state = { message: 'Hello, world!' };
        this.handleClick = this.handleClick.bind(this);
    }

    handleClick() {
        console.log('this is:', this);
    }

    render() {
        return (
            <button onClick={this.handleClick}>
                Click me
            </button>
        );
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 类字段

绑定`this`的最终方法是使用实验性的[公共类字段语法](https://babeljs.io/docs/plugins/transform-class-properties/)。

这种方法是最优越的，因为它避免了在每次渲染时重新分配函数的性能问题。当在构造函数中绑定函数时，它也消除了不必要的重复。

这种方法的一个显著缺点是，通过以这种方式声明方法，`handleClick`没有在原型中声明，因此不可能通过`super.handleClick`从派生类中调用。

```
class LogThis extends Component {
    handleClick = () => {
        console.log('this is:', this);
    }

    render() {
        return (
            <button onClick={this.handleClick}>
                Click me
            </button>
        );
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 额外:使用反应-自动绑定

据我所知，React ES6 类中处理绑定的一种非常规方式是使用 npm 模块 [`react-autobind`](https://www.npmjs.com/package/react-autobind) 。它具有在构造函数中绑定的所有优点，另外，您可以通过单独绑定每个函数来避免重复。

缺点是必须导入一个额外的 npm 模块，并且您仍然必须在构造函数中调用`autoBind`。

```
import autoBind from 'react-autobind';

class LogThis extends Component {
    constructor() {
        super();
        autoBind(this);
    }

    handleClick() {
        console.log('this is:', this);
    }

    render() {
        return (
            <button onClick={this.handleClick}>
                Click me
            </button>
        );
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 总结

我个人认为在 render 中使用绑定或箭头函数没有什么真正的好处。就我而言，没有真正的可读性好处，性能受到负面影响，即使可以忽略不计。

因此，如果你愿意使用 Babel `stage-2` preset 并且不必从派生类中调用父函数，我会推荐使用类字段方法来绑定。

否则，我会推荐在构造函数中绑定，要么手动绑定每个函数，要么使用`react-autobind`模块。两者都可以，归结为个人喜好。如果有几个函数要绑定，我会使用`react-autobind`,如果只有一两个，就用传统的方法。