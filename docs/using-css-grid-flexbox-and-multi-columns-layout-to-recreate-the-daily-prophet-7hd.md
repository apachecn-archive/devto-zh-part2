# 利用 CSS 网格、flexbox 和多栏布局再造《预言家日报》

> 原文：<https://dev.to/analizapandac/using-css-grid-flexbox-and-multi-columns-layout-to-recreate-the-daily-prophet-7hd>

最近，我发布了一篇关于我如何使用 CSS flexbox 创建了一份[红洋葱的《预言家日报》](http://redonion.se/cssgrid/)的[文章](https://dev.to/analizapandac/creating-a-copy-of-red-onions-daily-prophet-using-css-flexbox-1klo)。自从我开始学习 CSS grid 以来，我决定通过使用两个布局模块和多列布局来再次实现它。

然而，在我们深入讨论之前，让我先澄清一下，因为我认为这真的很重要。

在发表我的前一篇文章的第二天，我偶然发现了来自 Smashing Magazine 的一篇关于 flexbox 的文章，我意识到 **flex-direction** 有一个重要的方面我完全误解了。**我以为行总是从左到右开始。你猜怎么着，并不总是这样。**

> 行和列的显示取决于您使用的书写模式，而不是屏幕的物理尺寸。

因此，如果你使用英语，它使用默认的书写模式，即**水平-tb** ，这意味着内容从左到右水平流动，从上到下垂直流动。下一条水平线位于上一条线的下方。

然而，如果你正在使用其他使用不同书写模式的语言，比如希伯来语和阿拉伯语，它们是从右向左书写的**，那么你的 flex 项目将从右开始**。或者，如果书写模式是从上到下，那么您的行项目将像列一样显示。

好了，现在完成了，让我们开始吧。

由于我之前主要讨论了 flexbox，所以让我们仔细看看网格布局的用法。

# 将网格布局和 flexbox 一起使用

我如何决定何时使用 grid 或 flexbox？其实很简单。

我在处理*二维部分*时使用 CSS grid(行和列都有，例如主故事部分)。

Flexbox 用于简单的*一维节*(只有行或列，如出版物名称节)以及文本和元素的对齐。

# 划分列

要指定网格容器中的列数(和宽度)，必须使用 **grid-template-columns** 属性。

让我们来看看主要的故事部分。
[![](img/26839691ea60225e1a4cdf0b0441def8.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--QgmkrUIx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o3cru9rjgi8ydtc4ffp6.png)

正如你所看到的，故事左边的内容占了整个章节的四分之三(3/4 ),而右边的内容占了四分之一(1/4)。

起初我用值 **1fr 250px** 声明了 **grid-template-columns** ，但是当我把屏幕变小时，左边部分的内容被压扁了。原因是右边部分将总是占用网格父级的 250px，而左边部分将占用剩余的可用空间，因为我用一个 **`fr`** 值(空闲空间的一部分)声明了它。

所以我修改成这样:

```
<div class="story--main">
  <div class="section--left">
  </div>
  <div class="section--right">
    </div>
</div>

.story--main {
  display: grid;
  grid-template-columns: 3fr 1fr;
}

.story--main .section--left {}
.story--main .section--right {} 
```

Enter fullscreen mode Exit fullscreen mode

**3fr 1fr** 表示左边部分将总是占据可用空间的四分之三，而四分之一将被分配给右边部分。这也意味着每当您调整视口大小时，列将根据网格父级的大小自动调整其宽度。

# 使页面响应迅速

我之前做的 flexbox 练习没有响应性，但这次，我通过使用媒体查询和在特定视窗大小上操作网格模板列的值来处理响应性。

```
.parent {
        display: grid;
        grid-template-columns: repeat(2, 1fr);
        gap: 10px;

        @media (min-width: 768px) {
          & {
            grid-template-columns: repeat(6, 1fr);
          }
        }
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，在 iPad 和桌面视图中，列将以这种方式显示。
[![](img/c5ae1d81773bae01c59a545a011c681e.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--jF4I_jME--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gtad3mjx9jd09fje89nl.png)

而在 mobile 上，由于我把它指定为 **repeat(2，1fr)** ，所以会显示成这样。
T3![](img/afdc023f6618db01267223746857ae2f.png)T5】

### 重复()

而不是写成**grid-template-columns:1fr 1 fr 1 fr 1 fr 1 fr；**非常重复，您可以使用 **repeat()** 函数，这样 **repeat(6，1fr)** 将重复指定宽度 6 次，从而产生相同的结果。

我还在一些部分使用了 **repeat(auto-fill，minmax(min，max))** 来自动计算重复次数。

实际上，我对自动填充和自动适应的区别感到困惑。如果有人知道，请告诉我。:D

# 对仅包含文本的部分使用多栏布局

对于一些只包含由列分隔的文本的部分，我决定不使用 grid 或 flexbox，而是使用另一个名为 multi-columns layout 的现有布局模块来产生相同的效果。

```
<div class="paragraphs_wrapper">
 <p>Veteran Captain Oliver Wood did not seem too anxious about the hurricane threat.</p>
 <p>Ducimus qui blanditiis praesentium voluptatum delenit.</p>
 <p>Harum quidem rerum facilis est et expedita distinctio. Nam libero tempore, cum soluta nobis est eligendi optio.</p>
</div>

.paragraphs_wrapper {
  column-count: 2;
}

.paragrahps_wrapper {
  p {
    // to ensure that the paragraph will not be broken into two columns
    page-break-inside: avoid;
    break-inside: avoid;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

产生了这个结果。
[![](img/31a6f1be8177e4ddb1c1f2a86b43050f.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--tXGmfAVL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hvulkw7o44xdclnv81h6.png)

# 结论

使用所有这些布局模块帮助我理解了它们最有用的场景，以及如何利用它们的优势来制作更好的页面。我对这些可能性感到非常兴奋。

感谢阅读，可以查看页面 [@CodePen](https://codepen.io/analizapandac/pen/EeeXpp) 。

有任何反馈/意见，请告诉我。

**预言家日报版权归[红洋葱](http://redonion.se/en/home/)所有。*