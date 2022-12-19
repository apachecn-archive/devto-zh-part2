# 使用 div 和 span 的块级和内联

> 原文：<https://dev.to/wmahathre/block-level--inline-using-div-and-span-ohc>

当学习一门新的语言时，你总是会在一些事情上停滞不前，当我刚开始学习 HTML 时，是 div 和 span 标签让我感到困难。我真的搞不清这两者之间的区别，尤其是对于块级和内联。但是在我的代码中练习和使用它之后，我将告诉你使用 html 的两者之间的主要区别。

首先，div 是一个块级元素。这种类型的元素从新行开始，并占据整个宽度。Span 是一个行内元素，这意味着它停留在同一行上，并占用必要的宽度。

[![Alt text of image](../Images/fc6313938972ad451047b3bb32fdfa2d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--opZP85p---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6coleqfcc3zn2695ofge.png)

请注意，在本例中，突出显示的文本是 div 元素，它开始于一个占据整个宽度的新行，现在包含 3 个块。

[![Alt text of image](../Images/99d7aabb259dc1525407f4a7355a7459.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eZmB6YZO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/54moo23vsed6698s2mpj.png)

将它更改为 span 可以使文本连续，并且在宽度或块上没有任何变化。

所以请记住，span 是一个内联元素，用于占据由开始和结束标记限定的空间，而 div 是一个块级元素，总是从新行开始，占据页面的整个宽度。