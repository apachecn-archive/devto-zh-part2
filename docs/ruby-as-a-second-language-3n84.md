# Ruby 作为第二语言

> 原文：<https://dev.to/msarit/ruby-as-a-second-language-3n84>

这周在训练营的编码挑战让我的大脑都打结了。我已经因为解决了(大部分)32 个代码战形而感到疲劳，但我被安排与我的导师杰里米讨论题为“图像模糊 2”的挑战。我决定看看我是否能理解这个挑战，它是这样的:

> **使用 Ruby，变换一个由 1 和 0 组成的网格，使“1”上面、下面和旁边的任何 0 都变成“1”(如下图所示，粉红色的“1”曾经是 0)**

[![alt text](../Images/e33df1f617b75fe75955eb7f450c4c86.png "Pixel Transformation")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Hv1B9Uem--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AoB_-cC1FtnWthNXYHtm9Jw.png)

我们目前正在学习面向对象编程，所以我知道最终的算法将由类和方法定义组成。但是我一点也想不出解决的办法——至少那天晚上没有。所以我决定用简单的英语来描述我的代码:

> 1.  Locate the cell with a value of "1".
> 2.  Indicates whether there are rows above, below or on both sides of the cell.
> 3.  Check whether the value of the cell adjacent to the cell in these rows is 1.
> 4.  If yes, keep it the same; Otherwise, change the value of the adjacent cell to 1\. Location of the problem cell: upper array [row] [column] cell: lower array [row -1][ column] cell: left array [row +1][ column] cell: right array [row] [column -1] cell: array [row] [column +1]

我的文章中唯一与代码相关的部分是元素在网格中的位置(*“array[row][column]”*)。我用电子邮件把我的论文发给了杰里米，然后就到此为止了。在第二天的会议中，Jeremy 解释说这个挑战包括两个主要目标，而我的“模糊”概念几乎实现了更难的目标(whaa？？？).那天晚上剩下的时间，我们把我写的东西翻译成实际代码，并创建测试用例，以确保我的算法的健壮性。

这一经历显著增加了我接近算法的信心；在此之前，我相信**如果我没有从一开始就“用 Ruby 思考”，那么我的工作就不是真正合法的**。毕竟都说一种语言的母语者是用那种语言思考(不仅仅是说话)的，对吧？嗯，我当然不是 Ruby 方面的专家(目前还不是！)，但我意识到没关系——甚至有效！—翻译我已经熟悉的语言。