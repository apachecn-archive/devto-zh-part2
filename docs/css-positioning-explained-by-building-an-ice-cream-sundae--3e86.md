# 通过构建一个冰淇淋圣代来解释 CSS 定位

> 原文：<https://dev.to/kbk0125/css-positioning-explained-by-building-an-ice-cream-sundae--3e86>

## 如果你之前做过冰淇淋圣代，那么你就能理解 CSS 的定位。

你的 div 像罗马蜡烛一样在屏幕上缩放。

它们潜入容器深处，然后像鲸鱼一样浮出水面。

他们把其他元素推开，然后完全像一个不耐烦的商人一样离开容器。

不知何故，每当你改变 CSS 中令人讨厌的一行代码时，这就会以一种新的令人兴奋的方式发生:位置属性。

每个学过 CSS 的人都去过那里。CSS 定位似乎是无意义的，直到几个小时的试错最终给你一个模糊的认识。

这篇文章将一劳永逸地结束这种困惑。位置属性似乎与任何现实世界的概念都没有明显的联系…直到你想到不起眼的冰淇淋圣代。

我们将介绍以下职位属性:

*   绝对的
*   静态(默认)
*   固定的；不变的
*   亲戚

[![](img/0653b5789f779658c415e3bdd80ef6cd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7OJiwKSI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AXDzlibMEpQuEbSi685SOzg.png)

为了清楚起见，冰淇淋圣代将由 4 个主要部分组成:

*   玻璃
*   冰淇淋勺
*   生奶油
*   樱桃

### 将冰淇淋圣代作为 HTML

如果你必须用 HTML 解释冰淇淋圣代的结构，它可能看起来像这样。