# 我从为 React 构建自己的虚拟化列表库中学到了什么

> 原文：<https://dev.to/nishanbajracharya/what-i-learned-from-building-my-own-virtualized-list-library-for-react-45ik>

<figure>[![](img/e5b11c7f15fffae7d93c58d83e510662.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_84M-Nap--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ASBgZ6Rv3YqS_SzweL4EfZQ.png) 

<figcaption>虚拟化 vs 非虚拟化列表</figcaption>

</figure>

我正在做的一个项目有一个联系人列表，平均需要显示 5000 个联系人。这意味着需要在屏幕上呈现 5000 个包含大量嵌套组件的复杂 DOM 元素。当我们开始构建这个的时候，我们就知道渲染这么多这样的元素将会是一个非常繁重的 DOM 操作。和 React，即使如此努力，如果我们将它们映射到我们的视图中，也绝对会呈现所有的 DOM 元素，即使这会降低我们的呈现性能。

```
{
  LIST_WITH_5000_ITEMS.map(item => <ComplexListItem {...item} />)
} 
```

Enter fullscreen mode Exit fullscreen mode

所以我们选择了比这更好的方法。我们的第一种方法是使用一个无限滚动组件，一次加载 250 个联系人，到达列表末尾后，再加载 250 个联系人。这使得最初的渲染性能更好，但当您滚动到末尾时，会开始变得相当慢。这并没有解决手头的原始问题。

我们的第二种方法是使用一种叫做虚拟化列表的东西。这修复了与列表 DOM 和滚动相关的大多数底层渲染和性能问题，我们最终可以专注于优化联系人列表逻辑的非 DOM 相关问题，如繁重的 fetch API 请求。

### 什么是虚拟化列表？

虚拟化列表是一种技术，其中可滚动视图中的大型项目列表被虚拟地呈现以仅示出在可滚动视图窗口内可见的项目。这听起来可能很难，但它基本上意味着虚拟化列表只呈现屏幕上可见的项目。

### 为什么需要这样？

考虑一个处理大量项目列表的应用程序，比如一个列表中有 10000 行。呈现这个列表的最好和最有效的方法是什么？有人会说是分页列表，当页面改变时，其中的行被卸载。但是无限滚动视图呢？当我们向底部滚动时，无限滚动视图会在当前视图的底部添加行。每一行都被渲染，当我们向下滚动时，更多的行被渲染。

我们可以有一个一次只能显示 20 个项目的视图窗口，但是我们要呈现数万个 DOM 元素。这是非常无效的，并导致大量的缓慢滚动和无反应的列表。这不好。

理想情况下，我们只渲染可见的项目。视图窗口之外的项目不需要渲染能力。只有当它们进入可见窗口时，才需要渲染它们。这就是虚拟化列表*进入视图*的地方。

### 这是个新想法吗？

<figure>[![](img/53e83a5c7178f1b2e0e4cfe915b6e0d4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--M3FDucES--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ADwChLxQKz-JWSK2fw4Bgpw.png)

<figcaption>react-虚拟化由布莱恩·沃恩</figcaption>

</figure>

不。事实上，虚拟化列表的想法已经存在很长时间了。Android 开发自 2014 年以来就有了[回收器视图](https://developer.android.com/guide/topics/ui/layout/recyclerview)，React Native 提供了开箱即用的[虚拟化列表](https://facebook.github.io/react-native/docs/virtualizedlist.html)组件，对于 React 开发者，我们有一个高度可扩展的库，名为 [react-virtualized](https://github.com/bvaughn/react-virtualized) 。这些库和特性可能有不同的实现，但是它们都试图解决同一个问题。

### 接下来发生了什么？

我开始对虚拟化列表的幕后工作方式感兴趣。虚拟化列表没有任何您可能期望的滚动工件，因为它正在呈现进入视图的新项目，并且正在取消呈现从视图中消失的项目。它的工作原理与非虚拟化列表基本相同，当拖动滚动条时，在一侧显示滚动条，功能相同。

那么它是如何模仿这些行为的呢？它是如何跟踪视图中的元素的呢？当我开始研究虚拟化列表的内部工作时，这些都是我脑海中的燃眉之急。不久前，我从一个朋友那里读到了一篇关于虚拟化列表的博客，我所记得的是，不是像通常那样呈现列表，而是在元素上使用 position: absolute 并跟踪列表的滚动位置。

所以我开始以 React 库的形式实现我自己的虚拟化列表，我*称之为[React-virtualized-listview](https://github.com/nishanbajracharya/react-virtualized-listview)。由于我已经使用过 react-virtualized，我从该库的 API 中受到了很大的启发。我还想要比 react-virtualized 提供的各种功能更简单的东西。不要误解我的意思，react-virtualized 绝对是 react 可用的最好的库之一，但是由于它提供的各种各样的特性，一开始有点令人生畏。我想要更容易使用的东西，在构建库的过程中，我会理解虚拟化列表是如何工作的。*

这在代码中如何工作的一个例子:

```
const data = [1, 2, 3, 4, 5];

<List
  source={data}
  rowHeight={40}
  renderItem={({ index, style }) => (
    <div key={index} style={style}>
      Hello {index}
    </div>
  )}
/> 
```

Enter fullscreen mode Exit fullscreen mode

### 那么它是如何工作的呢？

<figure>[![](img/e8f3f356282b70d5a9978ada7af3aa08.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gcYY14Nq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A1tLLMpZgodT96tPnbxGH2w.png) 

<figcaption>道具的一个渲染列表项</figcaption>

</figure>

我们来看一个 1000 行的列表。比方说每行 20pxin 高。所以我们会有一个高度为 20000px 的列表。这是虚拟化列表的起点。它创建一个与其父可见窗口的宽度尺寸相匹配的 DOM 元素，其高度等于项目总数乘以项目高度(这里是 20000px)。这使得滚动条的位置完美地模仿了非虚拟化列表的行为。因此，在列表上滚动鼠标滚轮和拖动滚动条都可以正常工作。此时 DOM 元素为空。

接下来，我们跟踪列表中的滚动位置。这是虚拟化的关键部分。滚动位置决定了我们在列表中的位置。该索引与可见窗口的高度一起决定了可见的和需要呈现的索引。要呈现的列表项被赋予一个 position: absolute 样式和一个使用该项的索引和该项的行高计算的顶值。所以我们只呈现与计算出的索引相匹配的项目。

我们用来模仿非虚拟化列表的另一个技巧是*过扫描。*我们在可见窗口的上方和下方呈现了少量不可见的项目，以便在滚动时看起来像其他项目一样存在，而不是当它进入可见窗口时才突然出现在视图中。

虚拟化列表的 codesandbox 示例

[在 Codesandbox 上反应虚拟化的 listview](https://codesandbox.io/embed/r5kxmymx8q)

关于虚拟化列表的更多信息。

*   [nishanbajracharya/react-virtualized-listview](https://github.com/nishanbajracharya/react-virtualized-listview)
*   [bvaughn/react-虚拟化](https://github.com/bvaughn/react-virtualized)
*   [使用 React 虚拟化呈现大型列表](https://dev.to/bnevilleoneill/rendering-large-lists-with-react-virtualized-2p8b)
*   [虚拟化虚拟 DOM —进一步推动 React】](https://medium.com/outsystems-engineering/virtualizing-the-virtual-dom-pushing-react-further-d76a16e5f209)
*   [普通 JavaScript 中的虚拟列表](https://sergimansilla.com/blog/virtual-scrolling/)