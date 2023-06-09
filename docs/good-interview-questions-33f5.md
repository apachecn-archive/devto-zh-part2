# 好的面试问题

> 原文：<https://dev.to/thagomizer/good-interview-questions-33f5>

很多人不明白，做好面试是一种可以学习和提高的技能。似乎有一种趋势，就是使用你作为候选人时被问到的同样的问题。似乎新的面试官经常高估了 30 - 60 分钟能完成的工作，或者一个职位的招聘门槛。不管是什么原因，结果是许多技术性面试问题在评估任何东西时都非常糟糕。所以我想今天我会试着给你一些改进面试问题的指导。

## 接吻=保持简单愚蠢

我的第一个建议是，你的问题应该是一两句话。几年前，当我开始接受采访时，我从瑞安·戴维斯那里学到了这一点。

一句话可能太短了。毕竟，大多数真正的编码问题和任务不仅仅是用一句话来描述。在这个限度内问出有用的东西似乎是不可能的。很难把问题归结为一两句话，但这是值得的。简短的问题给了候选人更多的时间去工作，也给了你更多的时间去评估候选人。记住，你说话的任何时候都是候选人不需要展示他们技能的时候。简洁的问题也更符合时间框架，更容易适应不同的技能水平。如果你的问题不能用一两句话来概括，考虑把它分成多个部分。这样，如果候选人只有时间处理一部分，他们仍然会觉得自己取得了进步。

简短问题的另一个优势是，它们可以让你评估候选人思考问题的能力，并识别边缘情况或其他模糊之处。当我们得到要实现的任务或功能时，我们通常必须找到边缘案例，并与产品所有者或我们的同事一起找出如何处理它们。我希望与那些能够处理问题陈述中的一点模糊之处，并且能够提出问题以更好地理解正在发生的事情的人一起工作。

## 避免行话

我在许多面试问题中看到的另一个问题是行话的使用。行话可能是公司特有的术语；谷歌有很多这样的东西。或者，编程语言或特定行业术语对初级候选人来说可能是行话。

特定语言的行话通常非常微妙，以至于我们意识不到自己在使用行话。例如，关联数组在不同的语言中有不同的名称。我是一名 Ruby 爱好者，Ruby 中的关联数组数据结构被称为 hash。同样的数据结构，Python 里是字典，Go 里是地图，JavaScript 里是对象。如果我让一个候选人使用 hash 实现一个算法，而他们不熟悉 Ruby，即使他们知道如何使用关联数组，他们也可能不明白我在问什么。

这是我用了几年的一个问题中的另一个行话例子，“编写一个将整数转换为扩展形式的函数。”大多数成年人不知道“扩展形式”是什么意思，这使得这个问题比它需要的要难得多。我经常不得不在面试中定义和解释扩展形式，这浪费了宝贵的时间。我最终重写了问题，去掉了行话，这样既节省了时间，又给了我更多关于候选人实际技能的信息。

## 举例子

消除行话、缩短问题的最简单方法之一就是举例。对于我上面提到的问题扩展形式，我将其重写为“编写一个函数，它接受一个整数并打印出该数的分解。例如，372 会变成 3 个一百，7 个十，2 个一。”我也听说过面试官给出一个输入和输出的例子，然后要求候选人指定另一个输入的输出，以验证理解。

当你举例子的时候，注意不要举太多或者展示所有的边缘案例。一两个通常就够了。多了会让人分心。选择例子时要深思熟虑。我的扩展形式问题的例子没有显示像 0 和 1 这样的边缘情况。我发现，如果我预先指定所有的边缘情况，对候选人来说是压倒性的。当我让边缘案例有组织地出现时，我和候选人可以就它们进行对话。703 应该是 7 张一百，0 张十，3 张一吗？还是应该是 7 张一百，3 张一？这两个都是合理的答案，与候选人讨论选择哪一个让我更深入地了解如果被录用，他们将如何为设计过程做出贡献。

