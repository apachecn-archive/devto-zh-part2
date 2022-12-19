# JavaScript 挫折:类和类属性转换

> 原文：<https://dev.to/nestedsoftware/javascript-frustration-classes-and-class-properties-transform-16gl>

最近我一直在学习 React，在 JavaScript 中遇到了一些我没有预料到的事情。

这是我玩的一些代码的例子。此代码是对位于[https://react training . com/react-router/web/example/auth-workflow](https://reacttraining.com/react-router/web/example/auth-workflow)的代码的修改版本。

```
class Login extends React.Component {
  constructor() {
    this.state = {
      redirectToReferrer: false
    }
  }

  login() {
    fakeAuth.authenticate(() => {
      //the problem is here
      this.setState(() => ({ 
        redirectToReferrer: true
      }))
    })
  }

  render() {
    //...some additional logic here
    return (
      <div>
        <p>You must log in to view the page</p>
        <button onClick={this.login}>Log in</button>
      </div>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我相当震惊地发现，当我点击按钮时，浏览器抱怨说`setState`方法不存在！

事实证明，即使使用 ES2015 中首次推出的 [`class`语法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)，该类的方法也不会绑定到给定的实例。不知何故，我没有意识到情况是这样的。依赖于[调用上下文](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this#Function_context)的`this`还是那个老问题。如果我们想让代码工作，我们必须自己绑定方法，比如像这样:

```
class Login extends React.Component {
  constructor() {
    super()
    this.login = this.login.bind(this);
    //etc...
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我在网上看到的实际例子使用了我不熟悉的语法，大概是为了解决这个问题。原来这叫[类属性转换](https://babeljs.io/docs/plugins/transform-class-properties)。它目前可与[巴别塔](https://babeljs.io/)一起使用[第二阶段](https://babeljs.io/docs/plugins/preset-stage-2/)预设。下面是新语法的样子:

```
class Login extends React.Component {
  //class properties transform
  state = {
    redirectToReferrer: false
  }

  //class properties transform
  login = () => {
    fakeAuth.authenticate(() => {
      this.setState(() => ({
        redirectToReferrer: true
      }))
    })
  }

  render() {
    //...some additional logic here
    return (
      <div>
        <p>You must log in to view the page</p>
        <button onClick={this.login}>Log in</button>
      </div>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我不知道如何理解这个语法。我不是语言或 JavaScript 专家，但它对我来说就是不合适。

如果我们把`class`换成`function`，这让我想起了这样一件事:

```
function Login() {
  this.state = {
    redirectToReferrer: false
  }

  this.login = () => {
    fakeAuth.authenticate(() => {
      this.setState(() => ({
        redirectToReferrer: true
      }))
    })
  } 
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们使用`new Login()`创建一个实例，`this.setState`现在将不管调用上下文如何都工作。

然而，在这种情况下，使用类并添加这种新的转换语法真的值得吗？就好像这个新语法试图在使用`function`和`class`语法所能做的事情之间架起一座桥梁:我们不能仅仅在构造函数之外的`class`中编写`this.state = value`，但是现在我们可以用 transform 类属性来完成它。在这种情况下，也许一开始就应该在`class`允许。

我还研究了一下这个新语法是如何处理继承的。如果我们在超类中有一个普通的方法，在子类中有一个同名的箭头函数，那么在子类的方法中调用`super`实际上是有效的。

然而，如果超类和子类都使用箭头语法:
，那么`super`目前就不起作用

```
class BaseClass {
    arrowFunction = () => {
      console.log('BaseClass arrowFunction called')
    }
}

class SubClass extends BaseClass {
    arrowFunction = () => {
        super.arrowFunction()
        console.log('SubClass arrowFunction called')
    }
}

const t = new SubClass()
t.arrowFunction() 
```

Enter fullscreen mode Exit fullscreen mode

当我们使用带有‘env’和‘stage-2’预设的 Babel 来传输这段代码，并尝试在 node 中运行结果代码时，我们得到:

```
C:\dev\test.js:34
_get(SubClass.prototype.__proto__ 
  || Object.getPrototypeOf(SubClass.prototype), 'arrowFunction', _this).call(_this);

                                                                    ^
TypeError: Cannot read property 'call' of undefined
    at SubClass._this.arrowFunction (C:\dev\test.js:34:96) 
```

Enter fullscreen mode Exit fullscreen mode

看来`arrowFunction`在原型链中没有得到解决。我不知道这是有意的行为还是一个错误。

诸如此类的东西让我对 JavaScript 感到沮丧。这有点像 JavaScript 在追逐自己的尾巴，在更多的语法糖上添加语法糖，最终结果仍然令人困惑。我不知道这里的内部考虑是什么，但是看起来如果 JavaScript 有一个`class`语法，以一种更正交的方式这样做，不需要一直添加新的语法，会很好。

我对这种语法感到沮丧有错吗？我总是乐于接受不同的观点。