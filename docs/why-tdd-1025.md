# 为什么是 TDD？

> 原文：<https://dev.to/quii/why-tdd-1025>

如果不重复别人说过的话，很难写出关于测试驱动开发(TDD)的文章，但是它帮助我围绕这个问题组织我的想法。所以在某种程度上这是一种自私的努力，但是我希望这至少能让读者思考 TDD 以及它在软件开发中的重要作用。

## 软件

软件的承诺是它可以改变。这就是为什么它被称为*软*器的原因，与硬件相比，它具有延展性。一个伟大的工程团队应该是一个公司的惊人资产，他们编写的系统可以随着业务的发展而不断交付价值。

那么，为什么我们在这方面如此糟糕呢？

你听说过多少彻底失败的项目？或者成为“遗留”，不得不完全重写(重写也经常失败！)

一个软件系统到底是如何“失败”的？难道就不能改到正确为止吗？这是我们的承诺！

1974 年，在我出生很久以前，一位聪明的软件工程师曼尼·雷曼描述道

## 连续变化的规律

> 现实世界中使用的任何软件系统都必须改变，或者在环境中变得越来越无用

显而易见，一个系统*必须*改变或者变得不那么有用了，但是这一点经常被忽视吗？

许多团队被激励在特定日期交付项目，然后继续下一个项目。如果软件是“幸运的”,至少会有某种形式的移交给另一组人来维护它，但当然不是他们写的。

人们通常关心的是试图选择一个框架来帮助他们“快速交付”,而不是关注系统需要如何发展的寿命。

即使你是一个不可思议的软件工程师，你仍然会成为不知道系统未来需求的受害者。随着业务的变化，您编写的一些优秀代码现在已经不再适用。**软件必须改变**

雷曼兄弟在 70 年代蒸蒸日上，因为他给了我们另一条法律去思考。

## 复杂性递增的规律

> 随着一个系统的发展，它的复杂性会增加*,除非努力降低它的复杂性*

(强调我的)

他在这里说的是，我们不能让软件团队成为盲目的功能工厂，把越来越多的功能堆到软件上，希望它能长期生存下去。

随着我们领域知识的变化，我们需要继续管理系统的复杂性。

## 重构

软件工程的许多方面保持了软件的可延展性，例如:

*   开发者授权
*   通常“好”的代码。关注点的合理分离等
*   通讯技能
*   体系结构
*   可观察性
*   可部署性
*   自动化测试
*   反馈回路

我将专注于重构。这是一个经常被抛出的短语“我们需要重构它”——这是在开发人员第一天编程时不假思索地对他们说的。

这个短语来自哪里？重构和写代码有什么不同？

### 系数

在学校学数学的时候，你可能学过因式分解。这里有一个非常简单的例子

计算`1/2 + 1/4`

要做到这一点，你需要分解分母，把表达式变成

`2/4 + 1/4`然后你可以把它变成`3/4`。

我们可以从中吸取一些重要的教训。当我们*分解表达式*时，我们**没有改变表达式**的含义。两者都相等，但我们让它更容易使用；通过将`1/2`改为`2/4`，它更容易融入我们的“领域”。

当你重构你的代码时，你试图找到使你的代码更容易理解的方法，并且“适应”你当前对系统需要做什么的理解。最重要的是**你不应该改变行为**。

### 当重构代码时，你不能改变行为

这一点非常重要。如果你同时在改变行为，你就是在同时做两件事。作为软件工程师，我们学会将系统分解成不同的文件/包/函数等，因为我们知道试图理解一大堆东西是很难的。

我们不想同时考虑很多事情，因为那是我们犯错的时候。我目睹了许多重构努力的失败，因为开发人员贪多嚼不烂。

当我在数学课上用纸和笔做因式分解时，我必须手动检查我是否改变了头脑中表达式的意思。我们如何知道在重构代码时我们没有改变行为，尤其是在一个重要的系统上？

那些选择不写测试的人通常会依赖手工测试。对于除了小项目之外的任何项目，这将是一个巨大的时间下沉，并且从长远来看是不可扩展的。

为了安全地重构，你需要自动化测试，因为它们提供了

*   自信你可以重塑代码而不用担心改变行为
*   关于系统应该如何运行的人类文档
*   比手工测试更快更可靠的反馈
*   为了使代码可测试，it *通常*必须遵循单一职责、显式依赖(即没有全局变量)的最佳实践；也有助于重构的属性。

## 为什么要 TDD

有些人可能会引用雷曼兄弟关于软件必须如何改变和过度思考精心设计的名言，浪费大量时间试图创建“完美”的可扩展系统，最终出错并一事无成。

