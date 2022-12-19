# 使用 CSS 网格

> 原文：<https://dev.to/sirech/playing-with-css-grid-646>

<figure>[![Pre-Twitter Grid](../Images/79aee14a0273bf17b08644c8c8de7ce6.png "Pre-Twitter Grid")](https://res.cloudinary.com/practicaldev/image/fetch/s--7-bwjT-_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hceris.com/static/89342629b7da9773def6e3847e2c40e9/acb04/grid.jpg) 

<figcaption>
[电力网|温尼伯](https://www.flickr.com/photos/jrladia/7151535423)
 <small>由贾斯汀·拉迪亚/ [抄送由](https://creativecommons.org/licenses/by/2.0/)</small> 
</figcaption>

</figure>

我第一次听说 *CSS 网格*布局是在[全栈节 2016](https://2016.fullstackfest.com/) 上，在 [Jen Kramer](http://www.jenkramer.org/) 的一次谈话中。我记得当时觉得这是个很酷的主意。然而，它似乎只是没有支持，因此我们不得不继续使用经典的网格。

与此同时，网格已经慢慢地从浮动移动到柔性盒，就像 T2 的引导程序一样。我想这就是进步。每当我加入一个新项目时，我总是惊讶地发现自己编写的网格实现。这已经发生在我身上很多次了。我想人们只是喜欢他们的格子。

无论如何，似乎最近的支持已经到了用它们来构建布局变得可行的程度。它甚至出现在了 ThoughtWorks 的雷达上。根据[can use](https://caniuse.com/#feat=css-grid)的说法，如果你支持 *Edge* 及以上，你实际上是好的。IE11 仅部分支持前缀，这可能是个问题。

## 学习网格

为了获得更好的感觉，我看了一些非常好的指南:

*   [https://css-tricks.com/snippets/css/complete-guide-grid/](https://css-tricks.com/snippets/css/complete-guide-grid/)
*   [https://developers.google.com/web/updates/2017/01/css-grid](https://developers.google.com/web/updates/2017/01/css-grid)

这感觉有点像我第一次了解到*flex box*的时候。有很多选择和复杂性，但我得到的印象是，一旦你习惯了它，你就会在任何地方使用它。因此，也许我们将最终摆脱所有的行和列，我们已经建立了最后几年。

我构建了一些小例子来展示您可以用它做什么。

## *例 1* :主体结构

[https://codepen.io/sirech/embed/wmLbby?height=600&default-tab=result&embed-version=2](https://codepen.io/sirech/embed/wmLbby?height=600&default-tab=result&embed-version=2)

大多数网站遵循类似的结构，有一个主要内容，然后可能是导航栏和侧边栏。有了 *CSS Grid* 你可以用一种非常灵活的方式来构建它，并通过使用媒体查询使它适应移动设备。我最喜欢的一点是，你可以用一个非常紧凑和语义化的 html 布局来做这件事。如果你使用类似于*引导*的东西，和上面一样的布局可以是这样的:

```
<div class="container-fluid">
  <div class="row">
    <div class="col">
      <nav>
        <h2>Navigation</h2>
      </nav>
    </div>
  </div>

  <div class="row">
    <div class="col-12 col-sm-9">
      <main>
        <h2> Main content</h2>
      </main>
    </div>
    <div class="col-12 col-sm-3">
      <aside>
        <h2>Sidebar</h2>
      </aside>
    </div>
  </div>
</div> 
```

这么简单的东西嵌套太多了。还有，如果你想在高度上有所作为，你需要自己去做。我更喜欢纯网格解决方案。我不喜欢传统网格的一点是用于建立列间空白的负边距和填充量(我只记得在一行中有一个全宽图像有多难)。相反，只需使用`grid-gap`就能让一切变得容易得多。

## *例 2* :矩阵

flexboxes 的问题是它们只允许你控制一个方向的流量。有时候你想构建更复杂的组件，比如一个图库。假设您想要构建一个网格，并在其中放置不同大小的图片。

[https://codepen.io/sirech/embed/mxZYQR?height=600&default-tab=result&embed-version=2](https://codepen.io/sirech/embed/mxZYQR?height=600&default-tab=result&embed-version=2)

这是一个 3x3 的网格，带有自动对齐的不同大小的图片。另一个给你很大灵活性的很酷的特性是你可以让元素跨越多行或多列。

## *例 3* :对齐

*CSS 网格*在对齐方面非常灵活。它支持 flexbox 支持的许多属性，比如主容器的`justify-items`、`align-items`、`justify-content`和`align-content`，或者单个项目的等效属性。

[https://codepen.io/sirech/embed/aGpvWg?height=600&default-tab=result&embed-version=2](https://codepen.io/sirech/embed/aGpvWg?height=600&default-tab=result&embed-version=2)

## 给我格子！

这看起来真棒。CSS Grid 允许你以一种比我们目前所做的更自然的方式来构建布局，并为控制元素的高度开辟了许多新的可能性，这是你以前不容易做到的。不仅如此，它还能与 flexboxes 很好地协同工作。您可以用网格构建主页面结构，然后用 flexboxes 设计单个元素的样式，同时使用一组非常一致的属性。这无疑感觉像是构建高质量页面的未来。