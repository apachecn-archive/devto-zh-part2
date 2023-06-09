# 技术专家:重置你的风格

> 原文：<https://dev.to/reduncan/tech-speak-reset-your-styles-4dm3>

您正在开始一个新项目，并希望从空白画布开始。所以你创建你的文件结构，构建 html，使用你喜欢的编程语言创建你的网站的功能，最后设计你的网络应用。当您设计 web 应用程序时，您注意到您在设计应用程序时遇到了问题。你不得不在网站上不需要样式的部分添加样式，有时你不得不创造性地设计页面样式，因为某些属性没有得到应有的响应。这是为什么呢？

每个网络浏览器都有自己的一套预定义样式。你可以只使用 html 属性标签来创建一个 html 框架，当你在浏览器中查看它时，它会被“样式化”。属性标签如 *body、h1 - h6、p、ol、table 和 ul* 都有预定义的显示、边距、填充、边框和字体大小样式。你可以在这里查看从浏览器[继承的风格概述。虽然您可以在 css 文件中覆盖这些样式，但是如果您不想在浏览器放置填充的地方进行填充，该怎么办呢？您必须进入 css 文件并更改该值。这可能很耗时，而且您可能需要多次这样做，这会使您的代码变得混乱，可读性更差。那么我们如何解决这个问题呢？](https://www.w3schools.com/cssref/css_default_values.asp)

我们在 web 应用程序中添加了一个辅助 css 文件。这个 css 文件重置了从浏览器继承的所有样式，这个文件通常被命名为 *reset.css* 。你可能想知道我们怎么知道要重置什么。如果你想创建你自己的 reset.css 文件，你必须仔细检查并找出从浏览器继承的所有样式，但这可能很费时间，因为有许多浏览器，它们都有稍微不同的默认样式。最简单快捷的方法就是在你最喜欢的搜索引擎中进行简单搜索...

这里有一个有趣的谷歌搜索教程。

既然有这么多结果，现在你可能想知道用哪一个。这取决于你。尝试一下，找到最适合你的。他们都做着几乎相同的事情。我更喜欢使用 meyerweb 重置文件。它是第一个被开发者广泛使用的结果。

```
/* http://meyerweb.com/eric/tools/css/reset/ 
v2.0 | 20110126
License: none (public domain)
*/

html, body, div, span, applet, object, iframe,
h1, h2, h3, h4, h5, h6, p, blockquote, pre,
a, abbr, acronym, address, big, cite, code,
del, dfn, em, img, ins, kbd, q, s, samp,
small, strike, strong, sub, sup, tt, var,
b, u, i, center,
dl, dt, dd, ol, ul, li,
fieldset, form, label, legend,
table, caption, tbody, tfoot, thead, tr, th, td,
article, aside, canvas, details, embed, 
figure, figcaption, footer, header, hgroup, 
menu, nav, output, ruby, section, summary,
time, mark, audio, video {
margin: 0;
padding: 0;
border: 0;
font-size: 100%;
font: inherit;
vertical-align: baseline;
}
/* HTML5 display-role reset for older browsers */
article, aside, details, figcaption, figure, 
footer, header, hgroup, menu, nav, section {
display: block;
}
body {
line-height: 1;
}
ol, ul {
list-style: none;
}
blockquote, q {
quotes: none;
}
blockquote:before, blockquote:after,
q:before, q:after {
content: '';
content: none;
}
table {
border-collapse: collapse;
border-spacing: 0;
} 
```

正如你所看到的，有许多样式需要重新设置。meyerweb 重置文件还考虑了不再更新的旧浏览器(咳咳，咳咳，ie 浏览器)。该文件将从浏览器继承的所有内容设置为 0 或无。

现在你已经添加了你的 *reset.css* 文件，你可以随心所欲地设计你的应用程序，而不用担心继承的样式。

造型愉快，下次见:)