## 建立在歧义和扩展之上

我现在有两个编码问题。令人高兴的是，这两者都没有具体说明。正如我上面提到的，我没有预先提到边缘情况。我用的问题有很多合理的解决方法。

这一切都不是偶然的。一个带有内在模糊性的问题允许我在飞行中适应困难。如果人们在挣扎或者我们时间不够了，我可以减少我要求他们处理的边缘案例的数量。如果候选人在挣扎，我可以告诉他们使用什么算法或数据结构。如果候选人喜欢特定的方法，比如迭代而不是递归，他们仍然可以成功。

我也为我的问题写了几个扩展。这些是后续问题，会使问题更具挑战性。我可能会问，如果输入数据非常大，代码会如何变化。或者我可能会问，工作云如何分布在多个机器或数据中心。我问的一个相对常见的扩展是“你的解决方案有多复杂？你能做得更好吗？”所有这些都让我增加了难度，并获得了更多关于候选人的优势、劣势以及在事情“解决”后继续改进或扩展的意愿的信息。

我有灵活问题的另一个原因是模拟真实的软件开发。没有一个面试问题接近真实世界。然而，我们在现实世界中解决的问题很少是明确定义的，并且在我们开始编码后需求经常改变。如果候选人因为问题没有被很好地定义或者因为我改变了需求而感到沮丧，这是一个信号，询问他们如何与反复无常的产品所有者合作。

## 让事情变得比你想象的更容易

面试是一种人为的环境。候选人没有他们的工具和参考。他们应该独立工作。他们经常旅行，可能睡眠不足。他们通常处在一个不知道会发生什么，不知道去哪里找洗手间，也不知道是否会有午休的环境中。在这种情况下，期望人们进行生产质量的编码是不合理的。在现实世界中，我们不会在没有工具的情况下，在短时间内独自工作，同时不断受到评判，并有可能被打断。

所有这些混杂因素意味着你的面试问题应该比你认为合理的要简单。我的问题通常是我在职业生涯早期会乐于回答的问题。我的目标是同事能在 10 分钟内解决的事情，这样候选人就有很大机会在 25-30 分钟的面试中完成。

我用一个粗略的三分法则来决定我的问题是否适当的棘手。带着一个好问题，我预计有三分之一的考生不需要太多帮助就能成功。另外三分之一的人会犯小错误或者需要一些提示。最后的三分之一只有在有重要提示、有算法帮助的情况下才是成功的，或者在可用时间内没有完成。

## 如果可能的话，从你的作品中汲取

我的最后一个建议是，如果可能的话，借鉴你的作品。在我参加的第一次面试培训中，老师鼓励我们不要使用网上或书本上的问题。相反，他建议在我们日常工作中的任务中寻找问题，或者最近出现的特别有趣的事情。首先，这减少了候选人之前看到你的问题和/或记住解决方案的机会。

我上面写的数字分解问题直接来自我当时正在做的工作。我现在使用的两个问题之一来自我在谷歌之前的职业生涯中做了几十次的一个任务。一名员工根据他与顾客的互动提出了一个问题。找到这些问题可能具有挑战性，但如果你睁大眼睛，它们是存在的。

我对基于我实际工作的问题也感觉更好。有很多合理的理由可以解释为什么编写面试程序很糟糕。如果我公司的招聘流程要求我询问他们，我希望他们评估候选人是否能胜任这份工作。如果问题来自我所做的工作，那会让我更有信心评估相关技能。

## 结论

编码面试糟透了。在之前，我已经[写过了。然而，作为面试官，我们可以做很多事情让他们不那么糟糕，对招聘经理更有用。我已经列出了一些改进的方法，但我相信还有更多的方法。很想听听大家对好的和不好的面试问题的看法。](http://www.thagomizer.com/blog/2017/06/18/there-is-no-perfect-interview.html)