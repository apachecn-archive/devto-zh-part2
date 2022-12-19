# 【讨论】使用机器学习处理音频文件？

> 原文：<https://dev.to/jochemstoel/discuss-using-machine-learning-to-process-audio-files-13o7>

我使用  和各种软件(VST 插件)，它们需要一个 [DAW/host](https://en.wikipedia.org/wiki/Digital_audio_workstation) 来支持它们手动去除音频文件(语音记录)中的背景噪音。我说的背景噪音指的是你几乎总能从价格合理的麦克风和电话中听到的微妙的背景噪音。通过在文件中选择一个“静音”范围(没有人说话，只有噪音)作为所谓的音频配置文件，我可以改善结果。我重复同样的过程，直到背景噪音水平完全为零/消失，有时是以牺牲声音为代价。结果的质量主要取决于文件的质量和噪点的数量。

*本文中的噪音并不意味着来自交通等环境的随机干扰，它总是指静态的。*

结果通常很好，当你正确应用这些工具时，它们能做的事情是惊人的。然而，正确也意味着手动。处理音频的参数对于每个单独的文件都有一点不同，所以为了让*批量处理音频*，我需要以某种方式自动处理。我一直在寻找命令行实用程序或某种类型的库来做到这一点。有一些工具，但没有一个能产生效果。我甚至尝试用预定义的参数在一个无头的 VST 主机上加载 vst 插件，但这是一堆垃圾。

* * *

[![PhonicMind](../Images/01e756f0985474d3087c2d1fb180a167.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--w2ZtMEN8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/evtxq0qg976yn9o4qfug.jpg)

PhonicMind 将自己标榜为“在线人工智能声乐提取器”，这是一种使用“人工智能”从音频文件中提取声乐轨迹以改善结果的在线工具。这是一个商业产品(你必须付费才能使用)，在 Tensorflow 中有一个基于深度神经网络的[https://github.com/andabi/music-source-separation](https://dev.toGitHub)知识库，可以将歌声从音乐中分离出来。我没有深入调查，也不关心这两者是否有关联。

* * *

[![Lightbulb](../Images/c3c8f664a3be3ea1873215f1643457f5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Zi6_BvvG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tqb0bp37eis6n9rgr110.jpg)

所以，接下来是一个灯泡。背景噪声去除的机器学习？我怀疑(很可能是假设)在这样的大容量音频中检测和消除噪音比 PhonicMind 试图做的要简单得多。或许简单到可以自己做。

我该如何着手建立解决方案的原型？我对机器学习有一个大致的了解，知道 TensorFlow，但不能说我曾经用它做过什么。如果可能的话，我更喜欢需要最少学习的解决方案，所以如果你碰巧知道一个我没有找到的命令行工具或框架，那可能更好。

谢了。