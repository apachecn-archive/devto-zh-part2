# 标准差的几何

> 原文：<https://dev.to/nestedsoftware/the-geometry-of-standard-deviation--3m3o>

我最近需要查找标准偏差的公式。我觉得这个公式看起来有点神秘，有平方和平方根。以下是数据样本标准差的基本公式:

[![standard deviation formula](img/62d89336832997e83f88647a24e94791.png "standard deviation formula")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--z-4dOelu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ggzs44981kodjlgn5lml.png)

我们从平均值中减去每个测量值(通常称为“平均值”)，然后取平方。我们把所有这些方块加起来，然后除以 n-1。最后我们求平方根。这是怎么回事？

一段时间以来第一次看这个公式，我突然想到分子看起来很像[勾股定理](https://en.wikipedia.org/wiki/Pythagorean_theorem)中的距离。下图显示了两个点`p1`和`p2`之间的距离`d`:

[![pythagorean theorem diagram](img/5a96ea97e2b504ecc82d375afd2ba7c9.png "pythagorean theorem diagram")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kaBO4uvG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/33kt694ge9m8zhnsp5uc.png)

根据勾股定理，这个距离是:

[![pythagorean theorem formula](img/274d77ea84d07c391f637429a11bfec4.png "pythagorean theorem formula")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OgfPZ7XE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t6metdzumfk1k6x3lpml.png)

差不多吧？我们有一个平方根。在平方根里面，我们总结了一些值。总和中的每一项都是差值的平方。这和我们标准差的分子中的基本操作是一样的。

让我们用一个非常基本的具体案例来探讨这个问题:假设我们在一个数据样本中有两个值，比如说，x₁ = 2.5，x₂ = 1.5。平均值为(2.5 + 1.5)/2 = 2。

现在，让我们计算两点之间的距离:第一个点由两个采样测量值组成。第一次测量在 x 轴上，第二次测量在 y 轴上。我们称这个点为 V，因为它代表采样值。V = (x₁，x₂) = (2.5，1.5)。

> x₁的测量值在 x 轴上，但是 x₂的测量值在 y 轴上，所以我现在脱离了 *x* 指 x 轴的惯例。只是一个变量。我们也可以称这些值为 v <sub>1</sub> ，v <sub>2</sub> 。

第二个点，我们称 m 为平均值，由两个轴上的平均值 x̄组成。M = (x̄，x̄) = (2，2)。

[![diagram distance from V to M](img/492207e3aeb62be5530743c03ba963d9.png "diagram distance from V to M")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rz2lQg9a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s23tosdmaq1gapjjqhut.png)

我们可以看到 V 和 M 之间的距离是:

[![distance from V to M](img/08c981ef5fd88b987a9127d9bbbc4930.png "distance from V to M")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xb035Hqx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/41i67fce1yguy171exdo.png)

现在让我们看看，我们是否能把两点之间的距离和标准差的公式联系起来。

样本标准差的平方称为[方差](https://en.wikipedia.org/wiki/Variance)，记为`s²`:

[![variance formula](img/91d4bbe865427c8e28d653cd586a0fff.png "variance formula")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AE-or-4B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hpws5pfkw7qfxo4nk56j.png)

让我们将方差乘以`n-1`:

[![variance times n-1](img/2e8adb9a5b73d44769c122862804bb3e.png "variance times n-1")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vFRU5jAZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/obmnxnsvuaf4saa3k8pk.png)

现在我们来求平方根:

[![sqrt of variance times n-1](img/91a71115dc09db445b3593b5d4a54ea2.png "sqrt of variance times n-1")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--N1YRYmFd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9mamcy7yh49184en84zj.png)

让我们将刚刚导出的这个值定义为`d`:

[![define d](img/96e761c1a32b0df8099da9c6ee7a1730.png "define d")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yCKk7llk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mtw1adnp4k4utjy2owjf.png)

我们现在可以看到，我们之前计算的距离 0.71 实际上就是这个值`d`！

只需一些小代数，我们就可以证明标准偏差与这个距离密切相关:

[![sqrt of variance times n-1 = d](img/982f196690eb1b86eb5b7f17ff12fa63.png "sqrt of variance times n-1 = d")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--04zRRrGY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lrowkyw1ydoysitnxkjw.png)

[![relationship between standard deviation and distance](img/84d4d83693dfe783055b731443b90026.png "relationship between standard deviation and distance")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tsmWZjP_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5t00f4b05jzkaqjv3ay6.png)

我们可以看到，我们可以通过将`d`除以`√(n-1)`得到标准差。在这个特例中，`√(n-1)`仅仅是 1。因此我们可以说标准差是 0.71！

这对于样本中有 2 个测量值的情况很有效，但是如果我们有(通常情况下)超过 2 个数据点，我们该怎么办呢？如果我们有 3 个测量值，那么我们的点 v 将有 3 个维度，V = (x₁，x₂，x₃).我们的点 m 也有 3 维，M = (x̄，x̄，x̄).

最酷的是，我们可以任意扩展样本中每个测量的维数。x₁，x₂，x₃，...x <sub>n</sub> )，而 M = (x̄，x̄，x̄...x̄).

> m 将永远是对角线上的一个点，因为它在每条轴上都有相同的值，x̄。

勾股定理适用于任何维数。事实上，`n`维度中两点`a`和`b`之间的距离公式应该看起来很熟悉:

[![pythagorean theorem in n dimensions](img/92578be3ca2871b27a47a0136305a9f2.png "pythagorean theorem in n dimensions")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hoVZ4Hmq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wvztxulijo4udaecvxrj.png)

让我们与之前定义的`d`进行比较:

[![d as distance](img/4da28aae43bbd5443d3d50e0a8834d87.png "d as distance")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HiDsp40H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2mk56u9y06fj6d4k95bl.png)

对于大于 3 个数据点(即 3 个维度)的样本，使用这种距离概念可能对可视化标准偏差没有帮助，但我认为它有助于从概念上更好地理解标准偏差所代表的内容。

利用标准差，我们试图测量一些数据的“分布”或“可变性”。如果样本中的所有值都相同，那么点 V 和 M 之间的距离当然将为 0。这意味着标准差也是 0。数据变量越大，V 点离 m 点越远。

当我们除以`√(n-1)`时，我们正在归一化这个距离，实质上是平均样本中每个值对从 V 到 m 的距离的贡献。

> 这种 n 维几何的想法可能有点吓人。我认为在这里，每个数据都需要在它自己的轴上，这具有直观的意义:我们希望每个值都独立地对可变性*做出贡献*。在确定方差或标准差时，我们不希望+4 的值抵消-4 的值。

如果这对你来说是新的，我希望这种几何视角有助于建立对标准差真正是什么的直觉，而不仅仅是一个需要记忆的公式。感谢您的阅读！

> 如果我们在已经包含所有可能数据的情况下计算标准差，那么我们实际上除以`n`来计算方差。例如，当计算一次考试的所有分数的统计数据时，或者当计算一次医学试验的统计数据时，这可能是有效的。
> 
> 然而，在大多数情况下，我们选取的样本只是全部数据的一小部分。在这种情况下，我们通常用类似`n-1`的东西来除。我们为什么要这样做，这本身就有点神秘。更多信息，请查看[贝塞尔修正](https://en.wikipedia.org/wiki/Bessel%27s_correction)和[自由度](https://en.wikipedia.org/wiki/Degrees_of_freedom_(statistics))。

相关:

*   [计算流数据的移动平均值](https://dev.to/nestedsoftware/calculating-a-moving-average-on-streaming-data-5a7k)
*   [计算流数据的标准偏差](https://dev.to/nestedsoftware/calculating-standard-deviation-on-streaming-data-253l)
*   [流数据的指数移动平均](https://dev.to/nestedsoftware/exponential-moving-average-on-streaming-data-4hhl)