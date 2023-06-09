# 超维洞穴探险笔记

> 原文：<https://dev.to/awfulaxolotl/notes-from-hyper-dimensional-spelunking-3i5k>

[![intro spelunker](img/1ec1d7c1e48a18f31464e3361d65645c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6e2bvGQJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://axolotl.industries/static/intro-spelunker-953e11c2b0d0d31224ab847d550d7e80-7efac.png)

写软件很难。视觉化它的行为使它变得更容易。

我们当前的工具箱有单元测试、REPLs、静态分析、演练调试器、热代码重载、可视化编程、数据流图等等。

但是我们可以做得更多。多得多。

正如我在[上一篇](https://axolotl.industries/babbles/introducing-tastes/)中介绍的，我正在研究[口味](https://github.com/awfulaxolotl/tastes)。这是一个用于软件可视化的智能采样的类型脚本库。这个想法是为了显示一段代码在最少的设置下所能展现的状态范围。

思考这个问题引发了一些有趣的问题！

*   如果我们不能显示每个州，我们选择哪一个？
*   我们如何将我们选择的形象化？

欢迎来到超空间洞穴探险的世界。带上你的三次元头盔和十五次元绳。

我们要进去了！

# 请抓住绳子，看一看

> 你在一个山洞里！这难道不令人兴奋吗？
> 
> 我很高兴你和我一起来。我告诉过你离开城市一段时间会对你有好处的！我是说，我知道我需要它。我向上帝发誓，如果我不得不隔着那堵隔间墙再听一遍德里克的 Spotify 播放列表……不管怎样，让我们-
> 
> 那是什么在闪烁？那是你的手电筒吗？
> 
> 那是我们唯一的灯！
> 
> 我们现在如何绘制洞穴地图？！

孩子们，带备用电池去你们假想的洞穴探险。

但是，你没有——哎呀。你是做什么的？你用你剩下的有限的光看哪里？

这是一个类似于可视化程序行为的问题。你的手电筒成了你的程序，方向成了你的输入，洞穴地形成了你的输出。

你*可以*到处找，但是可能要花很长时间。当时间至关重要时，你会怎么做？

你*可以*随意看，但那样你可能会错过重要的特征。看看一些随机的数学函数:

[![random sampling](img/8f1070bd69d42362f547e95feef9e98f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fDkq3Skn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://axolotl.industries/static/random-sampling-880d00754943571dd6528be2aa6e34bd-126cd.png)

好吧，如果你的洞穴碰巧是一个信号，那么你可以问一些信息理论家。他们是洞穴探险专家，但前提是洞穴是由波浪组成的。

🥁🛎

他们建议使用[奈奎斯特-香农采样定理](https://en.wikipedia.org/wiki/Nyquist%E2%80%93Shannon_sampling_theorem)。基本思想是以两倍于最小可能波长的速率采样。否则你永远不会完全确定你在看什么。

[![shannon nyquist sampling](img/4f1d00e45c338a1c0521bd0a92ef36e5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--o9NXejuJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://axolotl.industries/static/shannon-nyquist-sampling-17462fe4b13ca5ba6ee7123662f1f83c-126cd.png)

酷毙了。但是我们的洞穴不是由波浪构成的。它不是一维的。它是超多维的。

我的天啊。

# 维度的诅咒

更高的维度使事情变得复杂。

你认为一个普通的，古老的，三维的洞穴很大吗？第四维度的洞穴要大得多。第五维度的洞穴要大得多，是方形的。第六维度的洞穴要大得多，是立方体。如此等等。

用手电筒对空间进行采样可能看起来是徒劳的。如果你不小心，你会在映射之前就耗尽能量，哪怕是一点点。

这是一个很大的问题，事实证明洞穴探险者们甚至给它起了一个名字。这是维度的[诅咒](https://en.wikipedia.org/wiki/Curse_of_dimensionality)。

该死的超级洞穴。冷静一下，好吗？

看起来我们必须用一些经典的智能来替换我们的低电量电池。

# 用笛卡尔积展开

一个好的开始是在你环顾四周的地方均匀地散开。

假设您想要检查每个维度上的三个位置。在三维空间中，这可能看起来像检查你周围的一个假想立方体的每个角落和中间的边。

这个位置列表是笛卡尔乘积。所以对于任意单位的超立方体，这个乘积看起来像:

```
 places = {0,0.5,1} × {0,0.5,1} × … × {0,0.5,1}
∣places∣​ = 3^dimensions​ 
```

Enter fullscreen mode Exit fullscreen mode

很好，但是`dimensions`给你敲响了警钟吗？

*诅咒！*

# 挑选收藏夹

更高的维度使得位置或样本的数量变得非常庞大。让我们试着减少我们的抽样。

一个好的第一步是在维度上优先考虑位置，然后避免最不重要的位置。例如，让我们检查一个不是洞穴的样本空间:

```
fonts   = hues × weights × styles
hues    = {h ∣ 0 ≤ h < 360, h ∈ R}
weights = {normal, bold}
styles​  = {serif, sans-serif, monospace}​ 
```

Enter fullscreen mode Exit fullscreen mode

换句话说，我们有一组不同颜色、粗细和样式的字体。你可以把`(23.453, bold, monospace)`想象成`fonts`中的一个例子。

假设你想看看不同的字体在你制作的海报上是什么样子。这里有几个问题要问:

*   我们要取样多少种颜色？理论上是无限的。
*   我们真的关心每一种色调、重量和风格的组合吗？

如果我们不太关心某些维度，我们可以设法配对样本集。你可以想象对一堆色彩进行采样，并结合随机的重量和风格。没有笛卡尔积！或者反其道而行之:看看带有随机色调的重量和款式的产品。

问题是因为我们的目标是人类的理解，所以最好的采样方法纯粹是主观的。任何实现都必须做出最好的猜测，但是要给用户可选的配置。这是目前在品味方面采取的方法。

# 不要碰填充空间的蛇

[![snake curves](img/bc4f836271a6c41a1159dd5f00c0365a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uAo-MQ8z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://axolotl.industries/static/snake-curves-bc2cf7dd44c7b2720771e9272965b43c-126cd.png)

笛卡尔积很容易理解，但是有一些缺点。你不能选择任意数量的样本，并让它们平均分布。你必须选择最喜欢的。

这些都不是空间填充曲线的问题。如果你不熟悉的话，它们是将一个维度投射到任何超维空间的函数。如果你把它们画出来，它们看起来就像你所见过的最熟练的蛇游戏。

它们之所以整洁，是因为它们在保持局部性的同时非常易于使用。想要五个样品？只需选择五个标量点，并将它们投影到曲线上。想让他们平均分配？嗯，它们会在投射后保持展开。

[![hilbert mapping](img/159284052c215082846e1b3b5f72e2e4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UDQgWvzL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://axolotl.industries/static/hilbert-mapping-3bb0684fc965be1097e7057aaccea906-6631e.png)

我更喜欢[希尔伯特曲线](https://en.wikipedia.org/wiki/Hilbert_curve)而不是 [Z 顺序](https://en.wikipedia.org/wiki/Z-order_curve)或[阿砣曲线](https://en.wikipedia.org/wiki/Peano_curve)。后两种方法不能像希尔伯特曲线那样保持局部性，但是计算起来要快得多。

# 拿上你的护目镜，你这个傻瓜！

所以我们越来越擅长用手电筒照这个超级洞穴。我想这很酷。

你知道什么更酷吗？

理解你正在看的东西。让我们看看超空间吧！

但是你可能已经猜到了，理解三维以上的东西有点困难。不过，这是值得的。能够看到超空间意味着我们实际上可以直观地探索任何我们认为合适的样本空间。

漂亮。

事实证明，我们确实有一些数学眼镜来做这件事。它们将高维空间投射到我们原始的粘糊糊的大脑的一维、二维和三维空间。

太好了，数学！

那么这些护目镜到底是什么？嗯，又是充满空间的曲线！我们可以反过来用它们将一个超点投射到第一维上——一条线。然后，我们可以随心所欲地将投射回第二维或第三维空间。漂亮。

这给了我们相邻样本彼此相似的酷属性。随着你向外走，样本可以预见地进化。示例用例可以是示例的可缩放网格。或者是或否的提示，像在一维线上做二分搜索法一样优化你的浏览。

你可以看到一些简洁的空间填充曲线的可视化应用，用于二进制数据可视化和互联网地图。

[![binvisio](img/b98619a49247f17c8cbb7eb6771eac75.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KMdMXTCt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://axolotl.industries/static/binvisio-a67c5902b0e9f5a98c1def0a2b0de9e2-c2ae9.jpg)

但是空间填充曲线并不是唯一的游戏。我们新的超级护目镜也可以通过[降维](https://en.wikipedia.org/wiki/Dimensionality_reduction)过程制成。

一个例子是 t-SNE，它在机器学习领域很受欢迎。它主要用于通过检查深度神经网络如何对数据集中的项目进行分组来窥视深度神经网络的学习。当然，SNE 霸王龙并不是唯一一个像 UMAP 一样正在开发其他方法的人。

[![t sne cnn embed](img/c324da182bb66d878daba9e2ea218f29.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XIF83MJq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://axolotl.industries/static/t-sne-cnn-embed-af2c91f36afecfd02d3f9867af1a438c-c2ae9.jpg)

现在想象这被用来可视化样本空间的聚类。我承认，这有点夸张。我不太确定这些方法的性能如何，或者它们是否支持数据集的实时添加。

# 无尽的洞穴

如果这引起了你的兴趣，请随意品尝或联系我们！当我为自己的作品扩展品味的能力时，我会考虑这些东西。

洞穴探险快乐！

PS。记住你的手电筒电池和护目镜。