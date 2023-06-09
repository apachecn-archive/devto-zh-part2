# 观察风格变化👁

> 原文：<https://dev.to/oleggromov/observing-style-changes---d4f>

在从事我的一个灵感 OSS 项目时，我发现目前没有办法观察元素风格的变化。至少我找不到任何类似图书馆的解决方案。我认为原因可能是很难理解风格是否改变了。

于是，我决定自己写一个库，取名`SauronStyle`。如果你的项目需要这样的东西，请看看并[试一试](https://github.com/oleggromov/sauron-style)。

## 如何观察

离开*为什么？*幕后，让我们直接跳到*如何*。我记得有几种更新元素样式的方法:

*   直接更新其`class`或`style`
*   分别更新其父属性
*   在文档中的任意位置插入或移除`style`或`link`元素

为了观察这些，我们需要`MutationObserver`支持——现代浏览器(IE11+)支持的 DOM 变化观察接口。我想这和允许你在你最喜欢的 DevTools 的 Elements 窗格中观察子树或属性修改是一样的。

那么它给我们提供了什么呢？简单地说，就是监听属性变化(`class`和`style`属于这一类)以及子树修改(移除时的外部样式表插入)的能力。

## 如何检查差异

当我们知道*发生了变化*时，我们应该检查是否有任何*实际*的变化，因为我们注意到的变化可能完全不相关。为了做到这一点，我们将使用`getComputedStyle`——这是一个在`window`上有用的方法，任何从 IE9 开始的现代浏览器都支持它。它所做的是，它返回一个所有 CSS 属性的平面对象，其值类似于 Chrome 中 CSS *computed* 选项卡中的值。

重要的是，它返回一个*实时* [`CSSStyleDeclaration`实例](https://developer.mozilla.org/en-US/docs/Web/API/CSSStyleDeclaration)，该实例会随着时间的推移而变化，这迫使我们保留它的一个副本。

## 实现先睹为快

实际的[源代码](https://github.com/oleggromov/sauron-style)位于存储库中，顺便说一下，相当紧凑，但是您可能对查看一些细节感兴趣。

首先，我想观察被观察元素属性的变化。这很容易实现:

```
this.mutationObserver = new window.MutationObserver(this.checkDiff)
this.mutationObserver.observe(this.node, {
  attributes: true,
  attributeFilter: ['style', 'class']
}) 
```

Enter fullscreen mode Exit fullscreen mode

这段代码的作用是创建一个新的`MutationObserver`类实例，并向它发送一个回调函数`this.checkDiff`，作为唯一的参数。然后它说:只观察`this.node`中`style`和`class`属性的变化，并调用这些变化的回调。

稍后，在`this.checkDiff`中，我们想看看实际的样式是否已经改变:

```
checkDiff () {
  const newStyle = this.getStyle()
  const diff = getDiff(this.style, newStyle)

  if (Object.keys(diff).length) {
    if (this.subscriber) {
      this.subscriber(diff)
    }
    this.style = newStyle
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码获取当前样式，并将其与存储的副本进行比较。然后，如果有任何差异，我们存储新的以备将来使用，并调用一个已经设置好的订阅者函数。

`this.getStyle`返回`this.computedStyle`的浅层副本。

```
getStyle () {
  return getCopy(this.computedStyle)
} 
```

Enter fullscreen mode Exit fullscreen mode

其中`this.computedStyle`是对上述`CSSStyleDeclaration`实例的引用:

```
this.computedStyle = window.getComputedStyle(this.node) 
```

Enter fullscreen mode Exit fullscreen mode

### 观察其他元素

如果我们不关心其他元素，比如父元素的属性变化或者移除时的`style` / `link[rel=stylesheet]`插入，那就差不多了。为此，我们需要另一个实体，我称之为`DocumentObserver`，来观察文档子树的修改，包括属性的改变。在课堂上看起来是这样的`constructor` :

```
this.observer = new window.MutationObserver(mutations => mutations.forEach(this.observe.bind(this)))
this.observer.observe(window.document, {
  attributes: true,
  attributeFilter: ['class'],
  childList: true,
  subtree: true
}) 
```

Enter fullscreen mode Exit fullscreen mode

这与另一个`MutationObserver`用例非常相似，但是这里我们分别对待每个`mutation`，并观察`window.document`上的变化。这里我们大致这样说:观察`window.document`及其子节点的`class`属性修改和子节点插入/移除。然后调用`this.observe`进行任何相关的突变。

观察代码很简单:

```
observe (mutation) {
  if (mutation.type === 'childList') {
    this.checkElements(mutation)
  } else if (mutation.type === 'attributes') {
    this.invokeAll()
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

本质上，它检查突变的类型，并前进到相应的分支。要么是对`this.invokeAll`的调用，它只是调用所有订户，要么是一些附加的检查，目的是只有在插入了`link`或`style`元素时才调用`this.invokeAll`。

这个部分，即`DocumentObserver`，是从`SauronStyle`内部使用的，比如:

```
this.documentObserver = getDocumentObserver()
this.listenerId = this.documentObserver.addListener(this.checkDiff) 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们将它作为单例使用，因为我们只有一个文档。第二，我们对文档的相关更改签署相同的`this.checkDiff`。

## 问题

嗯，这似乎工作得很好，但有什么问题吗？

首先，性能低。我们经常打电话给`getComputedStyle`，一个电话需要几毫秒，在我的 MacBook '2013 上从 1 到 5-6。很慢。想象一页上有几千个你想观察的元素。对 DOM 更改做出反应需要几秒钟吗？是的，会的。

其次，该算法更多的是概念验证质量，而不是生产就绪。我们广泛地调用`checkDiff`方法，因为 DOM 中的任何变化有时都与我们观察到的元素无关。我猜这种额外的计算复杂性可以通过在 DOM 之外计算和存储元素样式来消除。但是这可能会导致更多的差异检测错误和更大的 T2 理解复杂性。

我也不确定我没有忘记影响元素样式的其他方法。

## 如何帮助

*   告诉我你是否需要过类似的东西
*   思考并分享你对任何其他可能发现风格变化的方法的想法
*   在 GitHub 上给库一颗星
*   在你的项目中使用它！👻

感谢您的关注！

另外，这篇文章还有一个[交叉发布到我的个人博客](http://oleggromov.com/notes/2018-02-observing-style-changes/)。如果您对其他与开发相关的文章感兴趣或者只是想与我联系，请看看。