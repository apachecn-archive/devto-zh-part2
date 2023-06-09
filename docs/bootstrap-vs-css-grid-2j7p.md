# 自举与 CSS 网格

> 原文：<https://dev.to/sterlingperry/bootstrap-vs-css-grid-2j7p>

如果问任何一个前端开发者，Bootstrap 和 CSS Grid 哪个更好；你得到的答案是“视情况而定”我想给这场辩论一些公正的背景。我几乎同时学习了 CSS Grid 和 Bootstrap 4，并且使用了这两个框架。

## Bootstrap 需要更多 html &更少 css

引导是 div & class 重的。意思是将页眉、旁注、主要内容和页脚渲染到页面上；大约需要 9

and depending how you want these positioned and styled could require 12-15 pre-built classes plus your own classes & Id's. Leaving the only benefit here to be writing less css.

CSS 网格将使用你的语义 html，在这个例子中是 5

but you would have to write extra CSS for each html elelment to position and style our blocks.

## 反应灵敏

Bootstrap 包括五个预定义的类，用于构建响应式布局，这听起来很棒，但会变得复杂，因为断点是通过 html 确定的。这意味着您将添加越来越多的 div 类来为各种屏幕尺寸制作布局。

有了 CSS Grid，就不需要修改 HTML 了。您只需要向样式表添加一些不同的媒体查询，并为 HTML 的每个语义元素定义网格布局。

## 自定义网格布局

使用 Bootstrap，您几乎只能使用 12 列，这取决于您的设计规范，可能适用，也可能不适用。还有一个默认的 10px 左右填充，必须覆盖。每个新项目都要这么做，这很烦人。

使用 CSS 网格你可以完全控制布局。想列多少列就列多少列！

## 页面加载

尽管引导样式表和支持库只有几 kb，但仍然需要下载它们，这会增加页面负载。大约 85%的浏览器支持 CSS 网格，所以没有必要下载额外的样式表或脚本。

## 结论

双方都有优点和缺点，我认为这场战斗是平局。使用 Bootstrap 意味着编写更多的 HTML，而 CSS Grid 意味着编写更多的 CSS。你的偏好。根据设计规格，自举可能不是一个选项。对于不太复杂的布局，Bootstrap 是一个很容易快速启动和运行的工具。记住 Bootstrap 不仅仅是一个网格系统；这是一个完整的前端工具包，包括模态，工具提示，弹出窗口，进度条等。全部在类中预定义。使用 CSS grid 将需要编写 JS/jquery 文件来实现同样的功能。

在一个完美的世界里，我们会有一个包含两者优点的包。我欢迎你对使用 Bootstrap 和 CSS grid 的体验发表评论。你对此有什么看法？