# 如何让你的 CSS 跨浏览器保持一致

> 原文：<https://dev.to/neshaz/how-to-make-your-css-consistent-across-browsers--2hff>

也许这篇文章应该是第一篇，因为它是关于如何开始你的 CSS。再说一遍，你应该能够明白你到底在做什么，所以我不会太晚。

代码重置提供了一组简短的规则，将所有元素的样式重置为一致的基线。这是必要的，因为不同浏览器的样式不一致。当你打开时，你不希望像表格元素这样的预样式干扰你。

## 练习

当我开始学习 CSS 的时候，有人对我说，在开始的时候，你应该写:

```
 *{
    margin:0;
    padding:0;
 } 
```

这是基本的 CSS 重置。

不同浏览器中风格各异的一些最常见的元素是超链接(< a >)、图像(< img >)、标题(< h1 >到< h6 >)以及各种元素的边距。众所周知，浏览器会在几乎所有内容中添加一定量的填充。

不同浏览器之间结帐按钮的不同样式示例:

[![CSS Reset differences between browsers](../Images/c7cc4cffd8bc9989352bd8b87adb0fdd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5rl-Wp9q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kolosek.com/content/images/2018/03/indeterminate-checkboxes-browser-comparison.jpg)

不同元素(标题、链接、span、代码、blockquote)的不同样式示例:

[![CSS Reset differences between browsers](../Images/bda06cc12ed13e58f5449c3ca9021cc8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vviDgMhV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kolosek.com/content/images/2018/03/Mask-Group.png)

每个浏览器都有默认的 HTML 格式，您可以在检查模式下的**“用户代理样式表”**下看到它。如果为特定元素添加了样式，则它的默认浏览器格式将被覆盖。

[![user-agent-stylesheet](../Images/52dd7624653b053f98ee81b0cb4c57f8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bpSyB_mR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kolosek.com/content/images/2018/04/user-agent-stylesheet.jpg)

## 重置脚本

我最喜欢的 CSS 重置脚本是来自 http://html5doctor.com 的:

```
 html, body, div, span, object, iframe, h1, h2, h3, h4, h5, h6, p, blockquote,  pre, abbr, address, cite, code, del, dfn, em, img, ins, kbd, q, samp, small, strong, sub, sup, var, b, i, dl, dt, dd, ol, ul, li, fieldset, form, label, legend, table, caption, tbody, tfoot, thead, tr, th, td, article, aside, canvas, details, figcaption, figure, footer, header, hgroup, menu, nav, section, summary, time, mark, audio, video {
     margin:0;
     padding:0;
     border:0;
     outline:0;
     font-size:100%;
     vertical-align:baseline;
     background:transparent;
 }

 body {
     line-height:1;
 }

 article,aside,details,figcaption,figure, footer,header,hgroup,menu,nav,section{ 
     display:block;
 }

 nav ul {
    list-style:none;
 }

 blockquote, q {
    quotes:none;
 }

 blockquote:before, blockquote:after, q:before, q:after {
    content:'';
    content:none;
 }

 a {
    margin:0;
    padding:0;
    font-size:100%;
    vertical-align:baseline;
    background:transparent;
 }

 /* change colors to suit your needs */
 ins {
    background-color:#ff9;
    color:#000;
    text-decoration:none;
 }

 /* change colors to suit your needs */
 mark {
    background-color:#ff9;
    color:#000; 
    font-style:italic;
    font-weight:bold;
 }

 del {
    text-decoration: line-through;
 }

 abbr[title], dfn[title] {
    border-bottom:1px dotted;
    cursor:help;
 }

 table {
    border-collapse:collapse;
    border-spacing:0;
 }

 /* change border color to suit your needs */
 hr {
    display:block;
    height:1px;
    border:0;   
    border-top:1px solid #cccccc;
    margin:1em 0;
    padding:0;
 }

 input, select {
    vertical-align:middle;
 } 
```

一些已定义的样式是非常标准的(用于

您可以使用更多重置脚本列表:

*   meyerweb ，
*   [carrer web](http://www.vcarrer.com/2010/05/css-mini-reset.html) ,
*   [yui 库](https://yuilibrary.com/yui/docs/cssreset/)，
*   [西蒙哈特 html 5-重置](https://github.com/murtaugh/HTML5-Reset/blob/master/assets/css/reset.css)。

有些开发人员使用 **CSS normalize** 而不是 CSS reset(有些人将其称为“新的重置”)。reset 用于清除所有内容，使您的项目成为“白板”, normalize 保持良好的实践，同时消除跨浏览器风格的不一致。我将来肯定会写关于 CSS 规范化的文章。直到那时检查[这个帖子](https://cssreset.com/scripts/normalize-css/)。

## 结论

没有必要重置你的 CSS，但是要知道浏览器的预样式和 CSS 重置的存在。

本文原载于 [Kolosek 博客](https://kolosek.com/content/images/2018/04/user-agent-stylesheet.jpg)。