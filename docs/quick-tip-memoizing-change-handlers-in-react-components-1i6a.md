# 快速提示-记住 React 组件中的更改处理程序

> 原文：<https://dev.to/robertbroersma/quick-tip-memoizing-change-handlers-in-react-components-1i6a>

让我们考虑一个在`react` :
中带有受控组件的基本表单

```
class Form extends React.Component {
  state = {
    value: '',
  };

  handleChange = e => {
    this.setState({
      value: e.target.value,
    });
  };

  render() {
    return (
      <div>
        <InputComponent type="text" value={this.state.value} onChange={this.handleChange} />
      </div>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们保存一个状态，将值传递给我们的`InputComponent`，并用从中获得的值更新该值。

现在考虑这个更大的形式。我喜欢用这个返回另一个箭头的箭头函数(你管这个叫什么？)语法，以避免重复使用多个更改处理程序。

```
class BiggerForm extends React.Component {
  state = {
    a: '',
    b: '',
    c: '',
  };

  handleChange = key => e => {
    this.setState({
      [key]: e.target.value,
    });
  };

  render() {
    return (
      <div>
        <InputComponent type="text" value={this.state.a} onChange={this.handleChange('a')} />
        <InputComponent type="text" value={this.state.b} onChange={this.handleChange('b')} />
        <InputComponent type="text" value={this.state.c} onChange={this.handleChange('c')} />
      </div>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

看起来很简单，对吧？这样做的问题是`this.handleChange()`每次被调用都会创建一个新函数。这意味着每次 BiggerForm 重新渲染时，所有的`InputComponents`都会重新渲染。这意味着每一次击键都将重新呈现。你可以想象这对一个巨大的窗体会有什么影响。

现在，我们可以做的是将`handleChange`拆分成特定的变更处理程序，例如`handleChangeA`、`handleChangeB`、`handleChangeC`，这将解决我们的问题。但是这是大量的重复，考虑到我们正在考虑巨大的形式；许多乏味的工作。

幸运的是有一种东西叫做记忆化！简而言之，这是我们函数的缓存机制。听起来很奇妙，但它所做的只是在调用函数时记住哪些参数产生什么结果。当使用相同的参数再次调用该函数时，它将不会执行该函数，而只是返回相同的结果。在我们的例子中:

```
class MemoizeForm extends React.Component {
  state = {
    a: '',
    b: '',
    c: '',
  };

  handleChange = memoize(key => e => {
    this.setState({
      [key]: e.target.value,
    });
  });

  render() {
    return (
      <div>
        <InputComponent type="text" value={this.state.a} onChange={this.handleChange('a')} />
        <InputComponent type="text" value={this.state.b} onChange={this.handleChange('b')} />
        <InputComponent type="text" value={this.state.c} onChange={this.handleChange('c')} />
      </div>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

那很容易！在这个例子中，在第一次渲染`MemoizeForm`时，为每一个`InputComponent`调用`handleChange`函数，并将它们的特定键作为参数。每当`MemoizeForm`重新渲染时，`handleChange`就会被再次调用。然而，由于它是用和以前相同的参数调用的，记忆机制返回相同的函数(用相同的引用)，并且`InputComponent`没有被重新呈现(当然，除非值被改变！).

🎉

任何记忆库都可以，我喜欢用[快速记忆](https://github.com/caiogondim/fast-memoize.js/)

-编辑-

我最近才知道`event.target`包含了更多的东西！使用钩子你可以做:

```
const [state, setState] = useState(initialValues)

const handleChange = useCallback(e => {
  setState(values => ({ ...values, [e.target.name]: e.target.value }))
}), []) 
```

Enter fullscreen mode Exit fullscreen mode