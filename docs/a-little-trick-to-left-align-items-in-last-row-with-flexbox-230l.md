# 用 Flexbox 左对齐最后一行项目的小技巧

> 原文：<https://dev.to/stel/a-little-trick-to-left-align-items-in-last-row-with-flexbox-230l>

在我的第一篇文章中，我想和你分享几个月前我学到的一个小技巧。

对于像我一样喜欢使用 Flexbox 的人来说，您可能在过去遇到过最后一排的项目表现不太好的问题。也就是说，他们没有左对齐:

[![first_picture](../Images/4a9c95e8fbb090b7b6bd114df4369fad.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nh_STAu2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tdmtp7ujku6nkkbk7221.png)

非常令人不满意不是吗？

嗯，有一个非常简单的方法可以用一个简单的 html 属性实现完美的左对齐: **aria-hidden**

该属性将指示该元素及其子元素将不会被用户或读者看到或感知到。要了解更多信息，您可以登录 W3 网站。

这里有一些简单的 html 来创建那些漂亮的蓝色方块:
[![second_picture](../Images/43c07af3fdd5cbf4fcea1409efe8384e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7SgjHCGr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5rvl05llgdidc291ulpb.png)

正如你所看到的，我有六个空的 div 来创建正方形，下面三个`<i>`标签的 aria-hidden 属性设置为“true”。

这些`<i>`标签对应于我可能需要完成最后一行的正方形元素。更清楚地说，我的行由四个元素组成。如果您不知道在您的行中将有多少个元素，但是您可以知道在最后一行中将至少有一个元素。因此，你将需要和你的行中的位置一样多的`<i>`标签，减去一个。

现在，我们需要一些 css :
[![third_picture](../Images/400a142e9e4123babf24351a7b431b08.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--00rHMbWb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g6g75tui2kx08n5syxu0.png)

在这里，我给了我的行的容器不同的 Flexbox 属性，包括`justify-content: center`。但是这个技巧也适用于其他值，如`space-between`或`space-around`。

看看我的物品。我给了他们一个`width and an height of 250px`和`margins of 20px`。

这里最重要的是，你必须给你的`<i>`标签赋予和你的项目相同的宽度、高度和边距。

最后，TADAAA！！！完美的左对齐；)
[![fourth_picture](../Images/c82af5d6d38d408b576e3771d3bd68a9.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--wZb_Q1eq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wesyl6ew5uqi4e9sy5pd.png)