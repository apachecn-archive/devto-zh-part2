# Swift 中色彩和谐的乐趣

> 原文：<https://dev.to/samjarman/fun-with-colour-harmonies-in-swift-3cpb>

大家好！

最近，我开始以一个新的角色回到 Swift，我想我应该用一些小的副业项目来练习一些东西。

与此同时，我了解了[色彩调和](http://www.tigercolor.com/color-lab/color-theory/color-harmonies.htm)。理论是，给定一种颜色，就有与之完全匹配的颜色。我总是很难创造出搭配的颜色，所以当我发现两种/三种/四种颜色“看起来不错”之间存在数学关系时，我非常兴奋。

正如在上面的链接中所描述的，我希望能够产生一些颜色的和谐:互补的，相似的，三色的，正方形的，矩形的和分割互补的。

首先，我认为这是一个使用类别的绝佳机会，呃，我是说 UIColor 上的*扩展*。从这里开始，每个补色都可以是一个实例属性。酷毙了。

从那里，我需要做数学。

从图表的外观来看，颜色在 HSV(色调、饱和度、值、Alpha)颜色空间中表示，分为 12 组。所以，我必须首先将颜色从我们在 UIKit 中使用的 RGB 转换成 HSV。为了方便起见，UIKit 中实际上内置了一个转换 API。他们称之为 HSBA(色调，饱和度，*亮度*，阿尔法)。

一旦转换，我知道色调是围绕圆形色轮的角度。

[![HBq9I.png](../Images/a132c2a7001f8a019acf8492323058ef.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XvuC0uLM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static1.squarespace.com/static/58bfb8c059cc68cc36914ea9/t/5b0e418a562fa77adff8c2d5/1527660949300/HBq9I.png%3Fformat%3D1000w)

因此，从这里开始，我只需要添加所需的角度(180 度表示互补)，然后将值标准化，以找到新颜色的色调。其余组件将保持不变。再次重建 RGB 值，然后我们有一个颜色。

对于角度，我使用了 12 的倍数，因为我当时看的图表就是用的这个，然后我用轮子周围的 1/12 来编码每个补码。很简单。

最后的扩展在我的 GitHub 上[这里](https://github.com/samjarman/swift-color-harmonies)。还有一个小的示例应用程序，您可以在下面看到它的运行。

> 是啊，这太棒了。今天我学到了很多关于色彩调和的知识。
> 
> 谢谢你的主意， [@nzmilky](https://twitter.com/nzmilky?ref_src=twsrc%5Etfw) 。
> 
> GitHub 链接和博客文章...pic.twitter.com/RW1EIPZ2vY :D
> 
> —山姆·贾曼👨🏼‍💻💙([@萨姆贾曼](https://dev.to/samjarman))[2018 年 3 月 12 日](https://twitter.com/samjarman/status/973046900680048645?ref_src=twsrc%5Etfw)

我最后的问题，也许你知道。

*   有什么方法能让我做得更快吗？
*   代码能更有效吗？
*   这里可以更有效地使用存储属性和计算属性吗？

现在就这些了！请通过 GitHub 在您的项目中自由使用。几乎可以肯定还有其他库可以解决这个问题，它们可能是更好的解决方案。我不介意，我学到了一些东西。

感谢阅读，

萨姆（男子名）

_PS:我把颜色拼得很滑稽，是的，在新西兰我们就是这么拼的。抱歉。_