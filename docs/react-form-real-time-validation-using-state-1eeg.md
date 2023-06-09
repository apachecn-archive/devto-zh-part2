# 如何制作互动表单

> 原文：<https://dev.to/nsebhastian/react-form-real-time-validation-using-state-1eeg>

访问您的 web 应用程序的用户心中有一个他们想要实现的特定目标。表单是一种媒介，它允许用户与您联系并发送信息，如订单、目录请求甚至是查询，这些信息会传递给其他流程。

一个清晰、智能的好的表单设计可以帮助你的用户快速实现他们的目标。相反，一个设计糟糕的表单会引起混乱，甚至阻碍用户与你的应用程序交互。

所以我们同意，一个好的表单有益于你的应用，让用户开心。然而在 React 中实现一个好的表单需求似乎很难:动态表单、实时响应反馈和创建一个好的 UX。我们如何在`components`、`states`和`props`的土地上实现这些需求？

我们可以得到的第一个提示当然是来自关于表单的 React 文档。

```
handleChange = e => {
  this.setState({ value: e.target.value })
}

// ...

<input
  onChange={this.handleChange}
  value={this.state.value}
/> 
```

Enter fullscreen mode Exit fullscreen mode

这基本上就是 React 的表单文档总结了。它只是告诉您 React 在处理用户点击或击键时应该这样使用。React 将用户的值设置为 state，然后将该状态用作输入的值。**结束**。

*嗯？就这样？*

没错。至于在为具有复杂业务逻辑的应用程序构建表单时会遇到的其他问题..嗯，这取决于你。喜欢做:

1.  确认
2.  显示错误
3.  跟踪表单字段
4.  处理提交

正如在文档中所读到的，React 对如何构建项目和选择库栈没有任何偏见。这也意味着它只提供制作表单组件的最基本的必要条件。`component`、`state`、`props`就像拼图块，需要我们自己去拼凑。

