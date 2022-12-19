# 可变状态对于软件来说就像运动部件对于硬件一样

> 原文：<https://dev.to/kristiandupont/mutable-state-is-to-software-as-moving-parts-are-to-hardware-562g>

<figure>[![](img/585a793c6cf00bb507f3a38ac1d9d035.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--baIc0jBN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AaXBsF84bXpk_xOifUVrE8w.jpeg) 

<figcaption>照片由[比尔牛津](https://unsplash.com/@bill_oxford?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上 [Unsplash](https://unsplash.com/s/photos/gears?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

..在这种情况下，有些问题没有它是无法真正解决的，但是一旦你引入了它，你的整个系统就会变得更加脆弱。你得到了整个“我现在不能停止它，它正在进行中”的担忧。

此外，有一个问题，没有它您*可以*解决，但是不可变的解决方案将表现不佳。在这种情况下，最好是从不可变的解决方案开始，在你意识到不能再推迟的那一天进行转换。