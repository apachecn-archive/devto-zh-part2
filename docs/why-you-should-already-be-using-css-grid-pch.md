# 为什么你应该已经在使用 CSS 网格

> 原文：<https://dev.to/willamesoares/why-you-should-already-be-using-css-grid-pch>

[![header](img/30826cec8d62a251d833d15a7827995c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bS_SjGm5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4h381z8znyw4w0vcnv8z.png)

嘿大家好！

在这篇文章中，我将与你分享我收集的关于 CSS Grid 的一些东西，以及你如何使用它来构建更简洁的标记，从而提高项目的可维护性，这是它的众多优点之一。

CSS Grid 是众多闪亮技术中的一种，这些技术似乎突然出现并迅速普及，这让我们认为这可能是另一个 T2 炒作 T3 的事情。由于我们已经习惯了 web 开发世界中的这种事情，我们大多数人最终都会对尝试新的东西并了解新技术的实际效果持怀疑态度。

对我来说，CSS Grid 就是这样。就我而言，当我第一次听说它并观看来自 [Wes Bos 的 CSS 网格课程](https://cssgrid.io/)的视频时，我尝试了一下。我记得我发现它既有趣又令人畏惧，因为它改变了你在设计布局时的思维模式。此外，由于它目前的支持，我有不能在现实世界的项目中使用它的想法。如果我想使用它，我必须添加许多向后兼容的代码，所以对我来说这是一个*禁忌*。

事实证明，我需要在这个问题上做更多的研究，我很高兴我做到了。莫滕·兰德-亨德里克森的[演讲](https://dev.tube/video/7kVeCqQCxlk)极大地启发了这篇文章。

### 一些历史

CSS Grid 和浏览器中正式批准和发布的任何其他规范一样，自 2012 年由微软团队首次提交给 W3C 以来，已经经历了大量的讨论和实现。由于网格布局的想法已经流传了 20 多年，这并不是第一个被提出的规范。

不同的是，这一次，微软团队还展示了网格布局的真实实现。这有助于以更现实的方式评估提案。此外，一些人开始玩它并传播它，所以许多网格布局的真实例子开始出现。对我来说，这是真正帮助 CSS Grid 在社区中迅速流行的事情之一，这太神奇了！

从那以后，规范发生了很大的变化，在此之前完成的大部分工作也被考虑在内。于是，对最初的提议进行了大量的补充和清理。

带着对新酷小子的如此热情，到 2017 年第一学期结束时，Chrome，Firefox，Opera 和 Safari 已经提供了对 CSS Grid 的支持。看看它目前的支持:

[![css-grid-support](img/d4caeaa914ff7cb46ba89dce11614f3e.png "CSS Grid Support") ](https://res.cloudinary.com/practicaldev/image/fetch/s--8rf6R6e3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6hnlrdnczd4n8jf4c2k9.png) <small>提到的对 Opera 的支持仅针对 Opera Mobile。</small>

有趣的是，微软是最后一家在 Edge 中发布网格布局更新版本的公司。至于 IE10 和 IE11，支持的网格布局版本仍然是旧的，看起来这些是[没有得到更新](https://gridbyexample.com/browsers/)。

### CSS 网格隐语

与任何其他技术一样，了解与之相关的术语是一个很好的起点。在这一节中，我将列出在开始使用 CSS Grid 之前您应该记住的基本概念。

#### 容器

这是你的网格布局的根，并且包装了你的网格中的所有其他项目。为了定义一个网格容器，基本上是在想要成为网格容器的元素中将`display`属性设置为`grid`。

[https://codepen.io/willamesoares/embed/gKqBrW?height=600&default-tab=result&embed-version=2](https://codepen.io/willamesoares/embed/gKqBrW?height=600&default-tab=result&embed-version=2)

尽管在应用了这条规则之后，您看不到任何不同，但是现在您已经有了一个网格容器，可以开始在上面放置项目了。

#### 项

网格项是网格容器的每个直接子元素。在上面的例子中，你可以说所有的`span`元素都是你创建的网格容器的项目。

#### 曲目

网格轨迹是指布局中的*列*和*行*。对于构建基于网格的布局的人来说，这两个概念并不陌生，所以让我们看看如何在 CSS Grid 中定义它们。

[https://codepen.io/willamesoares/embed/WymGGx?height=600&default-tab=result&embed-version=2](https://codepen.io/willamesoares/embed/WymGGx?height=600&default-tab=result&embed-version=2)

在上面的例子中，你可以看到我们使用属性`grid-template-columns`和`grid-template-rows`来定义我们希望我们的列和行在我们的布局中如何出现。为这些属性传递的每个值代表我们正在创建的列和行的大小。

对于这个例子，我们定义了 3 列和 1 行，即第一列和最后一列的宽度为`1fr`(在下一节中解释)，中间一列的宽度是它的两倍:`2fr`。对于行，我们只定义了一行，但是由于有更多的项目要放在网格中，所以会自动为我们创建新的行。第一行的高度固定为`10rem`，其余各行的高度相等，相当于`1fr`。

#### 分数单位

分数单位或`fr`，代表列或行中可用空间的一部分。如果你回头看看上面的例子，你会看到我们为第一行创建了一个固定的高度，然后为剩下的每一行，我们让它们占用一小部分可用空间。基本上，留在容器中的高度将被平均分成网格中仍然存在的行数。

有趣的是，这里注意到您并不总是必须手动定义所有的列或行。在有大量轨迹的情况下，可以使用`repeat`方法创建轨迹模式，如下例所示。

[https://codepen.io/willamesoares/embed/xzBRGo?height=600&default-tab=result&embed-version=2](https://codepen.io/willamesoares/embed/xzBRGo?height=600&default-tab=result&embed-version=2)

这里我们为列创建一个模式。网格中每两列的第一列宽度为`1fr`，第二列宽度为`2fr`。这同样适用于行。我建议您在 codepen 中打开该示例，尝试更多的组合:)

#### 线条

你也可以根据亚麻编号摆放物品。当你创建一个 CSS 网格布局时，你可以选择指定你的项目将在哪一行开始和结束。请参见下面的示例。

[https://codepen.io/willamesoares/embed/aKMBrd?height=600&default-tab=result&embed-version=2](https://codepen.io/willamesoares/embed/aKMBrd?height=600&default-tab=result&embed-version=2)

您可以注意到，在示例中，我们使用行号来指定几个项目的位置。例如，网格中的第二个`span`将从第 2 行水平定位到第 5 行(您可以使用红线作为参考，从 1 开始，从左到右)。同样，相同的`span`将从第 1 行到第 3 行垂直定位(从上到下开始计数)。因此，第三个跨度将自动重新定位，因为第二个`span`占用了之前为跨度#3 预留的空间。另外，请注意，您可以使用速记属性`grid-column`或`grid-row`来设置同一规则中一个元素的开始和结束行。检查`span`的#5 和#6 是如何定位的，并使用它:)

#### 单元格

在网格布局中，单元格是您可以放置项目的每个单独的空间。基本上，您可以通过指定项目的起点和终点(正如我们在上一节中所做的)或使用网格区域，将项目放置在特定的单元格中或一组单元格中。

#### 区域

网格区域定义了一个命名块，可用于在网格模板区域中放置项目。让我们来看看它到底是如何工作的。

[https://codepen.io/willamesoares/embed/ZRPypG?height=600&default-tab=result&embed-version=2](https://codepen.io/willamesoares/embed/ZRPypG?height=600&default-tab=result&embed-version=2)

在这个例子中，我们用下面的块定义了一个网格模板区域:header、sidebar、main 和 footer。当您使用`grid-template-area`时，您可以通过使用您用于每个块的名称来指定您想要的行和列。

定义模板区域后，您必须将这些块名链接到标记中的每个元素。我们通过使用`grid-area`属性来做到这一点，如上面的例子所示。一旦将每个块分配到模板区域中的一个特定位置，您就可以移动`grid-template-area`属性中的行，并看到您不必为了移动项目而重新排序您的标记。简直管用！

一件有趣的事情是，如果你想在你的模板区域添加一些空白块，你可以在任何块名的位置使用一个点。参见下面的例子。

[https://codepen.io/willamesoares/embed/xzBLJQ?height=600&default-tab=result&embed-version=2](https://codepen.io/willamesoares/embed/xzBLJQ?height=600&default-tab=result&embed-version=2)

#### 缺口

一般来说，在你的物品之间留出一些空隙是有好处的。你可以在你的项目周围使用边距，但是在 CSS Grid 中有一个更好的方法。使用`grid-gap`属性。

[https://codepen.io/willamesoares/embed/WymZbZ?height=600&default-tab=result&embed-version=2](https://codepen.io/willamesoares/embed/WymZbZ?height=600&default-tab=result&embed-version=2)

### CSS 网格 vs Flexbox

在一篇文章中介绍了一堆新概念和术语后，你可能想知道 CSS Grid 相对于我们熟悉的以前的方法，比如 Flexbox，真正的优势是什么。

这两种技术之间的一个重要区别是，使用 Flexbox 时，您必须使用一维布局，因此您总是需要在特定时间将您的布局视为行或列。使用 CSS Grid，您可以以二维方式工作，这意味着您可以同时处理列和行！:)

对我来说，对 CSS Grid 如此热情的主要原因是现在我们可以编写更干净的标记。一个真正告诉你什么是你在页面中看到的真正元素的标记，而不是让你创建额外的`div`或任何其他你可能已经用于布局的唯一目的的 HTML 标签。

看看这两个分别用 CSS Grid 和 Flexbox 创建的例子。

#### CSS 网格

[https://codepen.io/willamesoares/embed/dKrzBb?height=600&default-tab=result&embed-version=2](https://codepen.io/willamesoares/embed/dKrzBb?height=600&default-tab=result&embed-version=2)

#### Flexbox

[https://codepen.io/willamesoares/embed/XYGebo?height=600&default-tab=result&embed-version=2](https://codepen.io/willamesoares/embed/XYGebo?height=600&default-tab=result&embed-version=2)

如果你看看这两个例子的 HTML 和 CSS，你会注意到 Flexbox 的标记看起来不像 CSS Grid 那样吸引人。您可以看到，为了放置布局中更复杂的部分，必须创建额外的块。至于 CSS Grid，不需要额外的块，所有的标记都是用户在页面中看到的真实内容。

### 结论

所以如果你仍然有这个问题“为什么是 CSS 网格？”以下是我想给你列举的几点:

*   更清晰的标记
*   增加布局的可维护性和灵活性
*   轻松实现更复杂的布局，因为您可以同时处理列和行
*   目前支持在全球网站流量的 [84.9%](https://caniuse.com/#feat=css-grid) ，肯定没问题。你不需要太多的后援(当然取决于项目需求)。

暂时就这样吧！感谢您的阅读，请告诉我您对 CSS Grid 的看法，以及您是否已经在生产中使用它:)

### 有用的链接

*   [本帖使用的 Codepen 集合](https://codepen.io/collection/DryRNN/)
*   [CSS Grid 的创造者的故事](http://alistapart.com/article/the-story-of-css-grid-from-its-creators)
*   [创建 CSS 网格-微软 Edge 开发展示](https://channel9.msdn.com/Blogs/msedgedev/Creating-CSS-Grid)
*   [网格示例-雷切尔·安德鲁](https://gridbyexample.com/)
*   [CSS 网格:从 CSS 框架到 CSS 网格(2018 年及以后)——丹尼尔·科斯金](https://dev.tube/video/paMmgo4MhQ8)
*   [CSS 网格改变一切——莫滕·兰德-亨德里克森](https://dev.tube/video/7kVeCqQCxlk)
*   [网格后退和覆盖](https://rachelandrew.co.uk/css/cheatsheets/grid-fallbacks)