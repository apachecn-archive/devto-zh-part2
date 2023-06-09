# 定位母球

> 原文：<https://dev.to/scotthannen/position-the-cue-ball-1nli>

我想大多数人都熟悉台球，也叫“撞球”这是一种在桌子上放球的游戏，你用球杆击打一个球，白色的球，称为“球杆”，使它击打其他球，并有希望将它们击入桌子边缘的一个口袋中。如果一个球正好在一个球袋旁边，那么就很容易把它打进去了。但是如果母球在桌子的一端，而你想敲进去的球在另一端，或者根本不在球袋附近，那就需要更多的技巧了。

十几岁时，我经常打台球。虽然我在将球打入袋中方面变得稍微好一点，但我很快就稳定下来了。几乎每个游戏都遵循相同的模式:

*   首先是“休息”，球排列成三角形，你用母球击打它们，它们向不同的方向滚动。有十五个球，所以即使没有瞄准，也有可能有东西会滚进口袋。
*   接下来，我将开始瞄准下沉单个球。这开始也很容易，因为有很多球，至少有几个已经在口袋附近了。
*   在比赛进行到一半的时候，比赛变得非常艰难，因为我打出了比较容易的球，而剩下的球变得越来越难。最后几个镜头通常几乎是不可能的。

一天，一个男人走进我正在打球的公寓俱乐部，看到我在努力，告诉我如果我想提高，我需要做些什么。

他说，任何人都可以轻松击球并沉球。但真正的技巧是在母球撞入另一个球后控制它滚向何处的能力，这样下一次击球和之后的击球也会很容易。你不能仅仅因为能够投出非常困难的球而获胜，尽管这很有帮助。你赢在一开始就没有让自己准备好做出困难的投篮。

(对我来说)这是革命性的。)我太专注于每一个球是否会进袋，以至于从来没有想过母球会去哪里。我想它可能会因为运气不好而处于有利位置，当它没有时，我试图通过一次艰难的射门来弥补。我甚至不知道定位母球是一件事。(我刚刚才知道甚至有一个词可以形容这个词，就是离开球的地方。这可能是我在任何运动中听过的最符合逻辑的名字了。)

### 这跟软件开发有什么关系？

不过，这听起来很难，所以我停止了打台球，转而成为了一名软件开发人员。果然，一个相似的模式出现了:

*   当我开始编写一个新的应用程序时，很容易想象出一切都会井井有条。你不能一笔带过就把自己逼到墙角，所以一开始很容易乐观。
*   当我继续的时候，有很多简单的任务让我继续前进。我通常从设计数据库表和存储过程开始。如果我被困在一件事情上，我会离开它，去做别的事情。
*   到最后，测试过程会导致新需求的发现。当我相应地调整我的代码时，它很快变得混乱和不可维护。在应用程序完成之前，我不得不做的每一个更改就像是尝试一个棘手的银行射击，并为即将到来的下一个更改留下了更糟糕的代码状态。

当然，将软件开发比作台球只是一种类比，所有的类比都是错误的。

*   在软件开发中，你只能提前计划这么多，因为你不能一开始就把所有的球都放在桌子上。随着时间的推移，您的客户将添加、移动和移除球。他们会移动口袋。他们会从一个口袋里拿出一个球，然后让你把它放进另一个口袋里。那不是一个坏顾客。这就是我们迭代开发软件的方式。这比他们以前把所有的球都放在桌子上，告诉你在比赛结束时记录每一次投篮，并让你坚持下去要好得多。(然后还是把东西搬来搬去。)
*   如果不可预测性甚至错误的选择让你陷入困境，你也可以稍微重新安排一下表格。这就是重构。这不是欺骗——事实上，我们应该不时地这样做。
*   好消息是:理想情况下，没有人与我们对抗，试图定位母球，这样我们的下一次击球就更难了。我们的团队在一起工作，每个人都很熟练，每个人都希望其他人赢。

这里的要点是:每一个镜头都有可能让下一个镜头变得容易、相当困难或者几乎不可能。我们将这种类比应用于编写代码，通过采取合理的步骤来确保我们今天编写的代码不会使我们或其他人明天必须编写的代码变得不必要的困难。但是，当我们甚至不知道明天的需求是什么时，我们怎么能做到这一点呢？