这是你从本教程中得到的最终产品:
[https://codepen.io/nathansebhastian/embed/pGoqOV?height=600&default-tab=js,result&embed-version=2](https://codepen.io/nathansebhastian/embed/pGoqOV?height=600&default-tab=js,result&embed-version=2)

在用 React 制作表单时，你需要记住 3 个基本原则，它们是:

1.  `component`用于渲染表单元素，通常是 JSX 元素
2.  `state`用于跟踪用户的输入
3.  `props`用于将数据传递到 JSX 元素中

不管你想创造什么样的形式，只要你记住这三个基本原则，你就会做得很好。

## 一种基本的反应形式

React 中的一切都是组件，包括表单，React 使用`state`来跟踪输入值。下面是一个用 React 编写的示例表单。

```
class BasicForm extends Component {
  constructor(props) {
    super(props);
    this.state = {
      name:'',
      email: '',
    };
  }

  handleNameChange = (event) => {
    this.setState({name: event.target.value});
  }

  handleEmailChange = (event) => {
    this.setState({email: event.target.value});
  }

  handleSubmit = (event) => {
    event.preventDefault();
    const { name, email } = this.state
    alert(`Your state values: \n 
            name: ${name} \n 
            email: ${email}`)
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <div className="form-group">
          <label htmlFor="name">Name</label>
          <input name="name" className="form-control" id="name" placeholder="Enter name" value={this.state.name} onChange={this.handleNameChange} />
        </div>
        <div className="form-group">
          <label htmlFor="email">Email</label>
          <input name="email" className="form-control" id="email" placeholder="Enter email" value={this.state.email} onChange={this.handleEmailChange} />
        </div>
        <button type="submit" className="btn btn-success btn-block">Submit</button>
      </form>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

*哇！这段代码是做什么的？*

不用担心，代码不会咬人！现在让我给你解释一下。

我们从`state`开始。react 表单使用`state`作为*字段值的唯一来源*。这意味着表单中的每个`input`元素`component`都将把`state`值作为它的值。

```
this.state = {
  name:'',
  email: '',
}; 
```

Enter fullscreen mode Exit fullscreen mode

然后将状态值分配到`input`标签`value` `prop`中。我们还添加了一个`onChange` `prop`，它将在每次输入值改变时运行。最后，我们还在表单组件中添加了`onSubmit` prop 来处理提交。

```
render() {
  return (
    <form onSubmit={this.handleSubmit}>
      <div className="form-group">
        <label htmlFor="name">Name</label>
        <input name="name" className="form-control" id="name" placeholder="Enter name" 
        // value and onChange prop
          value={this.state.name} 
          onChange={this.handleNameChange} />
      </div>
      <div className="form-group">
        <label htmlFor="email">Email</label>
        <input name="email" className="form-control" id="email" placeholder="Enter email" 
        // value and onChange prop
          value={this.state.email} 
          onChange={this.handleEmailChange} />
      </div>
      <button type="submit" className="btn btn-success btn-block">Submit</button>
    </form>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们可以添加一个接受`event`参数的`handleChange`方法。这个事件对象将保存我们的输入名称和值。

```
handleNameChange = (event) => {
  this.setState({name: event.target.value});
}

handleEmailChange = (event) => {
  this.setState({email: event.target.value});
} 
```

Enter fullscreen mode Exit fullscreen mode

表单结构的最后一部分是提交处理程序方法。在这个例子中，我们使用了一个`handleSubmit`方法，该方法简单地调用一个警告框，它打印出我们的状态值。

```
handleSubmit = (event) => {
  event.preventDefault();
  const { name, email } = this.state
  alert(`Your state values: \n 
          name: ${name} \n 
          email: ${email}`)
} 
```

Enter fullscreen mode Exit fullscreen mode

像常规的 HTML 表单一样，这是执行和处理保存或发送数据的地方。因为我们使用自己的 JavaScript 代码来处理提交，所以我们需要将`event.preventDefault()`添加到我们的提交方法中。这是因为浏览器的 JavaScript 侦听器被设置为侦听表单提交事件，这通常会触发页面重新加载。通过使用这个`preventDefault`，我们告诉浏览器停止使用默认的方法。这样，页面重新加载将停止，我们的提交方法可以运行。

## 进行验证

验证数据的传统方法是提交表单，等待服务器完成验证，然后网页将刷新并显示一些错误消息。对于用户来说，该过程花费大量时间并且很麻烦。

由于 React 是一个前端库，它可以通过在表单组件中内置即时验证来解决这个问题。其实这是 React 应用中常见的模式，在我看来非常牛逼。

因为 React 在状态中存储所有表单数据，所以我们可以在`render`之前使用一点检查，如果数据无效就显示错误消息。例如，要验证名称长度是否超过 3 个字符，我们可以使用:

```
render(){
  const isValidName = this.state.name.length > 3
  const isValidEmail = this.state.email.length > 3
} 
```

Enter fullscreen mode Exit fullscreen mode

然后把它放在上下文中:

```
// the render method

render() {
  const isValidName = this.state.name.length > 3;
  const isValidEmail = this.state.email.length > 3;
  return (
    <form onSubmit={this.handleSubmit}>
      <div className="form-group">
        <label htmlFor="name">Name</label>
        <input
          name="name"
          className={`form-control ${ isValidName? '':'is-invalid' }`}
          id="name"
          placeholder="Enter name"
          value={this.state.name}
          onChange={this.handleNameChange}
        />
        {/*feedback here*/}
      { isValidName? null: <div className='invalid-feedback'>Name must be longer than 3 characters</div> }
      </div>
      <div className="form-group">

        {/*after email input*/}
      { isValidEmail? null: <div className='invalid-feedback'>Email must be longer than 3 characters</div> }
      </div>
      <button type="submit" className="btn btn-success btn-block">
        Submit
      </button>
    </form>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

表单立即生效，当名称超过 3 个字符时，错误信息将消失。但是这种验证并不是最佳的，因为我们将验证逻辑放入 render 方法中，当我们验证大量数据时，这将很快使方法变得一团糟。它甚至会在我们对文本框做任何事情之前运行。这可不好。

## 使用状态进行错误检查

正如我们使用状态进行数据输入一样，我们也可以使用状态进行验证。我们将在状态初始化中添加新的状态属性。

```
this.state = {
  name: '',
  email: '',
  nameError: '',
  emailError: ''
} 
```

Enter fullscreen mode Exit fullscreen mode

`formError`状态将保存我们的错误消息，我们将使用它们来显示我们可能有的任何错误消息。让我们通过创建新的验证函数来将它们放到上下文中:

```
handleNameChange = event => {
  this.setState({ name: event.target.value }, () => {
    this.validateName();
  });
};

handleEmailChange = event => {
  this.setState({ email: event.target.value }, () => {
    this.validateEmail();
  });
};

validateName = () => {
  const { name } = this.state;
  this.setState({
    nameError:
      name.length > 3 ? null : 'Name must be longer than 3 characters'
  });
}

validateEmail = () => {
  const { email } = this.state;
  this.setState({
    emailError:
      email.length > 3 ? null : 'Email must be longer than 3 characters'
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

这样，只有当用户在输入中输入内容时，验证方法才会运行。现在，我们要做的最后一件事是当用户点击一个文本框时运行验证，然后不做任何事情就点击另一个文本框。

## 添加 onBlur

让我们给输入元素添加一个`onBlur`道具。

```
<input
  name='name'
  // className, id, onChange ...
  onBlur={this.validateName}
/>

<input
  name='email'
  // className, id, onChange ...
  onBlur={this.validateEmail}
/> 
```

Enter fullscreen mode Exit fullscreen mode

现在，验证方法将在用户“触摸”的相应 texbox 上运行，然后显示任何错误消息(如果有的话)。

这是演示:

[https://codepen.io/nathansebhastian/embed/pGoqOV?height=600&default-tab=js,result&embed-version=2](https://codepen.io/nathansebhastian/embed/pGoqOV?height=600&default-tab=js,result&embed-version=2)

## 结论

现在是总结我们从这个简单的例子中学到的东西的时候了。让我们再重复一下 React form 的 3 个基本原则:

1.  `component`用于渲染表单元素，通常是 JSX 元素
2.  `state`用于跟踪用户的输入
3.  `props`用于将数据传递到 JSX 元素中

我们已经看到这是一个如何反应形式的尝试和真正的原则。我们已经编写了一个`component`来呈现我们的 JSX 表单。我们使用了`state`来跟踪`name`和`email`值，并且使用了 props 将数据从状态值传递到输入值，包括将一个`handleChange`函数传递到`onChange` props。

对于那些还不熟悉 React 处理数据方式的人来说，在 React 中制作表单是一项非常复杂的任务。如果你需要一些关于 React 表单的高级指南，我推荐你去看看 Arinich 的高质量 [React 表单教程](https://goshakkk.name/on-forms-react/)。它可以帮你节省很多时间。

*感谢阅读！如果你喜欢这样的文章，一定要关注我。我很快会在 React 上写更多的教程。*