# 如何在 React 16.3+中使用 getDerivedStateFromProps

> 原文：<https://dev.to/larryprice/how-to-use-getderivedstatefromprops-in-react-163-oba>

从【2018 年 3 月下旬的一篇博文，宣布 React 生命周期方法`componentWillReceiveProps`、`componentWillMount`和`componentWillUpdate`将在 React 的未来版本中被弃用。这是因为 React 最终会迁移到异步渲染；当异步渲染成为默认时，这些生命周期方法将变得不可靠。

代替这些方法，新的**静态**方法`getDerivedStateFromProps`被引入。起初，我和我的团队努力思考如何将我们对`componentWillReceiveProps`的许多使用迁移到这个新方法中。这通常比你想象的要容易，但是你需要记住新方法是静态的，因此不能访问旧的生命周期方法提供的 T2 上下文。

每次呈现组件时都会调用`getDerivedStateFromProps`。它接受两个参数:正在讨论的组件的下一个`props`对象(可能与前一个对象相同)和前一个`state`对象。当实现这个方法时，如果不需要做任何更改，我们需要将更改返回给我们的组件`state`或`null`(或`{}`)。

### [组件别墅接收插件](#componentwillreceiveprops)

下面是我们在代码库中的许多组件中使用的模式:

```
componentWillReceiveProps(nextProps) {
  if (nextProps.selectedTab !== this.state.selectedTab) {
    this.setState(() => { return {selectedTab: nextProps.selectedTab} })
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

当我们要在组件中接收新的`props`时，这个生命周期方法被触发，将新值作为第一个参数传入。我们需要检查新的`props`是否表明我们标签栏的状态发生了变化，标签栏存储在`state`中。这是用`getDerivedStateFromProps` :
解决的最简单的模式之一

```
static getDerivedStateFromProps(nextProps, prevState) {
  return nextProps.selectedTab === prevState.selectedTab
    ? {}
    : {selectedTab: nextProps.selectedTab}
} 
```

Enter fullscreen mode Exit fullscreen mode

这段代码以完全相同的方式工作，但是，由于它是**静态的**，我们不再使用`this`提供的上下文。相反，我们返回任何状态变化。在本例中，我返回了一个空对象(`{}`)来表示当选项卡相同时没有状态变化；否则，我返回一个带有新的`selectedTab`值的对象。

有时您可能必须在新的`props`上执行一些操作，但是您仍然可以将结果与之前的状态进行比较，以确定是否有任何变化。可能在其他地方你需要存储一些额外的状态来复制你的旧的`props`来完成这项工作，但是这也可能意味着你需要使用一个替代的方法。

### componentWillMount

我们还需要替换对`componentWillMount`的调用。我发现这些调用通常可以被`componentDidMount`直接替换，这将允许你的组件执行一个初始渲染，然后执行阻塞任务。这可能还需要给你的组件增加一些加载风格的能力，但这比一个悬挂式的应用程序要好。

这里有一个`componentWillMount`的例子，我们最初阻塞了渲染，直到一个 API 调用完成:

```
componentWillMount() {
  this.setState(() => {
    return {
      loading: 'Loading tool info'
    }
  })
  return getTool(this.props.match.params.id).then((res) => {
    this.setState(() => {
      return {
        tool: res,
        loading: null
      }
    })
  }).catch((err) => {
    api.errors.put(err)
    this.setState(() => {
      return {
        loading: null
      }
    })
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

后来，我改变了状态，在初始渲染时显示组件正在加载，并用`componentDidMount` :
替换了`componentWillMount`

```
state = {
  tool: null,
  loading: 'Loading tool info'
}

componentDidMount() {
  return getTool(this.props.match.params.id).then((res) => {
    this.setState(() => { return {tool: res, loading: null} })
  }).catch((err) => {
    api.errors.put(err)
    this.setState(() => { return {loading: null} })
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

### 组件更新

与上面讨论的方法非常相似，当一个组件将要接收新的道具时，`componentWillUpdate`会被调用，而`render`方法肯定会被调用。这里有一个我们以前做过的事情的例子:

```
componentWillUpdate(nextProps) {
  if (!nextProps.user.isLogged && !nextProps.user.authenticating) {
    this.context.router.history.push('/')
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

并且，用`componentDidUpdate` :
替换该用法

```
componentDidUpdate(/*prevProps, prevState*/) {
  if (!this.props.user.isLogged && !this.props.user.authenticating) {
    this.context.router.history.push('/')
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

`componentDidUpdate`与`componentDidMount`相似，除了它是在状态或道具发生变化后引起的，而不仅仅是在初始挂载时。与`getDerivedStateFromProps`不同，我们可以访问`this`提供的上下文。注意，这个方法还有`prevProps`和`prevState`的参数，它们提供了组件的`props`和`state`的以前版本，以便与当前值进行比较。

### 结论

这些生命周期方法直到 React 17 才会被弃用，但是提前计划总是好的。我的团队使用这些被否决的方法的许多方式可以被认为是反模式，我怀疑你的团队可能处于同样的困境。