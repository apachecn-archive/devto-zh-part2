# 使用 CSS 网格的键盘显示

> 原文：<https://dev.to/gksander/a-keyboard-display-using-css-grid-2k2n>

# 一点脉络

我当然不是专业的 web 开发人员/设计人员，所以当我发现让我的生活变得更容易的工具时，我会很兴奋。目前，我对 CSS 网格很感兴趣。我知道 CSS grid 已经有一段时间了，但是我刚刚遇到了一个项目，这个项目让我真正明白了为什么 CSS grid 如此伟大。我想分享一下我的经历。

我以做在线数学课程开发(和教数学)为生，但是我最近开始做自由的网络开发工作(因为我真的很喜欢学习网络开发。).我开始为一个客户做一个项目，包括创建基于网络的打字课程。需求的一部分是创建一个非交互式的屏幕键盘，当击键时按键会发光。

这将是一个有趣的任务，因为键盘的布局不是很“矩形”，也就是说，键是偏移的，有些键比其他键更宽或更高。这是我最后得到的结果:

[![Keyboard preview](img/f80d89b12b831d586c909336b130ce33.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_8d62Zv6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6ubmcrp40c3np2yfxzzy.png)

# 引入“网格”

我注意到 15 个“正常大小”的键可以放在键盘的一行上，但是有些行混合了“宽”键和“正常”键。为了处理这个问题，我最终将键盘容器(QWERTY 键盘，上图左侧)分成了`15*4 + 14 = 74`个小列。每个“正常”键跨越这些列中的 4 列，键之间的每个间隙跨越 1 列，更宽的键跨越各种列。键盘的每一行仅跨越 1 行，其高度是每列宽度的 4 倍。如下所示:

[![Keyboard grid](img/a70bfee1fbe05293615d0eb93764d374.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--t9nuFMQ1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/grj7003d3u75qaa3qyfa.png)

为此，我有一个网格容器`div#qwerty_container`来放置所有的键，然后所有的键都是这个容器的直接子元素。这个容器的 CSS(我用的是 SASS)如下:

```
$key_size: 32px

#qwerty_container
    display: grid
    grid-template-columns: repeat(74, $key_size/4)
    grid-template-rows: repeat(5, $key_size)
    grid-column-gap: 0px
    grid-row-gap: $key_size/4 
```

Enter fullscreen mode Exit fullscreen mode

`display: grid`表示该元素具有网格布局。`grid-template-columns`表示列的大小，我使用 CSS `repeat()`函数来节省大量的输入。类似地，`grid-template-rows`表示行的大小。

然后，这个容器的每个子容器都被应用一个以`.g-`为前缀的类，以指示它应该跨越多少列。举个例子，一个普通的键应该有一个类`.g-4`，这个类包含以下内容:

```
.g-4
    grid-column: auto / span 4 
```

Enter fullscreen mode Exit fullscreen mode

差不多就这么多了！`grid-column: auto / span 4`表示这个类不应该影响元素的起始列，但是元素应该跨越 4 列。我有一个 JS 数组中的键列表，以及它们应该跨越多少列，然后遍历这个数组，使用 Vue.JS 输出网格元素。

# 为什么不是 Flexbox？

我不是 CSS 专家，但我认为这可以使用 Flexbox 来完成，因为我实际上并没有做任何“垂直”的事情。但是，右侧的数字键盘(见第一个图像)有跨越多行的键。现在，这是网格真正发光的地方！我用与 QWERTY 键盘基本相同的方式创建了数字键盘。这是数字键盘的网格外观:

[![Number pad grid](img/678e5e364c9ff6c4fe5fc705ec8fce34.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AZnLI-1b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8h999sc8i013wkvfzroc.png)

但是，加号键和回车键都跨越两行。两个处理它，我只是给这两个键添加了一个 id，并为每个键添加了仅仅三行额外的 CSS 代码。例如，假设加号键的 id 为`numpad_plus`。然后，我们可以将以下内容添加到我们的 SASS 文件中:

```
#numpad_plus
    grid-row: auto / span 2
    height: 100%
    justify-content: center 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，`grid-row: auto / span 2`表示这个键应该从它的“普通行”开始，例如，我们不改变它从哪一行开始，但是这个键应该跨越 2 行。基本上，这个键应该跨越两行(以及两行之间的间隙)。`height: 100%`和`justify-content: center`只是为了按键美观。

# ...但是它有反应吗？

键盘占据了相当大的水平空间，所以我想确保它在较窄的屏幕上看起来不错。CSS Grid 实际上使这很容易实现。我选择了几个断点，并在每个断点处添加了一个媒体查询。在每个断点处，我选择一个“比例因子”，比如 0.8，并按照这个比例因子来缩放与网格相关的所有内容——使键盘大约为其原始大小的 80%。不用做太多额外的工作，我就能很好地“缩小”键盘以适应更小的屏幕。

# 结论

这当然不是关于 CSS 网格的全面指南(为此我推荐 [CSS Tricks 的文章](https://css-tricks.com/snippets/css/complete-guide-grid/))，但对我来说，这是一次有启发性的经历，让我看到了网格的美丽。我想我应该与他人分享这一经历。

我也意识到我几乎没有挖掘网格的潜力——这很令人兴奋，因为这意味着我有更多的东西要学习和体验网格！