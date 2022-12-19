# 强化学习应用

> 原文：<https://dev.to/galyna_chekan/reinforcement-learning-applications-107f>

在浏览《T2》关于 AlphaGo 的故事时，你可能已经读过[强化学习](https://perfectial.com/blog/reinforcement-learning-applications/)——这种算法已经教会自己玩围棋，并击败了一名人类专家——并且可能会发现它很有趣。

然而，由于这个主题本质上很复杂，而且从商业角度来看似乎也没什么前途，所以你可能认为深入探讨它没什么用。

事实证明，RL 缺乏实际益处是一种误解；实际上，公司现在可以通过多种方式使用这项技术。

在本帖中，我们将列出可能的强化学习应用，并在没有技术术语的情况下解释 RL 通常是如何工作的。

## 监督学习、非监督学习和强化学习

[![reinforcement-learning-applications](../Images/45c40b407b0f832f16ecc7e4d9ffe00e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bLXrKdiU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://perfectial.com/wp-content/uploads/2018/06/img1-7.jpg)

因此，在传统的**监督学习**、[中，根据我们最近的帖子](https://dev.to/galyna_chekan/machine-learning-in-finance-and-how-to-apply-them-16hh)，我们有输入/输出(x/y)对(例如标记的数据)，我们用它们来训练机器。知道了每个输入的结果，我们让算法确定一个映射 Xs- > Ys 的函数，并且每当模型出现预测/分类错误时，我们就不断地校正该模型(通过反向传播和抖动该函数)。)我们继续这种训练，直到算法产生的结果令人满意。

在传统的**无监督学习**中，我们有没有标签的数据，我们将数据集引入我们的算法，希望它能揭示其中的一些隐藏结构。

**强化学习**解决[一种不同的问题](https://en.wikipedia.org/wiki/Reinforcement_learning%20target=_blank%20rel=)。在 RL 中，有一个代理与特定环境交互，从而改变其状态，并因其输入而获得奖励(或惩罚)。它的目标是通过尝试所有的行动并比较结果，找到产生最多奖励点的行动模式。

RL 的关键特征之一是代理的行为可能不会影响环境的即时状态，但会影响后续的状态。所以，有时候，机器直到很晚才知道某个动作是否有效。

帖子[强化学习应用](https://perfectial.com/blog/reinforcement-learning-applications/)最早出现在[软件开发公司完美](https://perfectial.com)上。