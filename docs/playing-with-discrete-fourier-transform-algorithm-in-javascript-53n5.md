# 在 JavaScript 中玩转离散傅里叶变换算法

> 原文：<https://dev.to/trekhleb/playing-with-discrete-fourier-transform-algorithm-in-javascript-53n5>

## TL；速度三角形定位法(dead reckoning)

您可以在 [JavaScript 算法库](https://github.com/trekhleb/javascript-algorithms/tree/master/src/algorithms/math/fourier-transform)中找到离散傅立叶变换算法。

## 离散傅立叶变换

尽管[离散傅立叶变换](https://en.wikipedia.org/wiki/Discrete_Fourier_transform)或 **DFT** 可能不是你日常工作中用到的东西，但它可能仍然是一个非常有趣的算法。不是因为它很复杂，而是因为它有意思。

*该算法允许您将在时间上扩展的输入信号分割成一定长度、振幅和相位的多个频率，以便所有这些频率一起形成原始信号*。所以它实际上将*时间*的域转换成*频率*的域，并向后转换。

[![](img/31ea1daf2994c1dd3ec184c2253e7ffd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AH7lgFXb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v1p6fhprekoheceqafw1.png)

这听起来可能很复杂，所以让我们从另一个角度考虑一下。

### 冰沙例子

想象你有一杯奶昔。DFT 将允许你把奶昔分成不同的成分！想象一下，你提供一瓶思慕雪作为 DFT 函数的输入，它把它分成三小瓶纯胡萝卜、苹果和橙汁！这就是 DFT 所做的——它将整个输入分解成它的成分。

[![](img/57dd4181e5731505667830df51c0210b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oHweNZto--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dc0pjetcru31jbspub7h.png)

### 描绘例子

或者想象一下，你想油漆栅栏，你已经混合了几种油漆，使它开始同质的颜色。DFT 函数然后将能够把你的混合颜料分成几个纯色，它们将一起形成初始的颜色！听起来是不是很神奇，是吗？

[![](img/c6e1d461b43d9f871ace4e8e865a0e50.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VNmmVNnX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o953c923pngcxtqga433.jpg)

### 算法

所有算法的美和复杂性都隐藏在下面的公式中:

[![](img/02c67a386b360c673f1a4eff51d6a7da.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BXjc0p9S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1loz2byyp5i8397r473d.png)

您可能会在 [JavaScript 算法库](https://github.com/trekhleb/javascript-algorithms/tree/master/src/algorithms/math/fourier-transform)中找到这个公式的直接而简单的实现。这只是一个简单而并不真正有效的(*【o(n^2】)*)转换的实现。但这些函数的目的只是触及傅立叶变换这样一个复杂、深刻而神奇的主题的表面。

关于这个话题有一篇[真的很好的文章](https://betterexplained.com/articles/an-interactive-guide-to-the-fourier-transform/)。如果你有兴趣了解更多，我建议你读一读，因为这里有很多视觉和*互动*傅立叶变换的例子和解释。

我希望你觉得傅立叶这个东西很有趣。算法玩得开心！