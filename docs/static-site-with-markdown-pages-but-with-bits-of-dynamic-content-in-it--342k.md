# 有降价页面的静态网站，但是有一些动态内容在里面？

> 原文：<https://dev.to/danroc/static-site-with-markdown-pages-but-with-bits-of-dynamic-content-in-it--342k>

你好！

我早就想把我的 Wordpress 博客转换成一个**静态网站**，所有的文章都作为降价文件，使用 Hugo，或者 Jekyll，或者 Gridsome 等等。

然而，我在我的每篇文章中都有一个自定义的**短码**，它从数据库中抓取数据并将其转换为文章中的元素。

本:
[![shortcode](img/c76ad0d640bb1d734c4db653e3447309.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fOt9pfPv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r9nr56ep7e5w1890o06g.png)

就变成了这个:
[![rendered posts](img/57b20c0bb3de3846519d28adfa4d5159.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lo0b1mX_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sgkkpe6gvcwkghfbl6jy.png)

我试着做了一些关于如何在一个静态站点中实现这种情况的研究，但是我不知道从哪里开始。

*   它还能作为一个静态站点在渲染时调用动态内容吗？
*   还是只在构建时调用动态内容(这也完全可以)？

所以我想我应该问你们:有没有人有类似的经历或建议？

谢谢！
丹尼尔