### 没有什么是微不足道的

我们熟悉设计模式、反模式、坚实的原则等等:

*   使用有意义的名称编写简短的方法(15 行或更少的实际代码)
*   使用常量，避免“魔法字符串”
*   不要重复你自己
*   偏好组合而非继承
*   不要用反射和运行时类型检查做坏事，除非你别无选择，而且你通常会这么做

很多时候，当我们做出看似很小的决定，也许是单独的代码行时，我们忽略了这些和其他原则，然后我们想知道为什么我们的代码库最终比我们希望的更粗糙。我们为什么要这样做？以下是几个原因。(我不是说它们是很好的理由。它们只是理由。)

*   我们赶时间。
*   可维护的代码很重要，但由于某种原因，不在我们现在正在处理的类或方法中。(这有多种形式，包括实用主义，比如“我明白为什么单元测试如此重要，但我不为它们费心，因为我是实用主义者，”或者“我的微调实用主义告诉我，当我可以向现有类添加的方法数量没有明确限制时，不要创建新类。”)
*   我们维护着如此脆弱、不可理解的代码，我们所能希望的最好的事情就是把我们的积木放在上面，逃离我们的生活。
*   当审查别人的代码时，我们会忽略次要的细节，因为我们讨厌吹毛求疵。

问题是很难将原因(小决策)与结果(不可维护的代码)联系起来。)这是因为影响是许多小原因的累积，可能需要几周、几个月或更长时间，我们才会感到足够的痛苦来意识到我们的代码已经到了一个糟糕的地方。我们不喜欢自己的结局，但很难回头去看那些让我们走到那一步的步骤。

补救办法是*永远不要认为任何决定都是微不足道的*,在合理的范围内尽最大努力将我们所知道的应用到我们编写的每一行代码中。如果我们确切地知道哪些决定会或不会给我们带来痛苦，以及为什么，那么我们就能够预测未来。但是我们不能确定。我们无法预见未来正是我们编写可维护代码的原因。我们能做出的最准确的预测是一般性的:今天的“琐碎”决定将在明天困扰我们或其他人。

即使我们应用了所有我们知道的东西，我们仍然会打一些结，我们会希望我们没有打。但它不会发生得如此之多或如此之快。我们的应用程序不会永远存在，但它们会更像克林特·伊斯特伍德，而不是莱西。(因为莱西死了，又被一遍又一遍地替代。要么这样，要么无情地将英年早逝的人与糟糕的软件相提并论，要么放弃与克林特·伊斯特伍德的比较。)

### 先想一步

一个更具体的方法，我们可以保持我们的母球定位良好，是通过思考可能的，甚至是假设的未来改变我们的代码。这并不意味着实际编写代码来考虑这些场景。但是如果我们的代码是可维护的，那么我们至少应该能够想象如何修改它。如果我认为未来的需求是可能的，我可能会记录或评论修改的样子。

依赖抽象在这方面起了很大的作用。如果一个类必须验证送货地址，并且它依赖于我为这个类定义的名为`IAddressValidator`的抽象来验证地址，那么即使我当前的实现非常简单，我也为提供一个更复杂的实现甚至是多个实现的门面敞开了大门。即使这种情况从未发生过，我仍然通过将地址验证器和依赖于它的类分开，使它们变得更简单、更易测试。如果行为发生了更大的变化——也许我的验证器会返回建议的更正——那么至少我知道交互是在哪里定义的，如果需要的话，我可以更改接口，因为它就是为此目的而定义的。它不是到处都在使用的巨型通用接口。

我并不是想有先见之明，这当然并不意味着所有未来的改变都很容易，也不意味着我不必改变任何现有的接口或类。希望这确实意味着，通过将行为分成我可以想象变化的依赖项，我将写一些不会让下一个人过多诅咒我的东西。我想让他们觉得我是作为他们的伙伴而不是对手来比赛的。

台球是一种有趣的游戏，所以决定是提高还是仅仅击球应该完全取决于什么对我们来说是有趣的。软件开发也应该是有趣的，但显然更重要。我们指尖有别人多年积累的智慧，也常常有自己忘记听的经验。这种经历有助于我们提前计划，为不可预见的未来做准备，而不是落后于形势。