# 在 React 中使用渲染回调来连接未连接的组件

> 原文：<https://dev.to/moimikey/using-render-callbacks-to-connect-non-connected-components-in-react-30nf>

连接组件和非连接组件的区别在于，连接组件订阅某种状态，而非连接组件不知道任何状态，因此是“哑的”

在开发可伸缩的应用程序时，您更有可能创建非连接的“哑”组件，这些组件随后可能需要订阅某种状态，就像您从 Redux store 订阅一样。

让我们想象一个由组成票务销售网站的组件组成的生态系统。这个应用程序由许多小组件组成，但我们将重点放在一个组件上:侧边栏中的一个模块，显示当时的最高门票销售。页脚也有一个显示相同数据的模块(它实际上是相同的模块，我们只是呈现了它的第二个实例)。这两个组件都是从不同的地方实例化的，但是它们需要相同的数据。两者都是哑的，都需要数据来呈现。

“提供者”组件背后的思想是提供值。例如，我们可以创建一个函数组件，它将一个函数作为它的子组件，当被回调时，返回一个名为 data 的属性，这是一个空数组。如果你不熟悉渲染回调的概念，请阅读我以前的文章，[在 React](https://dev.to/moimikey/power-up-your-renders-with-render-callbacks-in-react-47i5) 中使用渲染回调来增强渲染。

```
const Provider = (props) => {
    const data = []
    return props.children({ data })
} 
```

有了上面的内容，我们可以像使用渲染回调那样使用它:

```
<Provider>
    {({ data }) => <MyComponent data={data} />}
</Provider> 
```

我们所做的只是将我们的组件包装在这个`Provider`组件中，它将提供数据，供内部组件(MyComponent)使用。数据可以是我们想要的任何东西，也可以来自任何地方。为了更具描述性，可以将提供程序重命名为其他名称:

```
<TopSalesByHour>
    {({ data }) => <Tickets data={data} />}
</TopSalesByHour> 
```

上面的提供者只是充当一个状态机，并向其子节点提供状态，在上面的例子中是`Tickets`。我们可以更进一步，为`Tickets`提供一个加载接口，因为我们可能希望在实际渲染`Tickets`之前等待数据:

```
<TopSalesByHour>
    {({ data }) => data ? <Tickets data={data} /> : <Loading />}
</TopSalesByHour> 
```

让上述功能强大的是，我们从视图逻辑中抽象出了数据。我们还提供了一种可伸缩的方式来为无状态组件提供状态。最后，我们为组件提供了基于该状态有条件呈现的能力。

我们可以进一步深入到一个场景中，我们可能希望向订户提供多个数据流。在这种情况下，我们不仅想要`Top Sales By Hour`，还想要`Top Sales By Day`。我们如何“组合”这两个提供者呢？

```
/*
 * We need to combine both of these...
 */
<TopSalesByHour>
    {({ data }) => data ? <Tickets data={data} /> : <Loading />}
</TopSalesByHour>

/*
 * into one
 */
<TopSalesByDay>
    {({ data }) => data ? <Tickets data={data} /> : <Loading />}
</TopSalesByDay> 
```

为了做到这一点，我们需要创建一个助手函数。Javascript 并不神奇(有时很神奇)。这个 helper 函数将接受提供者作为参数，并将它们组合成两个可读的值。让我们首先想象一下如何使用这个组件:

```
<TopSales>
    {({byHour, byDay}) => (
        <React.Fragment>
            <Tickets data={byHour} />
            <Tickets data={byDay} />
        </React.Fragment>
    )}
</TopSales> 
```

在上面的例子中，我们没有给我们的变量命名为`data`，而是给它一个更具描述性的名称，说明数据点实际上是什么，因为我们现在要通过创建一个`TopSales`组件来组合`TopSalesByHour`和`TopSalesByDay`，该组件将组合两者:

```
const Create = (jsx, container) => (...containers) => ({ children }) => {
  const wrap = props =>
    typeof children === "function" // For react, in preact this is an array.
      ? children(props)
      : children.map(fn => fn(props));

  const gatherComponents = (parent, ...rest) => {
    const child =
      rest.length === 0
        ? props => jsx(container, null, wrap(props))
        : gatherComponents(...rest);

    return outerProps =>
      jsx(parent, outerProps, innerProps =>
        jsx(child, Object.assign({}, outerProps, innerProps))
      );
  };

  return gatherComponents(...containers)();
}; 
```

上面的函数是迷你蛋从`render-prop-composer`借来的。我喜欢这个，因为它简单。让我们使用它，然后解释它是如何工作的:

```
const composer = Create(React.createElement, React.Fragment)
const TopSales = composer(TopSalesByHour, TopSalesByDay) 
```

就这么简单，我们现在可以将`TopSalesByHour`和`TopSalesByDay`组合成一个单独的`TopSales`组件，它的唯一目的就是为我们提供票务数据:

```
<TopSales>
    {({byHour, byDay}) => (
        <React.Fragment>
            <Tickets data={byHour} />
            <Tickets data={byDay} />
        </React.Fragment>
    )}
</TopSales> 
```

但是代码到底是如何工作的呢？我们来探索一下。

```
const Create = (jsx, container) => (...containers) => ({ children }) => {
  const wrap = props =>
    typeof children === "function" // For react, in preact this is an array.
      ? children(props)
      : children.map(fn => fn(props));

  const gatherComponents = (parent, ...rest) => {
    const child =
      rest.length === 0
        ? props => jsx(container, null, wrap(props))
        : gatherComponents(...rest);

    return outerProps =>
      jsx(parent, outerProps, innerProps =>
        jsx(child, Object.assign({}, outerProps, innerProps))
      );
  };

  return gatherComponents(...containers)();
}; 
```

上面的`Create`函数首先要注意的是，它做的第一件事是返回一个函数。这就是为什么第一次使用时，我们设置函数使用`React.createElement`和`React.Fragment`。我真的很钦佩函数的作者，他保持了代码不可知和依赖自由，因此我们传递 React 函数供它内部使用。当你进一步阅读时，变量`jsx`将引用`React.createElement`，而`container`将引用`React.Fragment`。

```
const composer = Create(React.createElement, React.Fragment)
const TopSales = composer(TopSalesByHour, TopSalesByDay) 
```

当我们随后传递我们想要组合的组件时，`gatherComponents`被调用，使用它的参数:`TopSalesByHour`和`TopSalesByDay`。

```
gatherComponents(...containers)(); 
```

神奇的事情就发生在上面的方法中:

```
 const gatherComponents = (parent, ...rest) => {
    const child =
      rest.length === 0
        ? props => jsx(container, null, wrap(props))
        : gatherComponents(...rest);

    return outerProps =>
      jsx(parent, outerProps, innerProps =>
        jsx(child, Object.assign({}, outerProps, innerProps))
      );
  }; 
```

记住`jsx`指的是`React.createElement`，上面的代码返回的是另一个函数(这很重要)，它负责创建一个新的组件(`TopSales`)并把每个(`...rest`)组件的道具分配给它，作为参数传递给`composer` ( `const TopSales = composer(TopSalesByHour, TopSalesByDay)`)。

此外，请注意，`gatherComponents`在其自身内部被调用，这表示这是一个递归函数，而`rest.length === 0`是基本情况。

当`gatherComponents`迭代每个组合的`TopSales*`组件时，`outerProps`保存每个迭代的组件的值，这些值保存我们的道具数据，这些数据被分配给我们将使用的最终组合组件(`TopSales`)。

仅此而已！乍一看，这可能会让开发新手感到困惑，但是我强烈建议您在下面链接的沙箱中试用这些代码。如果这仍然是一个挑战，我推荐阅读闭包和递归。

如果你有任何问题，意见，担忧，想法，请在下面留下评论！另外，在 [Codementor](https://www.codementor.io/moimikey) 上安排一次与我的会面，并获得前 15 分钟的免费时间。

* * *

在 CodeSandbox 上试试上面的伪代码:[https://codesandbox.io/s/7j3pxr3xyq](https://codesandbox.io/s/7j3pxr3xyq)

参考资料:

*   [https://github.com/mini-eggs/render-prop-composer](https://github.com/mini-eggs/render-prop-composer)