# 理解 CSS 位置味道的不同配对

> 原文：<https://dev.to/anabella/understanding-the-different-pairings-of-css-position-flavours-5855>

*这个故事最初发表在的免费代码营上。
封面照片由[汤姆·赫曼斯](https://unsplash.com/photos/O6nOEZcLCWQ?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在[Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)T8】*

当你开始学习 CSS 的时候，position 属性可能是一个很神秘的东西。这就像给了一组未知的香料，它们看起来相似，但味道非常不同，它们之间的组合并不总是像预期的那样工作。

本文试图描述它们之间的最佳和最常见的配对，以便您可以在第一个网络烹饪步骤中轻松应用它们。就像我们在厨房里学到的一样——欧芹和大蒜搭配很好，但和肉桂搭配就没那么好了。

### 了解属性

首先，让我们定义每个位置变量的味道:

#### 静态

这是每个 HTML 元素默认拥有的。这意味着元素将根据正常的文档流进行定位。具有全宽度的块元素，行内元素对齐。这基本上是他们所有人的盐。

#### 相对

具有相对位置的元素可以相对于它们在正常文档流中占据的空间来放置。它们仍然只占据那个空间**，但是支持`top`、`right`、`bottom`和`left`属性。
分配给这些属性的任何值都将用于使用其实际位置边界计算其显示位置。就像加点胡椒粉，一般不会有太大危害。**

 **#### 绝对

这个很棘手——有点像孜然，可以是很好的添加物，但是你必须小心使用。绝对元素从普通文档流中删除。这意味着它们不影响页面中的其他元素，也不受其影响。然而，他们将被放置在**相对**(是的，我知道，请耐心等待)离他们最近的祖先。这意味着位置**被显式设置**的父元素。也可以使用`top`、`right`、`bottom`和`left`进行微调。所以它类似于相对定位，但是，因为它不再是文档流的一部分，所以它使用父文档作为引用。

#### 固定

啊，这个很简单。固定元素根本不是文档流的一部分，它们的位置基于整个窗口，有时也称为视口。此外，它们不受滚动的影响。

* * *

#### 开始烹饪前的快速提示

在使用`top`、`right`、`bottom`和`left`时，不建议使用`margin`和/或`padding`。后者将*偏移*元素的可见位置，而前者将偏移其**真实位置和影响文档的**位置。

如果你在同一个元素上使用两者，事情会变得混乱，所以最好把它们分开。

* * *

### 那么，怎么才能把它们混合起来呢？

每当你想把一个元素*从正常位置偏移一点点*的时候，使用`position: relative;`，但是你不想让其他的东西跟着它移动。请记住，所有其他元素都将表现得好像没有移动过一样。使用`top`、`right`、`bottom`和`left`将其相对*调整到其自然位置。*

[https://codepen.io/anabellaspinelli/embed/djeqZP?height=600&default-tab=result&embed-version=2](https://codepen.io/anabellaspinelli/embed/djeqZP?height=600&default-tab=result&embed-version=2)

当你关心元素相对于它的第一个父元素或具有明确(通常是相对)位置的包装器的位置时，使用`position: absolute;`。请注意，位置属性**不会级联**，所以这就是为什么它会使用最近的带有显式位置声明的父级，如果没有，那么它将是`<body>`。如果要强制层叠位置属性，可以声明为`position: inherit;`。

请记住，该元素的位置是由其最近的父元素的大小和形状定义的，因此如果您更改它，该元素也可能会受到影响。

[https://codepen.io/anabellaspinelli/embed/ajGaPo?height=600&default-tab=result&embed-version=2](https://codepen.io/anabellaspinelli/embed/ajGaPo?height=600&default-tab=result&embed-version=2)

最后，`position: fixed;`是一个有趣的玩物。正常使用包括粘性导航条、页脚或侧菜单。请记住，这超出了正常的文档流程，因此这意味着:

*   你可以把它放在任何你想放的地方，任何东西都不会损坏
*   这也意味着它不会做任何你没有明确告诉它去做的事情，所以你需要设置它的 2 坐标来显示它。

这两个坐标(即顶部或底部，加上左侧或右侧)将从窗口边缘开始测量。

(提示:上下滚动👇)
[https://codepen.io/anabellaspinelli/embed/PBeyoO?height=600&default-tab=result&embed-version=2](https://codepen.io/anabellaspinelli/embed/PBeyoO?height=600&default-tab=result&embed-version=2)

#### 粘性免责声明

还有一个选项我没有提到，那就是`position: sticky;`。它使元素正常地运行和滚动，但是当其余内容继续滚动时，元素会停留在某个位置。我决定不讨论它，因为它仍然是实验性的，超出了本文的基本理解范围。但是，如果你好奇，[这里有一篇文章](https://alligator.io/css/position-sticky/)展示了这一切。

* * *

我计划通过研究来解决这些我从未完全理解的关于 web 开发的基本问题，然后通过写相关的文章来测试我的新知识。因此，期待更多的深入了解我的基本知识吧！**