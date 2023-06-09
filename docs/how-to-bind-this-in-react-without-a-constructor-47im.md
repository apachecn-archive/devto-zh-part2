# 如何在没有构造函数的情况下在 React 中绑定“this”

> 原文：<https://dev.to/tiffany/how-to-bind-this-in-react-without-a-constructor-47im>

[![](img/abfbafbd3ed410127eb8375d6a94167f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nrngb8XJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a8j2ie73e5ux4fspnog0.jpg)

*这篇文章最初发表在[我的博客](https://tiffanywhite.tech/bind-this-without-constructor/)T3 上。*

`this`在 React 中是对当前组件的引用。通常 React 中的`this`被绑定到它的内置方法，所以当你想更新状态或者在表单上使用事件处理程序时，你可以这样做:

```
<input type="text" ref={this.someInput} required placeholder="This is a Method" defaultValue={getSomeMethod()}/> 
```

Enter fullscreen mode Exit fullscreen mode

其中`this.someInput`将状态传递给正在渲染的 React 组件。

然而不幸的是，React 没有将`this`自动绑定到自定义方法。这意味着，如果我想通过获取一些输入来操作 DOM，而这是用普通的 JavaScript 无法做到的，我会创建一个`ref`来做我想做的任何 DOM 修补。

但是因为 React 不自动绑定`this`，下面的代码在记录时会输出 undefined】

```
someInput = React.createRef();
  renderSomeInput (event) {
    event.preventDefault();
    const someFunction = this.someInput.current.value;
    console.log(this);
  } 
```

Enter fullscreen mode Exit fullscreen mode

为了避免这种情况，我们可以使用`constructor`函数来呈现组件或者获得我们想要的状态:

```
class SomeCompenent extends React.Component {
   constructor() {
    super();
    this.renderSomeInput.bind(this);
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

虽然这是在组件上呈现 ref 的一种不错的方式，但是如果您想在一个组件中绑定几个自定义方法呢？那会变得相当混乱...

```
class SomeCompenent extends React.Component {
   constructor() {
    super();
    this.renderSomeInput.bind(this);
    this.renderSomeInput.bind(this);
    this.renderSomeInput.bind(this);
    this.renderSomeInput.bind(this);
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

你明白了。

相反，我们可以将`this`绑定到自定义的 React 方法，方法是通过将一个方法分配给一个箭头函数来声明它:

```
class SomeCompenent extends React.Component {

someInput = React.createRef();
  renderSomeInput = (event) =>  {
    event.preventDefault();
    const someFunction = this.someInput.current.value;
    console.log(this);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这将允许我们将`this`的值绑定到`SomeComponent`组件。

## 希望有帮助！

ES6 给了我们类和构造函数，React 马上就利用了它们。你并不总是需要构造函数，知道什么时候用什么时候不用会有所帮助。