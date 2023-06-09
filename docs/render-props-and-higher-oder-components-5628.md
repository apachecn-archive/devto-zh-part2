# 渲染道具和高阶组件

> 原文：<https://dev.to/kayis/render-props-and-higher-oder-components-5628>

*[封面图片由涂鸦拍照者拍摄，在 Flickr 上](https://www.flickr.com/photos/graffiti-picture-taker/)*

上周，我参加了有史以来第一次开发竞赛，并提交了一个无服务器的多人点击器游戏。

### [要是能拿到你的❤️就太棒了&🦄在我的入门帖上](https://dev.to/kayis/startup-clix-serverless-pusher-contest-entry-written-in-javascript-1jai)

作为回报，我也想给你一些诀窍。

* * *

如果你是一名 React 开发人员，并且跟踪了这个生态系统几年，你可能会注意到最近出现了*渲染道具* (RP)，或者说像孩子一样的功能。

人们总是告诉你高阶元件(HoC ),现在又是这个？

我也很困惑，但是如果你考虑 React 编程实践，你会发现 RPs 完全有道理。

## 什么

渲染道具仅仅是将在渲染调用中以某种方式使用的道具。它们接受一个需要返回元素的函数。这个函数还通过它的参数获得一些动态数据，这些数据可以被返回的元素使用。

它们是 HoC 的依赖注入替代方案。

## 为什么

每次从基于 RP 的组件创建元素时，都可以将不同的元素传递到它的 RP 中。HoC 将在定义时而不是渲染时包装你的组件。

最近几年，在 React 编码实践中使用依赖注入来创建嵌套元素变得很普遍，RPs 是这一原则的自然延伸。

例如，你*不会*这样定义一个`List`组件:

```
const List = props => <ul>{props.data.map(i => <ListItem text={i}/>)}</ul>;
const ListItem = props  => <li>{props.text}</li>; 
// usage
<List data={["hello", "world"]}/> 
```

Enter fullscreen mode Exit fullscreen mode

因为现在你的`List`需要知道`data`和哪个`ListItem`需要渲染。

相反，你应该这样定义它:

```
const List = props => <ul>{props.children}</ul>; const ListItem = props  => <li>{props.text}</li>; 
// usage
<List>
  {data.map(i => <ListItem text={i}/>)} </List> 
```

Enter fullscreen mode Exit fullscreen mode

因为现在你可以*将`data`和子组件*注入到`List`中，它只需要渲染它。例如，你可以再加一个`List`不需要知道的`ActiveListItem`。

带有 RPs 的组件可以很好地处理这个问题。想象一下，您的`List`将简单地呈现所有的`children`,并将收集的一些数据传递给它们。

## 高阶取指

HoC 是实现这一点的另一种方式，但是他们背后的想法是创建一个你可以在任何地方使用的`wrapped`组件，它有一些额外的特性。

临时提取可能如下所示

```
const wrapWithFetch = Wrapped => class Fetch extends React.Component {
  state = { result: null };

  componentDidMount() {
    fetch(this.props.url)
    .then(r => r.json())
    .then(result => this.setState({result}))
  }

  render() {
    const {result} = this.state;
    return result? <Wrapped data={result}/> : null;
  }
}

// Stateless component that displays text
const Text = ({data})=> <p>{data.name}</p>; 
// Wrappted text that gets data
const FetchText = wrapWithFetch(Text);

// Usage
<FetchText url="/user/123"/> 
```

Enter fullscreen mode Exit fullscreen mode

## 渲染道具提取

RP 版本可能是这样的:

```
class Fetch extends React.Component {
  state = { result: null };

  componentDidMount() {
    fetch(this.props.url)
    .then(r => r.json())
    .then(result => this.setState({result}))
  }

  render() {
    const {result} = this.state;
    return result? this.props.render(result) : null;
  }
}

// usage
<Fetch url="/user/123" render={user => <p>{user.name}</p>}/> 
```

Enter fullscreen mode Exit fullscreen mode

当它被挂载时，它将获取一些数据并将其传递给 RP。

因为`children`是道具，你也可以用它们来代替定制道具。

```
<Fetch url="/user/123">{user =>
  <p>{user.name}</p> }</Fetch> 
```

Enter fullscreen mode Exit fullscreen mode

这将导致一个类似于
的`Fetch`组件

```
class Fetch extends React.Component {
  state = { result: null };

  componentDidMount() {
    fetch(this.props.url)
    .then(r => r.json())
    .then(result => this.setState({result}))
  }

  render() {
    const {result} = this.state;
    return result? this.props.children(result) : null;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

正如您所想象的，现在您可以简单地将任何子节点封装到一个函数中，该函数将从服务器接收数据，并且只在数据可用时才被呈现。

## 结论

渲染道具可以用来在你的应用程序中添加更多的依赖注入，使它变得更加灵活。

您可以在 RP 中添加新的元素来简单地改变显示的内容，例如将表格改为图表等。

但是您也可以更改包装 RP 组件，这样子组件现在将从不同的源接收数据，但是您不必更改子组件，因为您可以动态地将 RP 参数中的数据映射到正确的子属性。

## 竞赛

另外，如果你喜欢这篇文章:

### [我很欣赏你的❤️ &🦄在我的入门帖上](https://dev.to/kayis/startup-clix-serverless-pusher-contest-entry-written-in-javascript-1jai)