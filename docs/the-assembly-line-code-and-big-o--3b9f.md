# 流水线，代码，还有大 o。

> 原文：<https://dev.to/yelluw/the-assembly-line-code-and-big-o--3b9f>

我目前正在工作中重构一个非常混乱的代码库。这让我开始思考为什么会这么乱的原因。昨晚，在看《奇异事物》的一集时，我意识到为什么它如此糟糕。

# 好的代码就像一条装配线

这个代码库不好的原因很简单:没有一步一步清晰的流程来处理流经它的数据的状态。许多事情同时发生。每行代码的密度都很高。全局状态也被使用和滥用。理解给定时间点的数据状态是困难的。这需要侦探工作。很多。

如果我们将旧代码与重构代码进行比较，有一点非常突出，那就是数据显然是从一个点流向另一个点的。你可以清楚地理解输入是如何转化为输出的。就像装配线是如何工作的一样。

维基百科对装配线的定义是:

`... a manufacturing process in which parts (usually interchangeable parts) are added as the semi-finished assembly moves from workstation to workstation where the parts are added in sequence until the final assembly is produced...`

使用这个定义，我们可以把好的代码和装配线联系起来。它将过程分解成步骤(工作站),并按顺序添加或删除东西，直到产生输出。我不知道你是怎么想的，但是 DRY、依赖注入和许多其他的设计模式立刻浮现在我的脑海中。

# 一条装配线旨在为常数 O(1)

关于这个代码库，另一件让我恼火的事情是，每一步充其量都是 O(n)。访问哈希表中的项会使用循环。弦是通过分解其他弦构建的。完全没有效率。根本就没想过用最少的工作量把事情做完。好代码就是懒。这一个就像一个快餐工人一样过度劳累(我就是一个)。

相反，重构后的代码总是以 O(1)为目标。它并不总是能实现这一点，但是已经对所使用的数据结构进行了大量的考虑。你看，提高性能的诀窍是始终考虑什么样的数据结构最适合这个特定的过程。您还需要考虑数据结构本身的内容。这段混乱的代码维护着全局状态的每一部分数据。重构后的代码保持了局部性。

状态与绩效有什么关系？让我们再次使用装配线的例子。想象一下，你正在制造一辆汽车，是时候安装车门了。在你的工作站里，门不是在你的旁边，你必须一路走到工厂的后面才能拿到它们。这还不是全部。当你到达那里时，门不是按颜色或侧面分类的。你必须手动检查每扇门，找到合适的那一扇。这是全球状态。在装配过程中，在正确的时间为汽车的每一侧配备一个颜色正确的车门，可以极大地提高效率。你现在的工作只是闩上门。总共六个螺栓。一(1)次操作。

当我说装配线的目标是常数 O(1)时，我的意思是每一步都被分解到操作不需要大量处理的程度。装配线上前面的每一步都将所有事情分解成更小的步骤，并且状态总是保持在本地。

# 软件工程与制造

我知道有很多人反对软件工程这个术语。我明白为什么。目前还没有一种按常规的方法。这个行业还太年轻，任何事情都不能高度正规化。然而，我们可以从制造业中学到很多东西。研究装配线现在是如何工作的将会提供很多见解。我建议你调查一下。