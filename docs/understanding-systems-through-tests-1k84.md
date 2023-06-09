# 通过测试了解系统

> 原文：<https://dev.to/rishibaldawa/understanding-systems-through-tests-1k84>

我假设读者了解不同的测试。如果没有，可以考虑浏览一下这篇博文或维基百科。

当我处于一个奇怪的代码库 <sup id="fnref1">[1](#fn1)</sup> 并且时间紧迫的时候，我会走捷径，首先通过所有的测试。如果测试组织良好，内容丰富，易于理解；我能迅速理解系统(或一组系统)及其邻居的经验证的规则和限制。如果它们稀少、杂乱或者急需帮助，我知道我的一周会变得更有趣。

## 单元测试

阅读单元测试应该有助于你认识到团队对单一单元的看法。输入、输出值有助于您猜测周围流动的是哪种数据。mock/stub 应该有助于理解其他组件的行为。测试名称应该有助于您找出组件周围的用例/边缘用例。围绕特定维度相关的边缘情况(如日期、国家)的测试告诉您，系统以及您在其中所做的任何更改都需要尊重它们。

缺乏测试或明显的差距，意味着代码库没有得到充分验证。你现在是在狂野的西部，孩子！

## 集成测试

集成测试帮助您找出我们与之交互的系统和服务的边界。输入/输出值有助于您了解系统之间共享的数据类型。与单元测试相比，这将帮助您识别哪些数据留在系统中，哪些数据被传递。

如果有太多的集成测试(尤其是单元测试)，预计系统不会太关心延迟(甚至可能是总体性能)。系统可能更关心可用性或准确的交互。如果对一个特定的邻近系统进行了太多的测试，我敢打赌这个服务是不可靠的，而且在过去，开发人员在使用这样的系统时有过糟糕的经历。我也会留意他们的。

如果测试太少，要么是系统没有太多的交互，要么是与相邻系统的集成还没有痛苦。一般都是时间问题。要么软件被弃用，要么有人在令人沮丧的停机后增加了测试。

## 验收测试

验收测试，当可用时，是理解软件支持的核心功能的好方法。仍然有一点代码到业务逻辑的转换，但这总是一个有用的练习。我不太关注输入和输出，因为这些测试倾向于偏向“快乐路径”用例。可能存在任何奇怪的边缘情况处理来对抗非确定性行为。

## 特性测试

一般来说，非程序员希望代码做的事情。如果有太多这样的测试，QA 或开发人员会带着 BDD 去城里。无论哪种方式，它都将诚实地尝试传递与验收测试相同的价值，但使用更人性化的语言。

根据经验，很少有准确的特性测试。大多数表示部分功能或更糟。执行测试规范的代码可能会泄露更多的信息，但是我在过去已经被烧伤过几次了。

## 对比测试

这告诉我，代码提取、转换和/或加载关键数据集，系统需要关注准确性。此外，由于比较数据的成本比实际转换成 POJOs 的成本要小，所以您可能会处理复杂的结构或非常简单的结构；但是中间没有东西。还可能涉及合规性/法规。一个很大的教训是，如果你正在添加新的源或者改变输出，确保测试仍然是准确的第一次将是痛苦的。可能是手工操作。

## 生成性测试

这些太罕见了。当他们在场时，他们帮助我快速掌握系统的核心假设和期望。变体帮助我认识到系统处理的术语/技术。如果这些很难理解，那么你正在处理一个有许多副作用的复杂系统 <sup id="fnref2">[2](#fn2)</sup> 。

## 模糊测试

这些告诉我，系统应该负责所有的错误处理，不应该崩溃。所有的 API 都需要意识到随机输入 <sup id="fnref3">[3](#fn3)</sup> ，不安全的行为是不能容忍的。

## 旁注

*   如果测试不是部署链的一部分，或者不是经常进行，那么您可以打赌它们已经过时了。别理他们，继续前进。至少，小心行事。
*   还有更多类型的测试。甚至我描述的那些都有子类别。我选择了那些我经常遇到的来形成观点。
*   这部分是在特别忙的时候写的。我花了很多时间来完成这个。我提前为任何不准确之处道歉。

* * *

* * *

1.  我带领突击队已经两年多了，所以这种事情经常发生。 [↩](#fnref1)

2.  可能证明了不变测试的必要性

3.  基于属性的测试帮助我了解可以传入的有效输入值的精简子集。 [↩](#fnref3)