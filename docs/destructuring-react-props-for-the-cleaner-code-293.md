# 为 cleaner 代码构造 React 属性

> 原文：<https://dev.to/arnas/destructuring-react-props-for-the-cleaner-code-293>

在 React 中，向组件传递多个属性是很常见的。所以难怪许多 React 组件功能与很少或更多的道具进行交互。

## 类组件

例如，如果我们有一个简单的组件，它在`render`函数中使用了 4 个不同的道具。

```
import React from "react";

class Row extends React.Component {
  state = {
    showEmail: false
  };

  render() {
    return (
      <div>
        <div>
          <span>First Name: {this.props.firstName}</span>
        </div>
        <div>
          <span>Last Name: {this.props.lastName}</span>
        </div>
        {this.state.showEmail && (
          <div>
            <span>Email: {this.props.email}</span>
          </div>
        )}
        <button onClick={this.props.doSomethingAmazing}>Click me</button>
      </div>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以应用 ES6 [**析构赋值**](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment) 来简化代码。

```
import React from "react";

class Row extends React.Component {
  state = {
    showEmail: false
  };

  render() {
    const { firstName, lastName, email, doSomethingAmazing } = this.props;
    return (
      <div>
        <div>
          <span>First Name: {firstName}</span>
        </div>
        <div>
          <span>Last Name: {lastName}</span>
        </div>
        {this.state.showEmail && (
          <div>
            <span>Email: {email}</span>
          </div>
        )}
        <button onClick={doSomethingAmazing}>Click me</button>
      </div>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

注意:我没有破坏`showEmail`变量，因为在`render`函数中，我只使用了`state`的**一个**属性**一个**时间。

在我脑海中的功能是这样的

```
showAlert(){
    alert(this.state.showEmail)
} 
```

Enter fullscreen mode Exit fullscreen mode

比
更容易阅读

```
showAlert(){
    const { showEmail } = this.state;
    alert(showEmail);
} 
```

Enter fullscreen mode Exit fullscreen mode

尤其是在析构和变量使用之间有大量代码的情况下。虽然我会破坏变量，如果我想用一个道具不止一次。

```
showAlert(){
    const { showEmail } = this.state;
    alert(showEmail);
    alert(showEmail);
} 
```

Enter fullscreen mode Exit fullscreen mode

## 功能组件

ES6 [**析构赋值**](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment) 的好处在函数组件里看起来更好看。

如果我们有一个没有状态的类似组件:

```
import React from "react";

const Row = props => (
  <div>
    <div>
      <span>First Name: {props.firstName}</span>
    </div>
    <div>
      <span>Last Name: {props.lastName}</span>
    </div>
    <div>
      <span>Email: {props.email}</span>
    </div>
    <button onClick={props.doSomethingAmazing}>Click me</button>
  </div>
); 
```

Enter fullscreen mode Exit fullscreen mode

应用 props 析构技术可以编写这样的代码:

```
import React from "react";

const Row = ({ firstName, lastName, email, doSomethingAmazing }) => (
  <div>
    <div>
      <span>First Name: {firstName}</span>
    </div>
    <div>
      <span>Last Name: {lastName}</span>
    </div>
    <div>
      <span>Email: {email}</span>
    </div>
    <button onClick={doSomethingAmazing}>Click me</button>
  </div>
); 
```

Enter fullscreen mode Exit fullscreen mode

是不是很整洁！