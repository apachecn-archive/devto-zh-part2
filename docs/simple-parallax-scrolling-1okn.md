# 简单视差滚动

> 原文：<https://dev.to/annlin/simple-parallax-scrolling-1okn>

我在谷歌上搜索了“简单视差滚动”,找到了一个关于 [w3](https://www.w3schools.com/howto/howto_css_parallax.asp) 的教程。 [w3 视差滚动演示](https://www.w3schools.com/howto/tryhow_css_parallax_demo.htm)。

我喜欢用户滚动时背景图像不动的滚动效果。创建一个视差滚动效果(实际上可能不叫视差滚动，但是我在 w3 教程中使用了这个名字。).

### 然而，

它不再适用于手机。>:(

### 问题在于，

css 属性`background-attachment: fixed`由于巨大的重画成本而无法在移动设备上运行。

> ![Paul Irish profile image](img/3100e2db4d8e7867f64775b6a51a18f0.png)保罗爱尔兰[@保罗 _ 爱尔兰](https://dev.to/paul_irish)![twitter logo](img/03bb33b910d947ffb5d9c56b0a509ab2.png)[@ PPK](https://twitter.com/ppk)[@ bever loo](https://twitter.com/beverloo)固定背景有*巨大*重画成本和抽取滚动性能，我相信这就是它被禁用的原因。2013 年 2 月 27 日下午 17:30[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=306818591196602368)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=306818591196602368)10[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=306818591196602368)17

### 不过没事，

我们仍然可以使用`position: fixed`来获得同样想要的酷炫效果。所以我从这个实验中发现的是，尽管有不同的`z-index`，每个使用`position: fixed`的图像都将被一起渲染并相互叠加。

### 一个简单的微笑效果，

我有两张透明的 gif，有不同的面部表情。一旦用户滚动，gif 就会从微笑变成 meh 表情。

[![meh face](img/7f5d5c44a782b72a67c196750e6bda93.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KvvhmEd1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m3tphrqzy5q7eao12rze.png)
[![happy face](img/aeea0f890b36258a846a2c8de531038e.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--NSrntJqG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/agbu4k0lf6vm1findi7d.png)

这个想法是在两个 gif 之间添加一个白色层来隐藏基础层。

[![sketch on layers](img/9cfcd9d79753e4b0880dd2fdcac95e58.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aJiCj7eF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/st7rp67sebfeo4idtw65.jpg)

[https://codepen.io/toomuchome/embed/zJrEeZ?height=600&default-tab=result&embed-version=2](https://codepen.io/toomuchome/embed/zJrEeZ?height=600&default-tab=result&embed-version=2)

[在浏览器上玩演示](https://linxea.github.io/parallax-scrolling/smile/)

[源代码](https://github.com/linxea/parallax-scrolling)

### 但是等等，

最初我使用两个不同的 gif，然而由于不同的下载时间，gif 可能不会同步。简单的修复就是把两张 gif 合二为一，合二为一！我们可以使用`object-position`来裁剪每个 div 的图像。类似 css 精灵的东西。因此，我们保存了一个图像调用，并确保图像总是同步的。

### 好了，

再见。