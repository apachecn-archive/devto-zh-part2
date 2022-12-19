# 动态规划——解决任何动态规划面试问题的 7 个步骤

> 原文：<https://dev.to/nikolaotasevic/dynamic-programming--7-steps-to-solve-any-dp-interview-problem-3870>

[![Alt text of image](../Images/e46a16bceab978fe91e416591c449417.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MvNWeOTC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://blog.refdash.com/wp-content/uploads/2018/05/farzad-nazifi-71686-unsplash-e1526498488444-2000x1200.jpg)

# 动态规划——解决任何 DP 面试问题的 7 个步骤

*最初发布于 [Refdash 博客](http://blog.refdash.com/dynamic-programming-tutorial-example/)。Refdash 是一个面试平台，帮助工程师匿名采访来自谷歌、脸书或 Palantir 等顶级公司的经验丰富的工程师，并获得详细的反馈。 [Refdash](https://refdash.com) 还帮助工程师根据他们的技能和兴趣发现惊人的工作机会。*

尽管在构建软件产品方面有丰富的经验，但许多工程师一想到要参加侧重于算法的编码面试就感到紧张不安。我采访了数百名工程师，一些最常见的让工程师感到不安的问题是那些涉及动态编程(DP)的问题。

很多科技公司都喜欢在面试中问 DP 问题。虽然我们可以讨论它们是否能有效地评估一个人在工程岗位上的表现能力，但 DP 仍然是工程师们在寻找他们热爱的工作的路上的一个领域。

## 动态编程——可预测和可准备

我个人认为 DP 问题可能不是测试工程能力的最佳方式的原因之一是它们是可预测的，并且易于模式匹配。相对于工程质量，它们允许我们过滤更多的准备情况。

这些问题从表面上看似乎非常复杂，可能会给你一种解决这些问题的人非常擅长算法的印象。类似地，那些可能无法克服 DP 的一些令人困惑的概念的人，他们的算法知识可能会显得相当薄弱。

现实是不同的，表现的最大因素是准备。所以让我们确保每个人都有所准备。彻底地。

> ### The biggest factor to solve the dynamic programming problem is to be prepared.

## 解决动态规划问题的 7 个步骤

在这篇文章的剩余部分，我将回顾一个方法，你可以按照它来判断一个问题是否是一个“DP 问题”,以及找出这样一个问题的解决方案。具体来说，我将经历以下步骤:

1.  如何识别 DP 问题
2.  身份问题变量
3.  清楚地表达递归关系
4.  确定基本案例
5.  决定你想要迭代地还是递归地实现它
6.  添加记忆
7.  确定时间复杂度

### 样本 DP 问题

为了给我将要做的抽象提供一个例子，让我介绍一个样例问题。在每一节中，我都会提到这个问题，但是你也可以独立于这个问题来阅读。

### 问题陈述:

[![Alt text of image](../Images/054079cbc075d6d37917ae82028fb171.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--84sLt4Wj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://j.gifs.com/BLj1o2.gif)

在这个问题中，我们在一个疯狂跳跃的球上，试图停下来，同时避免沿途的尖峰。

## 规则如下:

1)给你一条平坦的跑道，上面有一堆钉子。跑道由一个布尔数组表示，该数组指示一个特定(离散)点是否没有尖峰。对清楚是真，对不清楚是假。

示例数组表示

[![Alt text of image](../Images/8177e6e188043c61bde5e192155b907f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BJ7UMtL0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://blog.refdash.com/wp-content/uploads/2018/05/post_illustration_1.jpg)

2)给你一个开始速度 S，S 在任何给定点都是一个非负整数，它表示下一次跳跃你将向前移动多少。

3)每一次定点着陆，在下一次跳跃之前，最多可以调整 1 个单位的速度。

[![Alt text of image](../Images/c54aca7064c2adcc3ee9dd2a838895e4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ffhiSp3O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://blog.refdash.com/wp-content/uploads/2018/05/post_illustration.jpg)

4)您想要安全地停在跑道上的任何地方(不需要在阵列的末端)。当你的速度变成 0 时，你停下来。然而，如果你在任何一点落在钉子上，你疯狂的弹跳球就会爆裂，游戏就结束了。

你的函数的输出应该是一个布尔值，表明我们是否可以安全地停在跑道上的任何地方。T3】

## 步骤一:如何识别一个动态规划问题

首先，让我们弄清楚 DP 本质上只是一种优化技术。DP 是一种解决问题的方法，它将问题分解成一系列更简单的子问题，每个子问题只解决一次，然后存储它们的解。下一次出现同样的子问题时，不用重新计算它的解，只需查找先前计算的解。这节省了计算时间，代价是(希望)适度的存储空间开销。

认识到使用 DP 可以解决问题是解决问题的第一步，也是最困难的一步。你要问自己的是，你的问题解是否可以表达为类似的更小问题的解的函数。

在我们的示例问题中，给定跑道上的一个点、一个速度和前方的跑道，我们可以确定下一个可能跳跃的点。此外，似乎我们能否以目前的速度从目前的点停下来，只取决于我们能否从我们选择的下一个点停下来。这是一件伟大的事情，因为通过前进，我们缩短了前方的跑道，使我们的问题变得更小。我们应该能够一直重复这个过程，直到我们到达一个点，在这个点上我们是否能够停下来是显而易见的。

> ### Knowing a dynamic programming problem is often the most difficult step to solve it. Can the solution to a problem be expressed as a function of the solution to a similar minor problem?

## 第二步:确定问题变量

现在我们已经建立了在我们的子问题之间有一些递归结构。接下来，我们需要用函数参数来表达这个问题，看看哪些参数在变化。通常在面试中，你会有一两个变化的参数，但从技术上讲，这可能是任何数字。一个改变参数问题的经典例子是“确定第 n 个斐波那契数”。两个变化参数问题的这种例子是“计算字符串之间的编辑距离”。如果你不熟悉这些问题，不要担心。

确定变化参数数量的一种方法是列出几个子问题的例子并比较这些参数。计算变化参数的数量对于确定我们必须解决的子问题的数量是有价值的，但是它本身对于帮助我们加强对步骤 1 中的递归关系的理解也是很重要的。

在我们的例子中，对于每个子问题，两个可以改变的参数是:

1.  **数组位置(P)**
2.  **速度**

可以说前方的跑道也在改变，但是考虑到整个不变的跑道和位置(P)已经携带了该信息，这是多余的。

现在，有了这两个变化的参数和其他静态参数，我们就有了子问题的完整描述。

> ### Identify the changed parameters and determine the number of sub-problems.

## 第三步:明确表达递归关系

这是一个重要的步骤，许多人为了进入编码而匆忙完成。尽可能清楚地表达递归关系将会加强你对问题的理解，并使其他事情变得容易得多。

一旦你发现了递归关系的存在，并根据参数指定了问题，这应该是一个自然的步骤。问题是如何相互联系的？换句话说，让我们假设你已经计算了子问题。你如何计算主要问题？

在我们的示例问题中，我们是这样考虑的:

因为在跳到下一个位置之前，你最多可以调整 1 的速度，所以只有 3 种可能的速度，因此我们可能是下一个到达的 3 个地点。

更正式地说，如果我们的速度是 S，位置 P，我们可以从(S，P)到:

1.  **(S，P+S)；** #如果我们不改变速度
2.  **(S–1，P+S–1)；如果我们将速度改变-1**
3.  **(S + 1，P+S+1)；如果我们将速度改变+1**

如果我们能在上面的任何一个子问题中找到停止的方法，那么我们也能从(S，P)停止。这是因为我们可以从(S，P)过渡到上述三个选项中的任何一个。

这通常是对问题的一种很好的理解(简单的英语解释)，但是有时你可能也想用数学的方式来表达这种关系。让我们调用一个函数来计算 canStop。然后:

**canStop(S，P) = canStop(S，P+S)| | can stop(S–1，P+S–1)| | can stop(S+1，P + S + 1)**

哇哦，看来我们有递归关系了！

> ### Recursive relation: Assuming you have calculated the subproblem, how would you calculate the main problem?

## 第四步:确定基本案例

基本情况是一个不依赖于任何其他子问题的子问题。为了找到这样的子问题，您通常希望尝试几个例子，看看您的问题如何简化成更小的子问题，以及在什么情况下不能进一步简化。

问题不能被进一步简化的原因是，在给定问题的**约束**的情况下，其中一个参数会变成不可能的值。

在我们的示例问题中，我们有两个不断变化的参数 S 和 P。让我们考虑一下 S 和 P 的哪些可能值可能不合法:

1.  **P 应在给定跑道的边界内**
2.  P 不能是这样的，跑道[P]是假的，因为那意味着我们站在一个尖峰上
3.  **S 不能为负，S==0 表示我们完成了**

有时，将我们关于参数的断言转换成可编程的基本案例可能有点困难。这是因为，除了列出断言之外，如果你想让你的代码看起来简洁并且不检查不必要的条件，你还需要考虑这些条件中哪些是可能的。

在我们的例子中:

1.  看起来这样做是对的。另一种方法是考虑将 P ==跑道末端作为基本情况。然而，有可能一个问题分裂成一个超出跑道尽头的子问题，所以我们真的需要检查不等式。

2.  这似乎很明显。我们可以简单地检查**runway[P]是否为假。**

3.  类似于#1，我们可以简单地检查 S < 0 and S == 0\. However, here we can reason that it is impossible for S to be < 0 because S decreases by at most 1, so it would have to go through S == 0 case beforehand. Therefore **S == 0** 是否是 S 参数的充分基础情况。

## 第五步:决定你是想迭代地还是递归地实现它

到目前为止，我们讨论这些步骤的方式可能会让您认为我们应该递归地实现这个问题。然而，到目前为止，我们所讨论的一切都完全不知道您是决定递归地还是迭代地实现这个问题。在这两种方法中，您必须确定递归关系和基本案例。

为了决定是迭代还是递归，你需要仔细考虑权衡。

[![Alt text of image](../Images/5c743e9965d222ddf92d38533eef4857.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sFxfAasD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7adrih3tf5ajcaoi9zk2.png)

堆栈溢出问题通常是交易的破坏者，也是你不希望在(后端)生产系统中使用递归的原因。然而，出于采访的目的，只要你提到了权衡，你通常对这两种实现都没问题。您应该对实现这两者感到满意。

在我们的特殊问题中，我实现了两个版本。下面是 python 代码:

递归解决方案:(原始代码片段可以在[这里](http://blog.refdash.com/dynamic-programming-tutorial-example/)找到)

[![Alt text of image](../Images/a14d1d2ca7b138a067fc1c87d4485000.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cKzfblKm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1n3bk5kh87s0kvsltk5p.png)

一个迭代的解决方案:(原代码片段可以在这里找到)
[![Alt text of image](../Images/2e067d0033f73911a4bd9a61fb8463c7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sk_pzrvt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4pngdhmmp6w3mafx9tnl.png)

## 第六步:添加记忆

**记忆**是一种与 DP 密切相关的技术。它用于存储昂贵的函数调用的结果，并在相同的输入再次出现时返回缓存的结果。为什么我们要在递归中加入记忆？我们会遇到同样的子问题，这些子问题在没有记忆的情况下会被重复计算。这些重复经常导致指数级的时间复杂度。

在递归解决方案中，添加记忆化应该很简单。我们来看看为什么。记住，记忆化只是函数结果的缓存。有时候，为了挤出一些小的优化，您想要偏离这个定义，但是将记忆化视为函数结果缓存是实现它的最直观的方式。

这意味着您应该:

1.  在每个 return 语句之前，将函数结果存储到内存中
2.  在开始任何其他计算之前，查找函数结果的内存

下面是上面添加了记忆的代码(添加的行突出显示):

[![Alt text of image](../Images/0542c5d182b77857d2726b9a9156b586.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WqaHI322--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yg5r3etvogo9t7as3c8b.png)

为了说明记忆和不同方法的有效性，让我们做一些快速测试。我将对目前为止我们看到的所有三种方法进行压力测试。这里是设置:

1.  我创建了一条长度为 1000 的跑道，在随机的地方有长钉(我选择长钉出现在任何给定位置的概率为 20%)
2.  初始速度= 30
3.  我将所有函数运行了 10 次，并测量了平均执行时间

以下是结果(秒):

[![Alt text of image](../Images/a790e89c53d44da63e663e0a37852f44.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8SuxvJDY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x3g2a42mrxbotpf5j5lb.png)

您可以看到，纯递归方法比迭代方法多花费大约 500 倍的时间，比带记忆的递归方法多花费大约 1300 倍的时间。请注意，这种差异会随着跑道长度的增加而迅速增加。我鼓励你尝试自己运行它。

## 第七步:确定时间复杂度

有一些简单的规则可以使计算动态规划问题的时间复杂度变得容易得多。这里有两个你需要做的步骤:

1.  计算状态的数量——这取决于问题中变化参数的数量
2.  想想每个州所做的工作。换句话说，如果除了一个状态，其他的都已经计算过了，那么计算最后一个状态需要做多少工作

在我们的示例问题中，状态的数量是 **|P| * |S|** ，其中

*   P 是所有位置的集合(|P|表示 P 中元素的数量)
*   s 是所有速度的集合

在这个问题中，每个状态所做的功是 O(1 ),因为给定所有其他状态，我们只需查看 3 个子问题来确定结果状态。

正如我们在前面的代码中提到的，|S|受限于跑道的长度(|P|)，所以我们可以说状态的数量是|P| <sup>2</sup> ，并且因为每个状态所做的工作是 O(1)，那么总的时间复杂度是 O( |P| <sup>2</sup> )。

然而，看起来|S|可以被进一步限制，因为如果它真的是|P|很明显，停止是不可能的，因为你必须在第一次移动时跳过整个跑道的长度。

因此，让我们看看如何对|S|进行更严格的限制。我们称最大速度为 s，假设我们从位置 0 开始。如果我们试图尽快停止，并且忽略潜在的峰值，我们能多快停止？

[![Alt text of image](../Images/1c21c9634789d87c275de946e9b53031.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XwbRebe---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ux2k2k57e1ky4xzjdx0u.png)

在第一次迭代中，我们必须至少到达点(S-1 ),通过将我们的速度从零调整-1。从那里我们至少要向前走(S-2)步，以此类推。

对于长度为 L 的跑道，必须满足以下条件:

(S-1) + (S-2) + (S-3) + …。+ 1 < L

**=>S *(S–1)/2<L**

**=>S<sup>2</sup>–S–2L<0**

如果你找到上述函数的根，它们将是:

**r1 = 1/2 + sqrt(1/4 + 2L)，R2 = 1/2–sqrt(1/4+2L)**

我们可以把我们的不等式写成:

**(S–R1)*(S–R2)<0**

考虑到对于任何 S > 0 和 L > 0，S–R2 > 0，我们需要:

**S–1/2–sqrt(1/4+2L)<0**

**=>S<1/2+sqrt(1/4+2L)**

这是我们在长度为 l 的跑道上可能达到的最大速度。如果我们的速度高于这个速度，即使理论上我们也不能停下来，不管尖峰的位置如何。

这意味着总时间复杂度仅取决于跑道长度 L，如下所示:

O(L * sqrt(L))优于 O(L <sup>2)</sup>

**O(L * sqrt(L))是时间复杂度的上界**

## 厉害了，你晋级了！🙂

我们所经历的 7 个步骤应该给你一个系统地解决任何动态编程问题的框架。我强烈推荐在更多的问题上实践这种方法来完善你的方法。

**以下是你可以采取的一些后续步骤:**

1.  通过尝试找到到达停止点的路径来扩展示例问题。我们解决了一个问题，它告诉你是否可以停下来，但是如果你也想知道为了最终在跑道上停下来应该采取什么步骤呢？您将如何修改现有的实现来做到这一点？

2.  阅读 python 中的 decorators 或其他语言中的类似概念，对于巩固你对 memoization 的理解以及理解它只是一个函数结果缓存非常有用。想一想，对于你想记忆的任何函数，它们一般是如何允许你实现记忆的。

3.  按照我们所经历的步骤解决更多的 DP 问题。你总能在网上找到一堆(例如。 [LeetCode](https://leetcode.com/tag/dynamic-programming/) 或 [GeeksForGeeks](https://www.geeksforgeeks.org/dynamic-programming/) 。在你练习的时候，记住一点:学思路，不要学问题。想法的数量明显减少，这是一个更容易征服的空间，也将更好地为你服务。

当你觉得你已经征服了这些想法时，看看 [Refdash](https://refdash.com/?utm_source=dp_blog_dev) 吧，在那里你将接受一位高级工程师的采访，并获得关于你的编码、算法和系统设计的详细反馈。