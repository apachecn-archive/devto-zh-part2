# React 中的上下文

> 原文：<https://dev.to/kayis/context-in-react-69h>

丽贝卡·杰克逊在 Flickr 上的封面图片。T3】

## 什么

第一，什么是语境？

上下文是一种在 React 元素树中存储和传递数据的方式，而无需将数据写入层次结构的每一层。它由两个组件组成，一个`Provider`和一个`Consumer`。

一个`Provider`存储一些数据，这些数据可以通过树中`Provider`下面的`Consumer`来访问。所以`Consumer`必须是`Provider`的后代。

一个`Consumer`访问`Provider`数据，并通过一个渲染道具使其对`children`可用。好消息是，`Consumer`不一定是`Provider`的直接子节点，它可以在树中它下面的任何地方。

## 为什么

有时你在应用程序中使用准全局的数据。有时它对整个应用程序是全局的，有时只是对一个屏幕或页面是全局的，但它被用在很多地方。

例如，您想要在所有 UI 组件中使用主题信息，或者您想要使当前登录用户的数据可用于许多组件，或者您有一个 API 客户端，它需要配置一次，然后在整个应用程序中使用。

现在，您可以简单地将这些数据全球化，但这会很快变得难以处理。上下文是一种封装的方式，由于`Provider`和`Consumer`之间的元素都不知道上下文或其数据，这也是另一种将依赖注入添加到应用程序中的方式，使其对变化更具弹性。

## 如何

那么你如何创造一个环境呢？还有以后怎么用？

为了灵活性和易用性，不久前对上下文 API 进行了重写。React 提供了一个简单的函数来创建上下文。

```
const Context = React.createContext(); 
```

Enter fullscreen mode Exit fullscreen mode

该函数返回一个具有两个属性`Provider`和`Consumer`的对象，其中包含稍后使用该上下文所需的组件。

基本用法如下:

```
<Context.Provider value="context data">
  ...
  <Context.Consumer>
    {value => <p>{value}</p>}
  </Context.Consumer>
  ...
</Context.Provider> 
```

Enter fullscreen mode Exit fullscreen mode

`Provider`拿了一个`value`道具，变成了它的`state`。`Consumer`以*儿童形式的`render`道具作为功能*。该函数接收当前的`value`作为参数。

通常，您有更复杂的数据，以及在使用这些数据的组件中更改这些数据的方法。

这里举一个更复杂的例子:

```
const Context = React.createContext();
class A extends React.Component {
  state = { x: 1 };
  handleContextChange = x => this.setState({ x });
  render() {
    const contextValue = {
      data: this.state,
      handleChange: this.handleContextChange
    };
    return (
      <Context.Provider value={contextValue}>
        <B />
      </Context.Provider>
    );
  }
}
const B = props => <div><C /></div>; const C = props => (
  <Context.Consumer>
    {({ handleChange, data }) => (
      <div>
        <button onClick={() => handleChange(2)}>Change</button>
        <D text={data.x} />
      </div>
    )}
  </Context.Consumer> );
const D = props => <p>{props.text}</p>; 
```

Enter fullscreen mode Exit fullscreen mode

我们从创建一个`Context`开始。

然后我们在组件 A 中使用它，它是我们层次结构的顶层。我们的`Context.Provider`的值是`A`的状态和处理这个`state`变化的`A`的方法。当`state`改变时，`Context.Provider`获得一个新值。也是我们状态的存储，上下文只是将它传递到层次结构中。

在组件`C`中，我们使用了`Context.Consumer`，它通过其`children`渲染属性接收一个函数。如果`Context.Provider`的值改变了，这个函数简单地被再次调用，并使用新值进行渲染。

如你所见，在`A`和`C`之间的成分`B`对于整个上下文安排来说是完全不可捉摸的。它只是让组件`C`对它的实现漠不关心。

此外，组件`D`和`button`元素不知道任何关于上下文的信息。它们只是获得通过它们的 props 传递的数据和 change handler 函数，并可以像使用任何其他 props 一样使用它们。`D`希望它呈现的文本传递到它的`text`道具中，而不是传递到子道具中，button 只是一个普通的旧按钮，它执行传递到它的`onClick`道具中的所有内容。所以 context 是纯渲染道具中使用的依赖注入的扩展。

因为这对`Provider`和`Consumer`是为每个`createContext()`调用创建的，所以您甚至可以拥有多个上下文。每个上下文都被封装起来，不受其他上下文的影响。

## 结论

新的上下文 API 比旧的更加灵活，不需要道具类型就可以工作，而且因为它现在是稳定的，你终于可以使用它了，不用担心它会很快消失。

它还扩展了 render props 中使用的依赖注入概念，允许您将状态从一个组件传递到它的深层祖先，而无需告诉中间的任何内容。