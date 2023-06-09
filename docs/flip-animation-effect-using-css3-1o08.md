# 使用 CSS3 的翻转动画效果

> 原文：<https://dev.to/skptricks/flip-animation-effect-using-css3-1o08>

帖子链接:[用 CSS3 翻转动画效果](https://www.skptricks.com/2018/01/flip-animation-effect-using-css3.html)

这篇文章解释了如何使用 CSS3 创建翻转动画效果。在这里，我们使用 CSS 关键帧属性来执行水平和垂直翻转动画效果到小正方形 div 框。所有的动画效果是由关键帧规则控制的，它提供了非常平滑的动画效果。

[![](img/fb84e538028f9cda2e59cd5a822d56d6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Uo7jleIa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://4.bp.blogspot.com/-rsk0wgF0mR8/Wlr9ZjCQ-ZI/AAAAAAAABLA/GlRDpP4JZyMFBWgin42e5hIjBT-RY7yLACLcBGAs/s1600/flip.gif)

关键帧动画:
这里我们使用 below CSS 样式来执行小正方形 div 框的水平和垂直翻转动画效果。

@ key frames rotate-div {
0% {
transform:perspective(120 px)rotateX(0 deg)rotateY(0 deg)；
-WebKit-transform:perspective(120 px)rotateX(0 deg)rotateY(0 deg)；
} 50% {
变换:透视(120 px)rotateX(-180.1 deg)rotateY(0 deg)；
-WebKit-transform:perspective(120 px)rotateX(-180.1 deg)rotateY(0 deg)；
} 100% {
变换:透视(120 px)rotateX(-180 deg)rotateY(-179.9 deg)；
-WebKit-transform:perspective(120 px)rotateX(-180 deg)rotateY(-179.9 deg)；
背景色:# ec407a
}
}

下载代码:[https://github . com/skp tricks/PHP-Tutorials/tree/master/Flip % 20 animation % 20 effect % 20 using % 20 css3](https://github.com/skptricks/php-Tutorials/tree/master/Flip%20Animation%20effect%20using%20CSS3)