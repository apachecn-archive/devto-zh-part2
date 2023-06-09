# PyConPL 2018 报告

> 原文：<https://dev.to/lukaszkuczynski/pyconpl-2018-report-2loo>

是时候召开 Python 会议了。参加了许多 JVM，我从来没有机会给 Python 它的位置。现在，每当问题摆在我面前时——Python 是默认的选择语言，我喜欢它提供的自由和可能性，尤其是在数据科学领域。老实说，这已经成为我两年以来的“爱”，所以参加 [PyCon](https://pl.pycon.org/2018/) 是必须的。是什么让我保持专注？我将与你分享:

## 速度是最重要的

我参加的一个讲座是关于`pypy`的。当你想用那个库写快速 Python 代码[时，Cuni 先生把它作为默认选择。如果你想这样做，用`pypy`而不是`python`来执行你的代码。演示期间展示的用例是视频处理，的确，由 *pypy* 运行的程序版本要快得多。但是第二天，我和视频处理的人讨论这个话题的时候。他们说为演示选择的比较不太公平。Python 只是一种脚本语言，而不是实时视频处理工具。](https://pypy.org/)

## 云无处不在

我们看到了云服务的重要性。提到了两个主要角色。谁赢了？在我看来阶段是 AWS 的。很难想象我们现在如何处理大量的数据。首先，Suryansh Tibarewala 展示了 Alexa，这是 AWS 上的一项服务。不幸的是我们没有看到任何现场演示，但至少理论介绍。请注意，语音识别会带来很多困难，其中之一就是处理上下文中的连续问题。第二天，michasmereczyński 透露了微软作为云提供商的一些信息。他让我们相信，Azure 不仅仅是 Windows。Azure Batch 是一个很好的 Azure 服务，因为你可以并行运行你的脚本。如果您偶尔要执行一些批处理作业，就没有必要购买和维护大型服务器。此外，您不必每次都手动设置环境，因为 *ACR* 允许您存储和使用 Docker 映像。pawekopka 周五给出的 Ansible task 也明确表示，有了专业的配置管理工具，我可以与任何使用相同(或相似)脚本的云提供商合作。

## 你健康吗？

在我看来，其中的两个演讲特别酷。而且重要！他们证明了 Python 如何有助于疾病预防。周五，在亚采克·米耶塔斯基的演讲中，我了解了 Python 在生物信息学中的应用。我们学习了是什么让 DNA、RNA 和蛋白质不同，以及我如何拟合非常长的基因组序列，并用你可以从这里[拿来的`Biopython`来分析它们](https://biopython.org/)。不幸的是，这个演讲不是用英语进行的，所以它阻止了一些外国听众听这个演讲，尽管他们很感兴趣。在周六的*核磁共振演讲*中，我们看到了 Python 如何帮助科学家分析核磁共振图像。用于该目的的的[库是`nipy`，但是你可以很好地利用其他非 Python 应用，如 *FreeSurfer* 。Mikoł aj Buchwald 的主要使用案例是了解当我们看到特定图像时，我们大脑的哪些部分会受到刺激。促使我积极思考的是我从那次演讲中摘录的以下句子:](http://nipy.org/)

> 一个行动和观想它几乎是一样的，所以保持积极的心态

## 注意安全！

Christine Bejerasco 女士展示了通向安全标准的道路。她开始是 Perl 开发人员，然后转到 Python，因为她可以继续使用 regex，嗯…我们都知道:Python 要好得多。在我看来，她的网络安全演讲是最好的演讲之一，因为它质量很高，没有大量的细节，展示了大量的实际例子。当互联网还不那么流行的时候，邪恶的代码被链接到现有的 *exe* 文件中，它可以通过物理驱动器感染机器。现在，生活在如此流行的“在线生活”时代，我们必须小心我们浏览的内容和打开的内容。我了解到，即使是访问危险的网站，而不是有意下载，也会导致灾难。我的浏览器可以被**探测**漏洞，然后**向**注入一些恶意代码。病毒甚至可以存活一段时间去激活，然后在最关键的时刻(Stuxnet)运行(唤醒)。如何玩病毒，又不伤害自己的机器？他们正在使用沙盒来做到这一点。

## 如何教机器

机器学习不是一项显而易见的任务。这不仅仅是现在的流行语。在会议期间，*numpy*T4】scipyT6】sk learn 这些词被频繁提及。我很高兴参加了周六的研讨会，向我展示了神经网络如何帮助识别图片。Prakhar Srivastava 介绍了*计算机视觉*的挑战和解决方案。2015 年，卷积神经网络已经超越了人类识别简单图片的能力，这对我来说是一个全新的发现。听起来有点吓人。我了解到，在使用一些高级库之前，你应该更好地理解它是如何工作的。然后你会更好地准备 1)珍惜`import sklearn`让你做的事情，2)用好这些工具。也可以在线玩 nice 神经网络演示，[这里](http://scs.ryerson.ca/~aharley/vis/conv/)。