这是软件糟糕的旧时代，分析师团队会花 6 个月的时间编写需求文档，架构师团队会再花 6 个月的时间提出设计，几年后整个项目失败了。

我说糟糕的过去，但这仍然发生了！

敏捷告诉我们，我们需要迭代地工作，从小处着手，不断改进软件，这样我们就可以快速获得软件设计的反馈，以及它如何与真实用户一起工作；TDD 实施了这种方法。

TDD 通过鼓励一种不断重构和迭代交付的方法，解决了 Lehman 所说的法则和其他从历史中学到的教训。

### 小步前进

*   为少量期望的行为编写一个小测试
*   检查测试失败，并显示明确的错误(红色)
*   编写最少的代码来通过测试(绿色)
*   重构
*   重复

随着你变得熟练，这种工作方式会变得自然而快速。

如果你处于系统不是“绿色”的状态，你会期望这个反馈循环不会花很长时间，并感到不安，因为这表明你可能掉进了兔子洞。

您将总是在测试反馈的支持下开发小而有用的功能。

## 对常见异议的简洁回应

### 测试*不要*帮我重构。每次我重构的时候，测试负载停止通过/编译

还记得*应该是什么样的重构吗？只是改变你的程序的表达方式，而不是改变行为。现在问问你自己为什么你的测试失败了。这将是因为你的测试**与实现细节**过于耦合。*

你可能*嘲笑得太多，测试了不相关的细节。记住单元测试是*而不是*只针对函数/类/无论什么。*

一个行为单元可以被测试，它可能有许多内部合作者来使该行为起作用；只是不要测试它们！

倾听你的测试，并根据他们告诉你的去做。

### 我不喜欢写测试，因为我想先探索设计，然后再写测试。

编写你的第一个测试很难/很耗时；*如果*你的第一个测试是“做一个与 twitter 竞争的网站”。

不管你是否实践 TDD，作为一名软件开发人员，能够将问题分解成小块是一项重要的技能。

这让我们在一个更小的问题空间中工作，并快速交付小块价值，让我们在工作时验证我们的假设。这一切都是为了从过去的错误中吸取教训，在前期设计上做了太多的工作。

TDD 的美妙之处在于它迫使我们从小处着手——除非你喜欢花大量时间编写一个大的测试，而没有看到测试通过的内啡肽冲动。

由于从小处着手的限制，这将挑战你的假设，因为你会更快地得到反馈。

事后编写测试通常更难，也更容易出错。您更有可能编写不容易测试的代码，因为您的代码是由您头脑中的假设驱动的，而不是由要求特定行为的测试驱动的。

此外，TDD 中的一个重要步骤是第一步；看看你的测试是如何失败的，看看这个错误是否有意义。这迫使您编写人体工程学测试，向阅读它的开发人员解释哪里出了问题。

我职业生涯中太多的时间都浪费在调试那些失败的测试上了

### 耗时太长

你应该读读 GeePaw 的 TDD(测试驱动开发)(测试驱动开发)(测试驱动开发)(测试驱动开发)(测试驱动开发)的代码块(测试驱动开发)(测试驱动开发)的代码块(测试驱动开发)谬误(T2 ),因为它很好地解释了为什么这种思路是错误的(至少一旦你精通了测试驱动开发)。

如果你太懒，我的 TL；DR 版本是

*   实际上，你不会在 9:30 到达办公桌前，一直写代码到 5:30
*   你所做的是混合了。1)对，写代码。2)思考代码，研究已有的代码。3)修改代码并运行它，看看会发生什么(例如启动服务器，看看会发生什么，调试等等)
*   前提是你写的测试基本上是 2 和 3 的一部分，但是要有条理和更快。

“学习”部分变得更容易了，因为正如 GeePaw 所说

> 这几乎就像测试代码形成了一种对运输代码的克里夫笔记。一个让我们更容易学习的脚手架，这让我们更容易知道发生了什么。这将减少我们一半的代码学习时间。

### 与“真正的”软件相比，所有的例子都是不现实的

这又回到了分解问题的能力上。随着你对 TDD 和软件开发的实践，你将学会如何分解问题，使它们看起来像你所学的简单例子。

一般来说，如果你的代码太难测试；不“现实”——写得很差。

## 包装完毕

*   软件的优势在于我们可以改变它。随着时间的推移，大多数软件都需要改变。
*   为了改变软件，我们必须随着它的发展而重构它，否则它会变得一团糟
*   一个好的测试套件可以帮助你更快地重构，并且压力更小
*   TDD 可以帮助并迫使你迭代地设计出分解良好的软件，并通过测试来支持未来的工作。