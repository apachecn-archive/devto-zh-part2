# 使用 React 16.5 探查器加快渲染速度

> 原文：<https://dev.to/dustinsoftware/making-renders-faster-with-the-react-165-profiler-f6f>

React 16.5 最近发布，增加了对一些新的分析工具的支持。我们最近使用这些工具来确定渲染性能缓慢的主要原因。

Faithlife.com 是一个由 React 16.3 支持的 web 应用程序。主页由按时间倒序排列的帖子组成。我们收到一些报告称，与帖子的交互(如回复)会导致浏览器延迟，这取决于帖子在页面上的位置。文章越靠后，滞后现象越严重。

在 Faithlife 的本地副本上更新 React 到 16.5 之后，我们的下一步是开始分析并捕获哪些组件被重新渲染。下面是工具显示我们在任何帖子上点击“喜欢”按钮的截图:

[![Slow renders screenshot](img/7405a3ce2516c9787bdcd87cbba18364.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3h6iT8tC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://files.logoscdn.com/v1/files/14522904/content.png%3Fsignature%3DNmNdRI26PmGCtI8F6Z-3-o99Le4)

NewsFeed 下面的蓝色块显示了 Feed 中的所有帖子都被调用了 render。如果加载了 10 个项目，`NewsFeedItem`及其所有子项目将被渲染 10 次。这对于小组件来说可能没问题，但是如果渲染树很深，不必要地渲染组件及其子组件可能会导致性能问题。当用户向下滚动页面时，提要中会加载更多的帖子。这导致 render 一直在顶部被调用，即使它们没有改变！

这似乎是一个尝试改变`NewsFeedItem`来扩展`PureComponent`的好时机，如果道具没有改变，这将跳过重新渲染组件及其子组件(该检查使用了浅层比较)。

不幸的是，仅仅应用 PureComponent 是不够的——概要分析再次显示不必要的组件呈现仍然在发生。然后我们发现了两个阻碍我们利用 PureComponent 优化的问题:

## 第一个路障:使用儿童道具。

我们有一个看起来像这样的组件:

```
<NewsFeedItem contents={item.contents}>
  <VisibilitySensor itemId={item.id} onChange={this.handleVisibilityChange} />
</NewsFeedItem> 
```

Enter fullscreen mode Exit fullscreen mode

这编译成:

```
React.createElement(
  NewsFeedItem,
  { contents: item.contents },
  React.createElement(VisibilitySensor, { itemId: item.id, onChange: this.handleVisibilityChange })
); 
```

Enter fullscreen mode Exit fullscreen mode

因为 React 在每次渲染时都会创建一个新的`VisibilitySensor`实例，所以`children`属性总是会改变，所以让`NewsFeedItem`成为`PureComponent`会让事情*变得更糟*，因为`shouldComponentUpdate`中的浅层比较运行起来可能并不便宜，并且总是会返回 true。

我们的解决方案是将 VisibilitySensor 移动到一个渲染道具中，并使用一个绑定函数:

```
<NewsFeedItemWithHandlers
  contents={item.contents}
  itemId={item.id}
  handleVisibilityChange={this.handleVisibilityChange}
/>

class NewsFeedItemWithHandlers extends PureComponent {
  // The arrow function needs to get created outside of render, or the shallow comparison will fail
  renderVisibilitySensor = () => (
    <VisibilitySensor
      itemId={this.props.itemId}
      onChange={this.handleVisibilityChange}
    />
  );

  render() {
    <NewsFeedItem
      contents={this.props.contents}
      renderVisibilitySensor={this.renderVisibilitySensor}
    />;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

因为绑定函数只被创建一次，所以同一个函数实例将作为道具传递给`NewsFeedItem`。

## 第二个路障:渲染时创建的内嵌对象

我们有一些代码在每个渲染中创建了一个新的 url 助手实例:

```
getUrlHelper = () => new NewsFeedUrlHelper(
    this.props.moreItemsUrlTemplate,
    this.props.pollItemsUrlTemplate,
    this.props.updateItemsUrlTemplate,
);

<NewsFeedItemWithHandlers
    contents={item.contents}
    urlHelper={this.getUrlHelper()} // new object created with each method call
/> 
```

Enter fullscreen mode Exit fullscreen mode

由于`getUrlHelper`是根据 props 计算的，如果我们可以缓存之前的结果并重用它，那么创建多个实例就没有意义了。我们用 [`memoize-one`](https://github.com/alexreardon/memoize-one) 来解决这个问题:

```
import memoizeOne from 'memoize-one';

const memoizedUrlHelper = memoizeOne(
    (moreItemsUrlTemplate, pollItemsUrlTemplate, updateItemsUrlTemplate) =>
        new NewsFeedUrlHelper({
            moreItemsUrlTemplate,
            pollItemsUrlTemplate,
            updateItemsUrlTemplate,
        }),
);

// in the component
getUrlHelper = memoizedUrlHelper(
    this.props.moreItemsUrlTemplate,
    this.props.pollItemsUrlTemplate,
    this.props.updateItemsUrlTemplate
); 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们将创建一个新的网址助手，只有当依赖的道具发生变化。

## 测量差异

分析器现在显示更好的结果:渲染新闻提要现在从大约 50 毫秒下降到大约 5 毫秒！

[![Better renders screenshot](img/43574672b5fb14b30d54cc64d4a9311f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_41QwIJ0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://files.logoscdn.com/v1/files/14522909/content.png%3Fsignature%3DFzQeT-m0XOUSU2hSJ7x2cYMqNlo)

## PureComponent 可能会让你的性能更差

与任何性能优化一样，衡量变化如何影响性能至关重要。

`PureComponent`不是一个可以盲目应用于应用程序中所有组件的优化。这对于具有深度渲染树的列表中的组件是很好的，在这个例子中就是这种情况。如果你使用箭头函数作为道具，内联对象，或者内联数组作为带有`PureComponent`的道具，那么`shouldComponentUpdate` *和* `render`都会被调用，因为每次都会创建这些道具的新实例！衡量你的变化的表现，以确保它们是一种进步。

对于您的团队来说，在简单的组件上使用内联箭头函数可能非常好，比如在循环中的`button`元素上绑定 onClick 处理程序。首先确定代码可读性的优先级，然后测量并在有意义的地方添加性能优化。

## 奖励实验

由于创建组件只是为了将回调绑定到 props 的模式在我们的代码库中很常见，所以我们编写了一个助手来生成带有预绑定函数的组件。在我们的 Github repo 上查看[。](https://github.com/Faithlife/react-util/tree/dcdeed8e2b24562766150661aaae8cfcd07c2de8/src/withEventHandlers)

你也可以使用窗口库，比如 [react-virtualized](https://github.com/bvaughn/react-virtualized) 来避免呈现不在视图中的组件。

感谢伊恩·蒙迪、帕特里克·诺沙和奥雷萨·奈卡特对这篇文章的初稿提供反馈。

来自 Unsplash 的封面照片:[https://unsplash.com/photos/ot-I4_x-1cQ](https://unsplash.com/photos/ot-I4_x-1cQ)