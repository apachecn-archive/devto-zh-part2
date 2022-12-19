# 衡量绩效(第二部分)

> 原文：<https://dev.to/joshua/measuring-performance-part-2-3648>

性能是个问题。证据是显而易见的——性能会影响您的业务目标、您的用户的幸福，并且它对连接性差的地区的影响甚至超出您的预期。

这真的很容易让人立刻想到，“我已经知道该怎么做了！让我们来解决这个问题”

当然，也有一些快速的性能胜利。然而，如果你想永久地提高性能，我们需要一种不同的方法。

> 你不能改进你不能测量的东西

这是一个流行的说法。基本原理是:改变某些东西需要知道你想要改变什么，关键的是，*如何判断你的改变是否有效*。重要的是要知道你的改变是否产生了影响，以及产生了多大的影响，这样你才能知道你是否应该继续执行那个动作，或者尝试其他的。

然而，我认为它仍然缺少一些东西

> 你不能**有意义地**提高你不能**有意义地**衡量的东西

我们想要测量正确的东西。如果我们衡量了错误的东西，这比什么都没有衡量还要糟糕——我们认为我们正在产生影响，但我们并没有解决一个有意义的问题。我们只是在改进随机数。

因此，我们必须小心谨慎地检查我们测量的是什么，并确保我们测量的是“有意义的”

# 什么是有意义的？

[![A picture of plato](img/9e28923afa69cc23470878f4c63e7c70.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--F0nEEzOV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/eqseoelz4h012bgi21wn.png)

这是一个很好的问题。在宇宙的背景下…很难说。但是在性能方面，我们有一个明确的答案:

*有意义=对我们的用户更好*

最终，我们为用户制作网站和应用。我们正在为我们的用户修复性能，希望我们能让现有的体验变得更好，并实现更多(可能是不同的！)用户比以前更多地使用我们所掌握的信息。

# 以用户为中心的绩效定义

我们发现了一个阻碍这一点的问题:性能。出于我们的目的，让我们将“性能”定义为:

> 一个*页面*到一个*用户*加载的时间

我们可以对其进行分解，以了解我们真正想要测量的是什么。

## 什么是页面？

[![A page in a book](img/2c3f487baae2ea1ff8c6b47c3b3f0e56.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lY6YDc2C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ths6c93d1eaet97s60lz.png)

从用户的角度来看,“页面”具有以下内容:

1.  行为
2.  出现
3.  媒体
4.  信息
5.  语义信息

从计算机的角度来看:

1.  java 描述语言
2.  半铸钢ˌ钢性铸铁(Cast Semi-Steel)
3.  资产
4.  数据
5.  超文本标记语言

因此，如果我们想真正理解一个页面，以及它是如何被加载的，从用户的角度来看，我们必须理解这些东西是如何传递给用户的

[https://glitch.com/embed/#!/embed/page-load-without-spa?attributionHidden=true&previewSize=100&path=index.html](https://glitch.com/embed/#!/embed/page-load-without-spa?attributionHidden=true&previewSize=100&path=index.html)

但是，我们说完了吗？传统上，是的。这些迭代中的每一个都被认为是一个页面。然而，今天，用户可能会感觉到完全不同的“页面”

[https://glitch.com/embed/#!/embed/page-load-with-spa?attributionHidden=true&previewSize=100&path=index.html](https://glitch.com/embed/#!/embed/page-load-with-spa?attributionHidden=true&previewSize=100&path=index.html)

注意末尾的添加——从用户的角度来看，通过 SPA 转换加载的页面和站点的第一个页面加载没有太大区别。

所以，最终，一页是:

*   HTML 文档
*   单页应用程序状态
*   应用程序状态
*   无论你的用户认为它是什么

## 什么是“装”？

观看下面的视频，并决定你认为什么时候页面已经加载

[https://www.youtube.com/embed/voHaPuTVjvE](https://www.youtube.com/embed/voHaPuTVjvE)

> 负载不是一个单一的时刻，它是一种没有一个指标可以完全捕捉的体验。在负载体验过程中，有多个时刻会影响用户对它的感知是“快”还是“慢”——[https://w3c.github.io/paint-timing/](https://w3c.github.io/paint-timing/)

当一个页面被加载时，没有一个事件或者一个简单的答案。它甚至可能需要未来的知识！你可能认为一个页面已经加载，直到你看到实际上有更多的未来。这也是一个频谱——我们有不同的“负载”状态，一个度量标准将它压缩成一个数字。

因此，我们需要非常小心地选择度量标准。它应该代表用户认为的“加载”，理想情况下，应该有一种方式来代表用户对“加载”的感知。

## 谁是我们的“用户”？

[![The game of guess who](img/56708e259910934bc07b488512e4141d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xBLh38M9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6he9h2alwy8g8vdbirkp.png)

谁是你的用户可能是显而易见的，但也可能很难确定。有许多变量需要考虑:用户的 cpu 速度是快还是慢，用户的互联网连接速度是快还是慢。他们可能是常客，也可能是第一次。所有这些因素都会影响整体体验，衡量这些因素以更好地了解性能非常重要。

* * *

现在我们知道了从用户的角度来看加载一个页面需要做些什么，我们可以开始考虑使用哪些指标来衡量这种体验