# 测试驱动开发有用吗？

> 原文：<https://dev.to/ruarfff/does-test-driven-development-work-p54>

## 简介

伯特兰·罗素(Bertrand Russell)关心的问题是，为了能够继续接受数学教育，必须接受某些公理，即使这些公理不一定被严格证明。他在他的书[数学哲学导论](https://people.umass.edu/klement/imp/imp-ebk.pdf)中讨论了这个问题。关于 Bertrand Russell 的《T4》一集中给出的一个例子是，对于空间中的任意两点，这两点之间存在一条直线。这是直观的，但没有证据证明它的真实性。缺乏证据的一个主要问题是，即使有些东西可能是直观的，也有可能认为这个东西对你来说不是直观的，或者其他答案更直观，没有证据可以证明一个论点是对还是错。

这篇文章将要探讨的一个问题是，我们有证据证明测试驱动开发(TDD)是有效的吗？

在这个上下文中,“工作”是什么意思？经常使用的两种测量方法是:

*   生产力
*   代码质量

实践 TDD 是否意味着生产率或代码质量的真正提高？仅仅测量生产率或代码质量就已经足够困难了。

在这篇文章中，我们将会看到一些关于 TDD 的讨论，以及一些用科学方法评估 TDD 的努力。

## 业内意见

在我们查看科学研究和数据之前，有必要回顾一下围绕 TDD 的一些众所周知的主观讨论。

[肯特·贝克](https://en.wikipedia.org/wiki/Kent_Beck)被[认为在 1999 年左右开发或者‘重新发现’](https://en.wikipedia.org/wiki/Test-driven_development)了 TDD。从那以后，有许多 TDD 的支持者和关于这个主题的[众多书籍](https://www.amazon.com/s/ref=nb_sb_ss_i_1_14?url=search-alias%3Dstripbooks&field-keywords=test+driven+development&sprefix=test+driven+de%2Cstripbooks%2C218&crid=2ADA7QMBPC5FP)。业内肯定有一个声音团体声称 TDD 对于编写好的软件至关重要。

David Heinemeier Hansson (他的名字经常被缩写为 DHH)是 [Ruby on Rails](http://rubyonrails.org/) 的创造者，他在博客[中表达了一种似乎相当普遍的反对 TDD 的观点。测试万岁](http://david.heinemeierhansson.com/2014/tdd-is-dead-long-live-testing.html)。这篇文章的一个有趣的方面是，有人认为 TDD 在某种程度上已经被接受为行业中进行开发的正确方式，甚至正在造成伤害。这篇文章是几年前写的，但是争论似乎没有太大的变化。

以我个人的经验，TDD 并不被普遍接受为软件开发中的一个必要过程，也很少被强制要求。有很多争论，虽然作为 TDD 的实践者并不被轻视，但它通常并不被认为是重要的或必然正确的。当然，这在不同的软件团队之间是不同的，但总的来说，这是我的经验。在我参加的站立会议上，经常听到开发人员说这样的话，“我已经完成了，但现在只需要添加测试”。在这样的对话中，我从未听到任何人建议应该使用 TDD 过程。在很大程度上，这是由单个开发人员决定的。

鲍伯·马丁可能是一个两极分化的人，但他是 TDD 的重要支持者。当 TDD 不起作用时，他试图在他的文章[中反驳 DHH 的观点。](https://8thlight.com/blog/uncle-bob/2014/04/30/When-tdd-does-not-work.html)

最初的“TDD 已死”帖子中的一个参考是[吉姆·科普林](https://en.wikipedia.org/wiki/Jim_Coplien)的文章[为什么大多数单元测试都是浪费](http://rbcs-us.com/documents/Why-Most-Unit-Testing-is-Waste.pdf)，在[吉姆·科普林和鲍伯·马丁争论 TDD](https://www.youtube.com/watch?v=KtHQGs3zFAM) 的视频中讨论了这篇文章。

TDD 已死的话题也引发了肯特·贝克、T2、马丁·福勒和 T4 的长时间讨论，这很有趣，如果你有兴趣可以在这里观看:

*   [TW Hangouts | TDD 死了吗？- YouTube](https://www.youtube.com/watch?v=z9quxZsLcfo)
*   [TW Hangouts | TDD 死了吗？第二部分- YouTube](https://www.youtube.com/watch?v=JoTB2mcjU7w)
*   [TW Hangouts | TDD 死了吗？第三部分- YouTube](https://www.youtube.com/watch?v=YNw4baDz6WA)
*   [TW Hangouts | TDD 死了吗？第五部& VI - YouTube](https://www.youtube.com/watch?v=gWD6REVeKW4)

这些视频中一个有趣的细节是围绕一个愉快的工作流程的讨论，如果在这个领域中有一个明显的区别，那就是一个人更喜欢写一个测试来开始解决一个问题，或者一个人不喜欢写一个测试，直到问题的某种形式的解决方案已经被写出来。

所有这些只涵盖了业内少数人的投入，还有很多例子。我在这里指出这些是因为，尽管在所有这些博客帖子和讨论中发生了丰富的辩论，但没有得出结论性的答案。似乎很少有一方信服。这可能只是事情的本质，但也许我们可以探索得更深一点。

几乎在所有的讨论中都有一个共识，那就是自动化测试很重要。大多数的分歧似乎是围绕着 TDD 作为一个过程的看法，也许还有自动化测试的粒度。

如同软件开发中的许多主题一样，我们分享和消费的许多关于 TDD 的信息都是基于观点和轶事。我们从讲故事者的角度听到许多故事，我们试图建立一个正确的画面，这样我们就可以尝试将它应用到我们自己的情况中。我们通常只是尝试一些东西，看看它们是否有效。一些数据可能会有用。只是软件开发很难用这种方式来衡量吗？当谈到 TDD 时，至少已经做了一些努力来收集数据。

## TDD 研究

在 2003 年，一项名为[的研究对
行业](http://staff.unak.is/andy/MScTestingMaintenance/Homeworks/STMHeima7TestDrivenDevelopment.pdf)的测试驱动开发进行了初步调查，有“24 名专业的成对程序员”参与，其中一组使用 TDD 开发，另一组使用更传统的(当时)设计-开发-测试-调试瀑布方法。该研究还参考了德国早期的一项对 19 名研究生进行的研究，其结论是

> 测试优先的方式既不会导致更快的开发，也不会提高质量。然而，
> 程序的可理解性增加了，这是根据现有的
> 接口的适当重用来衡量的。

这项研究有很多局限性，2003 年的研究试图解决这些局限性。

根据 George 和 Williams 的论文，TDD 小组生产的代码通过了 18%以上的功能黑盒测试，但开发时间增加了 16%。

> 这项研究的一个假设是，TDD 方法将产生具有较高外部代码质量的代码。基于所进行的数据分析，实验结果支持 TDD 方法产生具有优秀外部代码质量的代码。但是，结果的有效性必须在外部有效性部分讨论的限制范围内考虑。

还有一些有趣的发现，即对照组没有进行测试。一般来说，不实践 TDD 似乎会导致缺乏测试的倾向。

这项研究有许多局限性，但它仍然是一个很好的努力来收集一些关于 TDD 如何在行业中工作的真实数据。就证明 TDD 是否有效而言，这项研究与此相去甚远。

一篇有趣的论文是詹森博士(2006)。测试驱动开发对软件质量的影响的实证评估，它关注其他研究并对他们收集的数据进行分析。这是一篇很长的论文，我不会在这里重复太多，但我会强调一些有趣的数据和观点。这些数据很有趣，但值得记住的是，这些数据来自 2000 年初，样本量相对较小。

以下表格总结了 [Janzen，d . s .(2006)](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.94.9412&rep=rep1&type=pdf)中引用的各种论文的研究结果

类型(CE)是对照实验，类型(CS)是案例研究。

#### [业内](#in-industry)

| 研究 | 类型 | 公司数量 | 程序员数量 | 质量效应 | 生产率效应 |
| --- | --- | --- | --- | --- | --- |
| 乔治 | 这一个 | three | Twenty-four | TDD 多通过了 18%的测试 | TDD 花费了 16%的时间 |
| 最大化 | 特许测量员 | one | nine | 缺陷密度降低 50% | 最小影响 |
| 威廉姆斯 | 特许测量员 | one | nine | 缺陷密度降低 40% | 无变化 |

#### 在学术界

| 研究 | 类型 | 程序员数量 | 质量效应 | 生产率效应 |
| --- | --- | --- | --- | --- |
| 爱德华兹 | 这一个 | Fifty-nine | 缺陷减少 54% | 不适用的 |
| 商人 | 这一个 | eight | 改进的信息流 | 50%的改进 |
| [穆勒](http://ieeexplore.ieee.org/document/1049202/) | 这一个 | Nineteen | 没有变化，但更好的重用 | 无变化 |
| [Pancur](http://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=1248175) | 这一个 | Thirty-eight | 无变化 | 无变化 |
| [Erdogmus](http://ieeexplore.ieee.org/document/1423994/) | 这一个 | Thirty-five | 无变化 | 提高生产力 |

这里的数据表明 TDD 的结果大多是正面或中性的。有一个例外(之前提到的乔治论文), TDD 比控制组多花了 16%的时间，但是注意到控制组在那个研究中写了更少的测试。

> TDD 作为一种设计方法的本质实际上还没有被研究过，但是早期分散的采用仅仅基于轶事证据。
> 
> 虽然实证研究很少会产生绝对可重复的结果，但这种研究可以提供因果关系的证据，暗示在给定的环境中最有可能出现的结果。

这对我来说是一个伟大的观点。测量是困难的，对于这样的事情获得完全结论性的结果几乎是不可能的，但是我们仍然可以使用证据来帮助决策。

> 调查数据显示，开发人员对 TDD 过程的看法随着 TDD 经验的增加而提高，而对最后测试编程的看法却下降了。

那篇论文中的大部分数据依赖于调查。这些调查表明，一般来说，开发人员在测试和软件开发方面的知识和经验越多，就越有可能对 TDD 过程有更好的体验。有趣，但不是主要的启示。

论文得出的结论是:

> 这项研究已经证明了 TDD 能够并且很有可能以最小的代价来提高软件质量的某些方面。特别是，它在代码复杂性、大小和测试方面显示出统计上的显著差异。

这个结论似乎非常有利于 TDD。这些研究的一个值得注意的问题是，样本量很小，对结果的统计学意义提出了一些疑问。也就是说，我认为这项工作总比什么都没有好，至少给了我们一些线索。

> 这项研究揭示了新手和成熟开发者在 TDD 接受度和效率上的许多差异。

这对我来说只是一个有趣的观察。为什么体验和对 TDD 的接受之间有关联？

类似的工作:[测试驱动开发概述
研究项目和实验](http://proceedings.informingscience.org/InSITE2012/InSITE12p165-187Bulajic0052.pdf)也查看各种研究论文。这一份稍微新一些(2012 年)，包括了一些更近期的研究。一个重要的补充是 IBM 和微软开发团队的一项研究。

这项研究的最终结论是(Nagappan 等人，2008 年):

*   降低缺陷密度(IBM 40%，微软 60% - 90%)
*   编码功能所需的时间增加(15% - 35%)。

对该研究有效性的威胁被确定为(Nagappan 等人，2008 年):

*   使用 TDD 方法的开发人员的积极性更高。
*   使用 TDD 开发的项目可能更容易。

那篇论文的发现不如 Janzen 的积极，因为他们正确地指出，结果差异太大，样本量太小，无法得出任何积极的结论。

另一个在 2012 年进行的对照实验再次得出结论，TDD 可能是一件好事，但需要更多的证据。

【2016 年的另一项研究对比测试最后开发(TLD)来观察 TDD 的效果。

> 在本文中，我们报告了一个实验
> [13]的复制，在该实验中，TDD 与最后测试方法进行了比较。

这项研究似乎倾向于一个结论，TDD 并没有改善 TLD 的情况:

> 考虑到第 5 节中给出的限制，相对于迭代开发技术
> 而言，似乎
> TDD 既没有提高也没有降低参与者的
> 性能，在迭代开发技术中，单元测试是在生产代码之后编写的

如果你看看测试是如何进行的，你会发现一个有趣的方面，那就是程序员如何使用迭代开发。对于 TDD 和 TLD 来说，测试和生产代码之间有一个相当紧密的循环。也许一个紧密的迭代循环比测试代码的顺序更重要？

在我看来，这篇论文没有提供足够的数据来给出这样或那样的结论。这当然不能证明 TDD 比在为一个相当大的项目写完所有的生产代码后再写所有的测试更好或更差。

我们要看的最后一个研究是我最近偶然看到的，叫做[对测试驱动开发过程的剖析:测试优先还是测试最后真的重要吗？这项研究实际上似乎得出了与先前研究相似的结论，只是在这种情况下，所涉及的程序员在工业中工作。此外，与迭代测试最后(ITL)开发(我认为这是一个比 TLD 更好的术语)和 TDD 进行了比较。](https://arxiv.org/pdf/1611.05994.pdf)

该研究中另一个有趣的结论是，在某种程度上，更短的周期时间(生产代码和测试之间的时间)确实会带来更好的质量。

## 一些结论

看着这些数据，很容易说出“TDD 有效吗？”这个问题的答案尚无定论。

如果问题变得更具体，至少有一些答案。

支持短迭代测试周期的证据似乎非常多，非常类似于 TDD 文献中所描述的，对代码质量有明显的改善，而对生产率的影响最小。用小的可测试代码块进行小迭代的过程看起来确实会产生更易维护的代码。

目前的证据表明，单元测试胜过集成和更高层次的测试。

目前很少有证据表明实践 TDD 是不好的。

在一个紧凑的迭代周期中，先写测试还是后写测试更好还没有定论。

测试是一门学科，在看到它的真正好处之前需要花时间去学习。这是我自己的观察，但也得到我们所研究的一些观察的支持。

我们能确定 TDD 是否真的有效吗？我不知道。从积极的一面来看，这类问题似乎越来越常被问到。更严格的研究正在进行。以[妮可·福斯格伦](http://nicolefv.com/)、[杰斯·汉布尔](https://twitter.com/jezhumble)和[吉恩·金](https://itrevolution.com/faculty/gene-kim/)的《加速》这本书为例。这不是关于 TDD 的具体内容，但是这是一个很好的例子，说明了在软件开发中什么样的技术是真正有效的，所以至少我们应该有更准确的信息来帮助我们做出这样的决策。

## 个人对这一切的想法

我花了很多时间敦促其他开发人员去做 TDD 或者至少尝试一下。通常，人们会尝试一下，但很难坚持下去。我试图解释说，这是你随着时间的推移学到的东西，当你变得擅长时，这很棒！

我经常想到我可能是错的。如果我试图让人们在不太好的事情上投入时间呢？有什么证据证明 TDD 是有效的呢？仅仅因为它对我有效，并不意味着它对每个人都有效。

这就是这篇文章的原因。我想寻找 TDD 确实有效的证据。我也想尽可能多地阅读那些反对 TDD 或其某些方面的人的文章。仅仅因为我认为 TDD 是好的，我就倾向于避开它们，这不是一个好的学习方法。

我个人认为各种各样的 TDD 方法都是可以的。TDD 的定义不应该像某些古代文本那样完全照字面意思理解，永远不要改变。

对我来说，TDD 最大的优势之一(也许是最不被重视的优势)是它鼓励的设计。我经常听到的抱怨是太多的单元测试导致代码难以维护和更改。这只是学习过程中的一个常见陷阱。一旦你将 TDD 添加到你的软件开发技术的武库中，并很好地学习它，它将真正有助于实现好的软件设计。如果好的、干净的、设计良好的软件是你的目标，我相信 TDD 会真正帮助你实现这个目标。

虽然有时感觉 TDD 有点太难了。这让我想起了尝试用 Java 做函数式编程。如果一切从一开始就设计成便于 TDD，那就相当容易了。这是非常罕见的，而且在我们不得不使用的系统中，一直尝试 TDD 是非常困难的。多亏了今天的工具和教育材料，事情似乎正在好转。

尽管有缺点，在我阅读和经历了一切之后，我仍然认为 TDD 是一个非常好的工具。我将继续使用它，并鼓励其他人也这样做。

## 参考文献

乔治，b .，威廉姆斯，l .(2003 年)。测试驱动开发在工业中的初步调查

简曾博士，塞迪安，s .(2005 年)。测试驱动开发:概念、分类和未来方向

詹森博士(2006 年)。测试驱动开发对软件质量影响的实证评估。

桑切斯，J. C .，威廉姆斯，l .，马克西米利安，M .(2007 年)。在 IBM 持续使用测试驱动开发实践

(2012 年)布拉季奇，桑巴西瓦姆，斯托伊奇。测试驱动开发研究项目和实验概述。

Causevic，a .，Sundmark，d .，Punnekkat，S .(2012 年)。测试设计技术知识对测试驱动开发的影响:一个受控实验

Mä kinen，s .，Münch，j .(2014 年)。测试驱动开发的效果:实证研究的比较分析

[Fucci，d .、Scanniello，g .、Romano，s .、Shepperd，m .、Sigweni，b .、Uyaguari，f .、Turhan，b .、justino，n .、Oivo，m .，(2016 年)。使用多站点盲分析方法对测试驱动开发的效果进行外部复制](http://people.brunel.ac.uk/~csstmms/FucciEtAl_ESEM2016.pdf)

[Goto Fail、Heartbleed 和单元测试文化](http://www.martinfowler.com/articles/testing-culture.html)

[测试驱动开发-维基百科](https://en.wikipedia.org/wiki/Test-driven_development)

对测试驱动开发过程的剖析:先测试还是后测试真的重要吗？福奇等人，ICSE (2017 年)

测试驱动开发过程的剖析:测试优先还是测试最后真的重要吗？|晨报

伯特兰·罗素-面对面访谈(BBC，1959) - YouTube

伯特兰·罗素-在我们的时代 BBC 广播 4 台- YouTube

伯特兰·罗素(第 1 部分，共 6 部分)权威和个人:社会凝聚力和人性