# CSS:用后续的兄弟组合符选择第一类

> 原文：<https://dev.to/philnash/css-select-first-of-class-with-the-subsequent-sibling-combinator-13li>

有一大堆 CSS 选择器可供 web 开发者使用，但有时还是不够。我最近在构建我的网站的[口语部分时发现了这一点，并想使用不存在的`:first-of-class`伪类来应用一些风格。](https://philna.sh/speaking/)

## 问题

伪类`:first-of-type`确实存在，但是有限。它是`:nth-of-type()`伪类的一个特例。从规格(强调我的):

> :n-of-type(An+B)伪类符号表示将由以下元素匹配的相同元素:n-child(| An+B | of S)， ***，其中 S 是类型选择器*** 和匹配所讨论元素的名称空间前缀。

这意味着，当使用`:nth-of-type()`或`:first-of-type`时，选择器只能是类型选择器。也就是说，当使用`:first-of-type`时，你只能直接引用元素，比如`<p>`或`<h1>`。所以，如果你有下面的 HTML:

```
<div>
  <p>This is paragraph 1</p>
  <p class="special">This is paragraph 2</p>
  <p class="special">This is paragraph 3</p>
</div> 
```

还有这个 CSS:

```
div p { color: #333; }
div p:first-of-type { color: red; }
div p.special:first-of-type { color: green; } 
```

你可能会认为第一段是红色的，第二段是绿色的。但是`div p.special`不是类型选择器，所以`:first-of-type`在这里不适用，没有什么是绿色的。

## 后续的兄弟组合子

这可以通过由[陈家风](https://stackoverflow.com/questions/2717480/css-selector-for-first-element-with-class)和[利·韦鲁](https://stackoverflow.com/questions/5287272/css-select-first-element-with-a-certain-class/5293095#5293095)创造的技术来解决，并在堆栈溢出上共享。我们可以使用[后续或通用兄弟组合符](https://www.w3.org/TR/selectors/#general-sibling-combinators)，而不是使用伪类。

后续的兄弟组合子看起来像`A ~ B`，其中`A`和`B`是两个复合选择器(不仅仅是类型选择器)。它允许您选择`B`，其中`A`和`B`共享一个父级，并且`A`在文档中位于`B`之前。

使用下面的 HTML，选择器`h2 ~ p`将选择第二个`<p>`，因为它跟在`<h2>`后面，而不是第一个`<p>`。

```
<div>
  <h1>Heading</h1>
  <p>Meta data</p>
  <h2>Subheading</h2>
  <p>Some article text</p>
</div> 
```

## 后继兄弟组合子和:一流

使用组合子来模拟一个`:first-of-class`伪类的技巧是使用一个常规的选择器用你想要的样式来设计类的所有元素。然后使用组合子关闭除第一个元素之外的所有元素。在我们最初的例子中，CSS 现在看起来像这样:

```
div p { color: #333; }
div p:first-of-type { color: red; }
div p.special { color: green; }
div p.special ~ p.special { color: #333; } 
```

现在第一段是红色，第二段是绿色，最后一段是灰色。你可以在 Codepen 上查看这方面的一个实例。

## CSS 选择器第 4 级

在贴出这篇文章后，[ime Vidas](https://twitter.com/simevidas)[在 Twitter](https://twitter.com/simevidas/status/975394813863432192) 上指出，CSS 的未来对这种风格的选择器更有希望。在 CSS 选择器级别 4 规范中，`:nth-child()`伪类采用了一个类似于`An+B [of S]?`的参数。`An+B`部分意味着你可以提供一个函数来计算`n`是什么，但是可选的`of S`意味着伪类将匹配匹配选择器的第 n 个元素。

这意味着我们可以更新我们的例子来使用`:nth-child()`而不是我们上面所做的两个规则。看看下面的 CSS:

```
div p { color: #333; }
div p:first-of-type { color: red; }
div :nth-child(1 of p.special) { color: green; } 
```

使用`:nth-child(1 of p.special)`意味着我们正在选择`<div>`的第一个孩子，这是一个具有“特殊”类的`<p>`。这正是我想要的。

这种技术的唯一缺点是？目前只能在 Safari 中使用。我已经更新了 [Codepen，在 Safari 中查看一下，看看你所有的未来选择器梦想都实现了](https://codepen.io/philnash/pen/WzoNwG/)。

## CSS 黑客依然好玩

我断断续续写 CSS 已经十多年了，虽然 CSS 所有的现代布局功能意味着黑客攻击少了很多，但有时你需要想出一个创造性的解决方案来解决问题。我本可以用一个额外的类或者重新安排我的 HTML 来解决这个问题，但是当 CSS 可以为你做这件事的时候，你会感觉更满意。

感谢 Daniel 和 Lea 分享他们关于堆栈溢出的解决方案，特别感谢 Daniel，他的[回答更详细地介绍了 CSS 选择器和伪类误解](https://stackoverflow.com/questions/2717480/css-selector-for-first-element-with-class/8539107#8539107)。

也许有一天我们会看到一个`:first-of-class`伪类。有了最新版本的`:nth-child()`，也许我们甚至不需要`:first-of-class`，我们只是在等待浏览器的支持。与此同时，后续的兄弟组合子仍然是我们的朋友。

* * *

*[CSS:用后续的兄弟组合子](https://philna.sh/blog/2018/03/18/css-first-of-class/)选择一流最初发表于 2018 年 3 月 18 日 [philna.sh](https://philna.sh) 。*