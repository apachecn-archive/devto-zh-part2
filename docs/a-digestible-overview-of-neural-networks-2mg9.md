# 神经网络的简明概述

> 原文：<https://dev.to/techsmosh/a-digestible-overview-of-neural-networks-2mg9>

[![](../Images/cc31af11adb72a1a1ee18e1ed0930d55.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hlG-yq5Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9e8062x8w6c9ncguaaac.jpg)

*由 Raji Ayinla 撰写，作为对 techsmosh 的贡献*

当我说，“2250 年。”你突然想到了什么？这可能是一个充满人工智能机器的世界，重现科幻小说呈现给我们的每一个可能的场景。23 世纪的学生可能会在计算机科学课程中研究人工智能的历史，嘲笑我们新生的机器学习算法，就像我们嘲笑 ENIAC 一样。但是，正如 ENIAC 和许多其他图灵完整机器为超级计算机奠定了基础一样，神经网络正在为能够模仿毕加索艺术天赋的机器人奠定基础。

## 什么是神经网络？

[![](../Images/75349e1f8874e1eb4a9305cf6e987ecc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Vi9eYyX6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oyj4xvvwpvf4d15y2vx0.png)

通常的做法是将人工神经网络(ANN)概念化为生物神经元。从技术上讲，正如阿达·洛芙莱斯所说，人工神经网络是“神经系统的演算”一个非常非常简化的神经系统。以至于一些神经科学家可能因为这个类比而失眠。

可视化神经网络的一个好方法是不去想生物神经元。相反，想象一个洗车场。是的，洗车。当你把脏污的车开到当地的洗车店时，你希望把车投入使用。现在，想象洗涤过程对你是隐藏的。洗车场输出你的车，一下子就干净了。

所以，我们可以推测，你输入你的汽车(x)，它被转发到一个隐藏层(h)，然后它被转发到一个输出层(y)。这是一个严重的过度简化，但是这个类比描述的是正向传播。当然，神经网络中发生的不仅仅是数据的转发。

神经网络由一个输入层、一个或多个隐藏层以及一个输出层组成。请注意，层数从第一个隐藏层开始计算。隐藏层越多，网络就越复杂。四个或更多的隐藏层构成了一个深层网络。上图中描绘的神经网络被认为是一个 6X4X3X1 网络。

让我们来看看所有层共享的共同组件:突触和神经元。

突触获取前一层存储的输入值，并将其乘以一个权重。该权重的值可以在-1 和 1 之间。你会发现，深度学习就是将权重校准到特定值，以产生准确的输出。想象一个淋浴，你可以调节冷热旋钮来获得理想的温度。之后，突触将结果向前传播给神经元。

另一方面，神经元的责任更大。他们必须将所有的(权重* X 值)相加，并包括偏差。

这是准系统方程式的样子:

Y=b+∑ wixi

#### 什么是偏差？

好吧，想象一个规模。天平的左侧平台上有一个偏差。这个左边的平台标有 0。在另一个平台上，你有求和。与右侧平台相关联的数字是 1。偏差越大，神经元越难输出 1。偏差越小，神经元越容易输出 1。

#### 为什么需要偏置？

实现偏置允许您偏移值为 0 的输入。由于权重乘以突触的输入，任何乘以 0 的值都等于 0，所以你会遇到最终输出与你预期的结果大相径庭的情况。在 stackoverflow 论坛上，你可以找到关于偏差必要性的有力论据。

## 激活功能:

所以我们有了解决方案。不过，我们还没完成。神经元需要激发或不激发的潜能。这是通过激活功能实现的。有几种激活功能，它们都有自己独特的风格。深入探讨每个函数超出了本文的范围，但是我们将看看阶跃函数和 sigmoid 函数，以展示两者之间的差异。

#### 步进功能:

[![](../Images/49902100eb9659b94b43f1be45dcd2c7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--G52gYln6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v90f0ao8005ytc3cfmea.png)

阶跃函数就像一个开/关开关。它的二进制。对于任何熟悉编程的人来说，你知道阶跃函数等同于布尔条件。以这个伪代码为例:

阈值= 0.5。

如果 Y >阈值，输出 1。

否则，如果 Y < 1 阈值，输出 0。

就神经网络而言，如果网络可以清楚地识别一个类别，阶跃函数就可以很好地工作。不幸的是，图像识别几乎总是涉及一个以上的类别。例如，当试图用 MNIST 数据库中的手写数字数据集训练神经网络时，使用范围从 0 到 9 的数字类。如果所有的神经元都因为所有的总和达到了阈值而被激发呢？结果将是灾难性的。

#### 乙状结肠功能:

[![](../Images/0ab88df393e8b0795241091b16bbafc3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DWrs1-aE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f3o02890b0o052rn1j6h.png)

解决方案是使用非线性激活函数，如 sigmoid 函数。当您将求和输出传递给 sigmoid 函数时，输出的范围将在 0 和 1 之间。

既然我们已经决定了激活函数。我们可以进入机器学习中最受关注的部分:训练阶段。

## 训练神经网络:

当你蹒跚学步时，你可能犯了很多错误，导致你的父母责备你，让你知道你有多错。再骂几次之后，你就知道自己的错误了。类似地，我们通过使用成本函数确定预测中的误差量来训练神经网络。均方误差是用于此目的的常见成本函数。

[![](../Images/84ff335b539647fbc728d0dfd9e2202b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--r8vQt_jY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vxlkeoen1f8g5e1niij9.gif)

将期望值与预测值进行比较。一旦我们获得了误差值，我们需要找出如何最小化成本函数。理论上，我们需要做的只是调整权重，以改变成本。我们的成本越低，我们的结果就越准确。还记得我们的淋浴类比吗？好吧，把突触上所有的重量想象成独立的旋钮。我们可以通过计算每一种可能性来手动调整这些旋钮，但现在想象一下，不是两个旋钮，而是数百万个旋钮需要调整。维数灾难阻止任何人试图通过暴力方法猜测值。

厌倦了类比？好吧，这是我们解决方案的一个比喻。这是一个盲人越过了他的营地。离营地的距离是他的错误。他知道他的营地位于海拔最低的地方。如果他感觉自己在走下坡路，他决定通过移动来最小化他的错误。如果他真的在走下坡路，他会继续增加他的动力，相信他已经接近底部了。

这就是梯度下降的基本原理。正在发生的是一个叫做微分的数学过程。我们找到均方差的导数。这决定了变化的速度。如果是负的，那么成本函数就是在走下坡路。如果是正的，就是上坡。然后我们相应地调整权重。

[![](../Images/4765636aba5df99f5eeffd8488f53827.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5KJ_9yRd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2smexc834p84xnyvthiv.png)

这个权重调整过程称为反向传播。你可以把反向传播归结为链式法则。在微积分中，链式法则用于将外部函数的导数乘以内部函数的导数。有了这个规则，不管一个网络有多少个隐藏层，你总是能够从第 n 层到第一层。让我们把一个 3X2X1 层重新想象成嵌套函数 f(f(XW^(1)) W^(2)).

你可以把反向传播等同于拆开一个嵌套的套娃。我们一次弹出一个函数，将内部函数乘以外部函数，直到到达根层。另一方面，向前传播就像组装一个套娃。当您组合正向传播和反向传播时，您会创建一个循环，该循环会导致权重的增量调整，从而减少误差值。经过多次迭代后，权重将稳定下来，最终结果是优化的输出。

## 结论

在开始，我提到神经网络可能导致艺术倾向的机器人。是的，这可能是天方夜谭。如果他们网络的 ImageNet 分类准确率能够达到 80%以上，一些数据科学家将会欣喜若狂。然而，这仍然不应该阻止最狂热的阿西莫夫迷，因为数据分类是当今人工智能的前沿。你知道你在科技文章中不断看到的所有数据科学术语，以及深度学习似乎 10 次出现 9 次吗？嗯，最近数据分类的突破是最近所有喧嚣的原因。

这些分类中涉及的主要类型的神经网络被称为传统神经网络，或 CNN，或 ConvNets -无论你的船漂浮。根据手头的问题，神经网络有多种风格。它们继续打破壁垒，并将在未来几年成为技术领域的重要组成部分。

## 资源

[https://ml 4a . github . io/ml 4a/conv nets/](https://ml4a.github.io/ml4a/convnets/)

[https://arxiv.org/pdf/1508.06576.pdf](https://arxiv.org/pdf/1508.06576.pdf)

[https://br hrer . github . io/how _ convolatile _ neural _ networks _ work . html](https://brohrer.github.io/how_convolutional_neural_networks_work.html)

[https://en.wikipedia.org/wiki/CIFAR-10](https://en.wikipedia.org/wiki/CIFAR-10)

[https://medium . com/data things/neural-networks-and-back propagation-in-a-simple-way-explained-f540a 3611 f5e](https://medium.com/datathings/neural-networks-and-backpropagation-explained-in-a-simple-way-f540a3611f5e)