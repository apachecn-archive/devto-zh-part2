# CSS 百分比单位，邪恶部分

> 原文：<https://dev.to/ismail9k/css-percentage-unit-the-evil-parts-1g29>

CSS 有几十个[长度](https://developer.mozilla.org/en-US/docs/Web/CSS/length)单位，但是**百分比**单位是我的最爱之一。然而，它有时会非常邪恶和麻烦。

这应该是最容易理解的 CSS 概念之一， **percentage** CSS 数据类型代表一个百分比值， **percentage** 是一个表示为 100 的分数的比率，因此它必须进行一些计算以获得最终的计算(绝对)值。

到目前为止，这似乎很简单，最棘手的部分是每个百分比值都必须相对于某个元素的绝对值。它通常相对于元素的父节点，但有时也会相对于元素本身(我将在后面解释)。此外，我们必须确切地知道它将引用哪个属性值(高度或字体大小)。

## TL；速度三角形定位法(dead reckoning)

当你将它与一些 CSS 属性一起使用时，它将相对于**父元素的宽度**。这些属性包括:左、右、边距、填充(甚至上下边距/填充都将相对于父级的宽度)。

有时它会相对于**父元素的高度**。就像预期的那样，当它用于顶部或底部属性时。

当你将它与**转换**属性一起使用时，它将相对于**元素本身**，或者是它的宽度或者是它的高度。

* * *

我们将通过几个步骤来创建这个转盘(滑块),以熟悉 CSS 百分比单位。

[![carousal (final)](../Images/b5ad048ad6744559583cb1dbbb208820.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uCoZb10v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1100/1%2A_SlOkwsYSxK3D0utZsFuxg.png)

请注意，我们不会详细解释如何根据样式创建这个旋转木马，或者如何使它具有交互性。我们将只关注如何在对齐控制器中处理百分比并设置幻灯片的宽度和高度。

详细创建旋转木马需要大量的数学知识和钻研 JavaScript，这需要一篇独立的文章；所以为了简洁起见，我将不深究这两者，而将从这种基本样式的 [**教程笔**](https://codepen.io/Abdelrahman3D/pen/pVgPZM) 开始。

[![carousal (start)](../Images/ac8d30d5efb86ed5b2c07f867a2c3727.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yqPumIno--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1100/1%2AAVoX3GQzqRLBB3G6_4Z9Qw.png)

## 一般指南

我在命名类时使用 [BEM](http://getbem.com/) 约定；因此，每个元素都有相同的[特异性](https://developer.mozilla.org/en-US/docs/Web/CSS/Specificity)权重，可以很容易地重写其属性。

我们使用 border-box [box-model](https://css-tricks.com/box-sizing/) 是因为它更直观，因为
元素的框的可见宽度=宽度+填充+边框，而
元素的框的可见高度=高度+填充+边框。

我使用粗斜体字体的类名，如 ***。滑块*** ，指向我当前正在处理的元素。

你可以看到钢笔上有垂直和水平的准线，完全居中对齐。要隐藏参考线，只需移除*。滑块—从**到*引导*类。滑块*** 容器。

[![guides code](../Images/cfcb39d89b46c6567698df712b056ae1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UN_q3Mgi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/880/1%2ACRh0veQd55o_LIyZQgI_ew.png)

在 CSS 部分的笔的底部，你会发现每个教程步骤代码都有注释。取消注释，随着本教程的进行，一步一步地查看结果。

* * *

## 第一步:设置幻灯片的高度

我们给了一个 ***。slider__slide*** 宽度为*的 25%* ，这将使元素的内容区域正好等于其父元素的四分之一宽度( ***)。slider__track*** )，但前提是其父级具有显式宽度。

[![step 1.1](../Images/8db4b55a43930304ccdb7fc5f2411ba0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DUKofH_V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1100/1%2ACEDOQSS_P50gDu0QoP19TA.png)

我们给了一个 ***。滑块 _ _ 滑块*** 高度为 100%，这将使元素的高度正好等于其父元素的高度( ***)。滑块 _ _ 轨道*** )。

[![step 1.2](../Images/bdb8f83ab95020dd822e53996720f99a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NkQQArux--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1100/1%2AAkS3J_6P1Pk99aAvKelc9A.png)

您会注意到幻灯片脱离了导轨，我相信任何级别的前端开发人员都面临过与我刚才描述的问题类似的问题。当你认为使用 CSS percentage 非常简单(也应该如此)时，你就面临着这种让你抓狂的奇怪行为。

[![slider 1.2](../Images/7322e48e60610d94cf774b7f034050a7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lBeFOdW8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1250/1%2AfzZCPPrRx_FpCx4NklAL3g.png)

如果你对这个问题调查的多了，你会发现 ***。slider__slide*** 的高度等于它的父级，正如我们所料，但是因为它的顶部和底部有 20px 的边距，所以它离开了网格(我故意放了那个边距😈😈).

当元素的宽度/高度设置为 100%时，我们必须非常小心，因为它有一个边距，这将会把它移动到父元素之外。

> 注意:如果您使用默认的浏览器框模型(内容框)，添加填充或边框也会将元素移出父元素。有很多方法可以解决这个问题，我相信你已经想到了一些，其中一个是去掉那个小的邪恶的边距，或者我们可以简单地从高度中减去上下边距。

[![step 1.3](../Images/177e1bf8bceb444f68a1abbc3d618a10.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Qd0E158_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2Am--5ixB7dVYvNlnYhOP3AQ.png)

🎉🎉你做到了！

[![slider 1.3](../Images/5b64fec74d1481372a02ccae70aa03fa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NtlFsffh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1100/1%2AAlsvMZ8MxrQSI-apenlvlA.png)

## 第二步:对齐滑块指示器

我们将从对齐 ***开始。滑块 __nav*** 到滑块的中下方。我们会给它一个绝对的位置，相对于 ***。滑块*** 向左*移动滑块宽度的 50%* ，查看输出。

[![step 2.1](../Images/7389e5e51b01ba3dca0e2490a7587d93.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x_YjalG7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1100/1%2AaXr_2Rbt7kZuzz_WuKsJkg.png)
[![slider 2.1](../Images/e5e04611e240a1eacaffb983a7eba527.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--t7fGow29--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1250/1%2A0s4Hdok_6bDAA4kw0sXd1A.png)

正如我们所见，我们已经对齐了 ***的左侧。滑块 _ _ nav*T3 到**T5 的中间。滑块** ，但我们其实是想对齐 ***的中心。将 __nav* 滑块**移动到**的中心*。滑块*** 。**

不幸的是，CSS 中没有 center 属性，所以我们必须设计一个技巧来强制使用 ***。*滑块 _ _ nav**移回中心。

我们需要告诉 ***。滑块 __nav*** 移动到其宽度的右半部分(*距离宽度 50%* )，我们可以为此使用负值的边距。

如果我们在**的*上加上余量-左: *-50%* 。滑块 __nav*** 本身，我们会发现它会回到它的起始位置，因为百分比值会相对于。滑块宽度不是 ***。滑块 __nav* 宽度。**

这里的诀窍是选择第一个**T1。slider__nav** child，添加 *margin-left: -50%* 到它，然后百分比将相对于 ***。滑块 __nav* 本身。**

[![step 2.2](../Images/c0d39381183a5816a61bc39e6576c751.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6LEDSvPu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1100/1%2ALHeTmtat9YuYA4r9TzOf9A.png)

👌👌现在它完全居中

[![slider 2.2](../Images/9df92c49c4c83d2243be183652bfb501.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9CTbJJMo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1250/1%2AdZoq_86S5oy-f0-OYPLviA.png)

不过不要着急，我们还有两个箭头要对齐…

## 第三步:对齐滑块控制器下一步按钮

我们将**对齐*。滑块 _ _ 下一个*** 箭头到**中间的右侧*。滑块*** 。

让我们试试刚刚学过的技巧，但现在我们将使用它来垂直居中对齐元素。

[![step 3.1](../Images/13daa828480a068040c6c3ed6c909f84.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WpdQkCqA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1100/1%2AqBoieyi9SoIfCnz7Ea0skQ.png)
[![slider 3.1](../Images/f5dc18d90fa1c1edc465f48051f2c88d.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--5bTedvm0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1250/1%2AjotAhhluo4Tm_r5XFzUBsw.png)

哎呀！这次没成功，你能猜到原因吗？

看起来这种技术在水平对齐上非常有效，但是在垂直对齐上就不那么有效了。这就是为什么 CSS 有时会如此邪恶。

要知道为什么，我们将检查每个百分比值，并确定它与哪个元素和属性相关。

*顶*属性是相对于**的*。滑块*** 的高度正是我们想要的，而 margin-top 是相对于 ***的。slider__next*** 的元素，这也是我们想要的，但是它是相对于它的宽度，而不是它的高度。

如果 ***，这将完美地工作。滑块 _ _ 下一个*** 的高度与宽度相同，但是我们如何解决这个问题呢？

[![spiderman meme](../Images/eb762c8cffac979a3903ddf48624044e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2pspvIu1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1100/1%2A9Ic7UZ9TWLG6DiZggQlhWQ.jpeg)T3】

<center>*source: [https://memegenerator.net](https://memegenerator.net)*</center>

### 我向你介绍终极解决方案，说欢迎来到超级英雄变身。

*transform* 属性非常强大，因为它可以相对于元素本身的宽度或高度，所以不需要选择第一个子元素的技巧。它可以完美地工作在垂直和水平方向，不用说，它的性能也更好。

事实上，有时父容器中没有子元素，所以第一个子元素根本不起作用。

这就是为什么我称之为终极解决方案。因为它在任何情况下都会起作用。

用这段代码替换前面的代码。

[![step 3.2](../Images/6f8ce3bf7020cbc52c74c462ee03c341.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jJyeANtr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1100/1%2AbjUyUSFh9qOdTXQ0d4uWqw.png)
[![slider 3.2a](../Images/7ffc413b7cd9b9547992947bd1126858.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--8ZXq7CYO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1100/1%2AyjKG0WRcCn1R6HhMOGmrGw.png)

这里还有一个小问题。当悬停在下一个箭头上时，它将被放错位置。

[![slider 3.2b](../Images/3b793c4e3ce570bd6efe40dfe5196620.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--B3EvWYVX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1100/1%2AD2rkkMDv62baaq9wJ2nsPw.png)

所以我们要给 ***加上 translateX。滑块 __next:悬停*** 状态过。

## 第四步:对齐滑块控制器上一个按钮

最后一步是对齐 ***。滑块 __prev*** ，它将完全作为 ***。滑块 __next*** 除了它会在左边。

[![step 4.1](../Images/4ae7f9a95597568f2cdcc0443a22e6e4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EC0Zfohz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1100/1%2AoqST1TlvAEOM6th0dDZNYQ.png)

🎉🎉恭喜你！你已经成为一个真正的 CSS 蜘蛛了。

[![slide 4.1](../Images/b5ad048ad6744559583cb1dbbb208820.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uCoZb10v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1100/1%2A_SlOkwsYSxK3D0utZsFuxg.png)

## 结论

我希望这个例子能帮助你更好地理解 CSS 百分比单位是如何工作的，它何时相对于元素本身，以及它将相对于哪个属性。

我还暗示了一些非常重要的主题，如 CSS 特异性、盒模式和 BEM 命名约定。

如果你确切地知道如何操纵百分比单位，它会非常强大。

感谢阅读，编码快乐！

## 参考文献

[https://www.w3.org/Style/Examples/007/center.en.html](https://www.w3.org/Style/Examples/007/center.en.html)
T3】https://www.impressivewebs.com/vertical-percentages-cssT5[https://developer.mozilla.org/en-US/docs/Web/CSS/length](https://developer.mozilla.org/en-US/docs/Web/CSS/length)