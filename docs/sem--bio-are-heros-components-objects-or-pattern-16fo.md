# SEM & BIO:Heros 是组件、对象还是模式？

> 原文：<https://dev.to/ghost/sem--bio-are-heros-components-objects-or-pattern-16fo>

在过去的几天里，我真的很喜欢钻研 CSS 方法。对 [@allison_seboldt](https://dev.to/allison_seboldt) ，[提问的](https://dev.to/allison_seboldt/how-do-you-organize-your-css-4p97/comments)[精彩回应](https://dev.to/bhaibel/comment/6ggk)你是如何组织你的 CSS 的？。

我正在读 Ryan Yu 的《结合 SEM 和 BIO 提高 CSS 的[，它是一堆方法(BEM、ITCSS、OOCSS)的奇妙综合。](https://css-tricks.com/combining-the-powers-of-sem-and-bio-for-improving-css/)

英雄单位是这些天内容页面的主要部分。以下是 Ryan 对 ITCSS 中的层的看法:

*   "对象(命名空间:o-)用于设计模式，如布局，其中项目被安排而不是装饰."
*   “组件(命名空间:c-)是构成网站一部分的小功能。想想按钮、手风琴、滑块、模态对话框等等。”
*   另一方面，例如，标题将是一个模式，因为它不可缩放(标题不能用于内容或侧栏区域)，并且还包含其他组件，如按钮、折叠、菜单、徽标、搜索表单等

这似乎表明英雄是一种模式。但是我认为用页面上的什么地方来定义“可伸缩性”很奇怪。这就是我们所说的“可伸缩 CSS”的含义吗？

Ember 和 React 让我认为组件是 UI 的一部分，这在语义上是一个英雄，但我认为标记意义上的“组件”不同于 ITCSS 意义上的“组件”。

你怎么想呢?