# 如何以及何时使用 React 的新上下文 API

> 原文：<https://dev.to/bnevilleoneill/how-and-when-to-use-reacts-new-context-api-131o>

### 如何(以及何时)使用 React 的新上下文 API

嘿，伙计们，你们听说有一个新的 React 上下文 API 了吗？

因此，在即将发布的 React，React 16.3.0 中， [React 上下文](https://reactjs.org/docs/context.html#why-not-to-use-context)将保持稳定，这在互联网上并不是什么新闻。React 上下文是 React 的一个实验性特性，由于贡献者的[多重](https://github.com/facebook/react/pull/11818) [PRs](https://github.com/reactjs/rfcs/pull/2) ，React 得到了更好的实现。

尽管这不是什么新闻，但花一分钟来学习何时以及如何使用它是值得的。这就是我们要做的。

### **上下文 API 解决什么问题？**

反应上下文是子组件访问父组件中的值的一种方式。

React 中的一个常见问题是众所周知的[支柱钻孔](https://medium.com/dailyjs/reacts-%EF%B8%8F-new-context-api-70c9fe01596b)。

道具钻取发生在这样的情况下，您希望从 react 树的顶部到底部获得状态，并且您最终通过不一定需要它们的组件传递道具。

React Context 解决道具演练问题。它允许你与一个间接的孩子或父母分享道具或状态。

通常，您会使用像 Redux 或 Mobx 这样的状态管理库，但是如果您不想这样做呢？或者要传递的数据是如此之小，以至于使用状态管理库将是多余的。

这就是新的 React 上下文 API 的用武之地。

### **当前上下文 API 怎么了？**

嗯，当前的上下文 API 被认为是不稳定的，因为它是一个实验性的 API，很可能会在 React 的未来版本中崩溃。此外，当前的和即将过时的上下文 API 在 shouldComponentUpdate 阻止上下文更改方面存在问题。

当一个中间组件使用 shouldComponentUpdate 进行更新时，React 会自动假设没有更改，并重用整个子树。问题是，如果子树包含一个上下文消费者，消费者将不会收到最新的上下文。

### **有什么新鲜事？**

对于新的 React 上下文 API，您应该熟悉三件事情:

**反应。createContext**

React.createContext 用于初始化上下文，并传递初始值。它返回一个带有提供者和使用者的对象。提供者和消费者是成对出现的，也就是说，对于每个提供者，都有一个相应的消费者。

```
const Context = React.createContext(); 
```

**供应商**

Provider 组件在树的更高层使用，它接受一个名为 value 的属性。它提供了一个根，在这个根上，树中的任何子节点都可以访问由上下文提供者提供的值。

```
render() { return ( \<Provider value={this.state.contextValue}\> {this.props.children} \</Provider\>  ); } 
```

提供者接受一个上下文值作为属性。在提供者的子树中，任何匹配的消费者都可以访问它，不管它的嵌套有多深。

**消费者**

顾名思义，消费者使用被传递的数据，并通过使用 render prop API 来呈现数据。

```
render() { return ( \<Consumer\> {contextValue =\> \<Child arbitraryProp={contextValue} /\>} \</Consumer\> ) } 
```

### 使用 React 上下文 API

在我演示如何使用 React Context 之前，让我们看一下下面这个使用 prop drilling 来获取一些数据的示例，以便我们可以建立一点…等等…上下文。
[https://codesandbox.io/embed/mmlzkk9p7y](https://codesandbox.io/embed/mmlzkk9p7y)
上面的代码块只是一个简短的例子，展示了道具钻取是如何工作的。我们必须从上到下传递道具数据，即使组件不一定需要它。

现在让我们看看如何使用 React 上下文来解决这个问题。要开始，你必须安装 React 16.3 的 alpha 版本。

```
npm i react@next react-dom@next 
```

[https://codesandbox.io/embed/j35mr677o5](https://codesandbox.io/embed/j35mr677o5)
我们来看看上面的代码块。

```
const JediContext = React.createContext(); 
```

我们首先初始化一个新的上下文，并将其设置为常量变量。它可以设置为任何值。

```
class JediProvider extends Component { state = { name: "Vader", side: "dark" }; render() { return ( \<JediContext.Provider value={{ state: this.state, turnGood: () =\> this.setState({ side: "good" }) }} \> {this.props.children} \</JediContext.Provider\>  ); } } 
```

在 JediProvider 组件中，我们在 render()函数中创建了一个提供者上下文作为 JediContext。提供商。JediContext。Provider 接受一个设置为状态的 prop called 值和一个更新状态的函数。

现在我们有了一个提供者，我们如何消费数据，甚至是跨父组件到子组件的数据？

```
class App extends Component { render() { return ( \<JediProvider\> \<Vader /\> \</JediProvider\>  ); } } 
const Vader = props =\> { return \<Luke /\>; }; const Luke = props =\> { return \<KyloRen /\>; };

const KyloRen = props =\> { return ( \<JediContext.Consumer\> {context =\> ( \<React.Fragment\> \<p\>My grandfather is {context.state.name} \</p\>  \<p\>He belonged to the {context.state.side} side\</p\> \<button onClick={context.turnGood}\>Turn\</button\>  \</React.Fragment\> )} \</JediContext.Consumer\>  ); }; 
render(\<App /\>, document.getElementById("root")); 
```

首先要做的是用我们创建的提供者(即 JediProvider)包装顶级应用程序组件的所有子组件。这意味着它的所有子节点都可以访问上下文中的值。

几乎就像上面的道具钻孔方法一样，虽然没有道具，没有钻孔，但是组件是相互传递的。不同之处在于底部的孩子是 KyloRen。KyloRen 组件使用 JediContext。消费者将上下文作为呈现道具传递。因为上下文保存状态，所以您可以很容易地使用它来显示所需的数据。

如 context.turnGood 所示，我们还可以使用 context 来更改和更新状态。每当单击按钮时，它都会调用作为值添加到提供程序中的 turnGood 函数。

总的来说，这是一个比 prop drilling 方法更好的解决方案，你不必把不必要的道具传递给不需要它们的组件。
[https://codesandbox.io/embed/j35mr677o5](https://codesandbox.io/embed/j35mr677o5)T2】

### **难道是 Redux 杀手？**

不。新的上下文 API 有其局限性。

例如，它[鼓励使用不可变的](https://github.com/reactjs/rfcs/blob/master/text/0002-new-version-of-context.md#relies-on-strict-comparison-of-context-values)或持久的数据结构或上下文值的严格比较，这可能被证明是困难的，因为许多常见的数据源依赖于突变。

另一个[限制](https://github.com/reactjs/rfcs/blob/master/text/0002-new-version-of-context.md#only-one-provider-type-per-consumer)是新的上下文 API 只允许消费者从单个提供者类型读取值，一个消费者对一个提供者。不像当前的 API，它允许一个消费者连接到多个提供者。

### 什么时候应该使用 React 上下文？

当您只想进行简单的状态管理时，或者当您想深入传递一些道具而又不想使用 Redux 或 MobX 带来的过度杀伤时，您可以使用它。

上下文 API 应该更新，并包含在 React 的下几个更新中。看到 Context API 的更新和特性变化肯定会很有趣。

* * *

### Plug: LogRocket，一款用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)T2】](http://logrocket.com)

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *

帖子[如何(以及何时)使用 React 的新上下文 API](https://blog.logrocket.com/how-and-when-to-use-reacts-new-context-api-b584e41b2704/) 首先出现在[日志博客](https://blog.logrocket.com)上。