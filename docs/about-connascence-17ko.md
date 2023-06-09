# 关于共生

> 原文：<https://dev.to/trikitrok/about-connascence-17ko>

最近在 Codesai，我们一直在研究并在我们的代码中应用[相关性](http://connascence.io/)的概念，甚至已经[做了关于它的介绍性发言](http://slides.com/franreyesperdomo/connascence#/)。我们希望这篇文章是一系列关于共生的文章中的第一篇。

### 1。起源。

**关联**的概念一点也不新鲜。[梅里尔·佩奇-琼斯](https://www.linkedin.com/in/meilir-page-jones-a55132)于 1992 年在他的论文[中通过封装和伴随的方式比较技术](http://wiki.cfcl.com/pub/Projects/Connascence/Resources/p147-page-jones.pdf)中介绍了它。后来，他在 1995 年的《每个程序员都应该知道的关于面向对象设计的知识》一书和 1999 年的《UML 中面向对象设计的基础》一书[中详细阐述了**的概念。**](https://www.amazon.com/Fundamentals-Object-Oriented-Design-Meilir-Page-Jones/dp/020169946X/ref=asap_bc?ie=UTF8)

十年后， [Jim Weirich](https://en.wikipedia.org/wiki/Jim_Weirich) ，在一系列的演讲中把**connasnce**从遗忘中带了回来:[软件设计的大统一理论](https://www.youtube.com/watch?time_continue=2890&v=NLT7Qcn_PmI)，[模块化的构建模块](https://www.youtube.com/watch?v=l780SYuz9DI)和[connasence 检查了](https://www.youtube.com/watch?v=HQXVKHoUQxY)。正如我们将在这篇文章的后面看到的，他不仅让 **connascence** 起死回生，还改进了它的阐述。

最近， [Kevin Rutherford](https://silkandspinach.net/) 写了一系列非常有趣的帖子，在这些帖子中，他谈到了使用 **connascence** 作为选择最有效重构的指南，以及 **connascence** 如何成为比代码气味更客观和有用的工具来识别设计问题。

### 2。什么是共生？

在 90 年代早期，当面向对象开始成为主流编程范例时，作为一种在面向对象设计中评估设计决策的通用方法，出现了**一致性**的概念。在以前的主流范式中，[结构化编程](https://en.wikipedia.org/wiki/Structured_programming)、**扇出**、**耦合**和**内聚**是用于评估设计决策的基本设计标准。为了弄清楚佩奇-琼斯对这些术语的理解，让我们看看他使用的定义:

> **扇出**是给定过程中代码行对其他过程的引用数量的度量。
> 
> **耦合**是过程间连接的数量和强度的度量。
> 
> 内聚性是对给定过程中代码行在满足该过程的目的方面的“专一性”的度量。

根据 Page-Jones 的说法，这些设计标准控制着结构化编程中出现的[级封装](https://books.google.es/books?id=iNAezyMExBkC&pg=PA210&lpg=PA210&dq=levels+of+encapsulation&source=bl&ots=BLv-66F9xq&sig=vaJWjQYq1Bc3_0MHQSKza5y7BiU&hl=en&sa=X&ved=0ahUKEwjF_cy7l-rQAhUBXhQKHSsoCJoQ6AEILjAC#v=onepage&q=levels%20of%20encapsulation&f=false)之间的交互:1 级封装(子程序)和 0 级封装(代码行)，这可以从下面的表格中从[UML 中面向对象设计的基础](https://www.amazon.com/Fundamentals-Object-Oriented-Design-Meilir-Page-Jones/dp/020169946X/ref=asap_bc?ie=UTF8)中看出。

[![alt text](img/a21e779dfe9315b2be0f90190a859505.png "Encapsulation levels and design criteria in structured programming")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1cQRiKND--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rt22s4vavo705xwhlt5a.png)

然而，OO 至少引入了二级封装(类)，它将一级结构(方法)和属性封装在一起。这在封装级别之间引入了许多新的相互依赖，这将需要定义新的设计标准(参见下面的表格[UML](https://www.amazon.com/Fundamentals-Object-Oriented-Design-Meilir-Page-Jones/dp/020169946X/ref=asap_bc?ie=UTF8)中面向对象设计的基础)。

[![alt text](img/80094d4ff91a417aeccd9b793d3ef3cc.png "Encapsulation levels and design criteria in OO")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NefbRDE---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9w7o8z64cdjvqjewkrzv.png)

其中两个新的设计标准是**类内聚**和**类耦合**，它们类似于结构化编程的过程内聚和过程耦合，但是，正如你所看到的，表中还有其他一些标准甚至没有名称。

相关性是所有这些背后的更深层次的标准，因此，它是一种在面向对象设计中评估设计决策的通用方法。这是佩奇-琼斯对**的正式定义:**

> 两个软件元素 A 和 B 之间的关联意味着
> 
> 1.  你可以假设对 A 的一些改变会要求 B 被改变(或者至少被仔细检查)以保持整体的正确性，或者
>     
>     
> 2.  你可以假设一些改变，要求 A 和 B 一起改变，以保持整体的正确性。

换句话说，当两个软件元素必须一起改变以使软件保持正确工作时，它们之间存在着**关联**。

我们可以看到这种新的设计标准是如何用于面向对象中存在的封装级别之间的相互依赖的。此外，它还可以用于更高级别的封装(包、模块、组件、有界上下文等)。事实上，根据 Page-Jones 的说法，**适用于任何具有划分、封装和可见性规则[<sup>【2】</sup>](#nota2)的设计范式。**

 **### 3。共生的形式。

佩奇-琼斯区分了几种形式(或类型)的**关联**。

**从属关系**可以是**静态**，当它可以从代码的词法结构中评估时，或者是**动态**，当它依赖于运行时代码的执行模式时。

**静态关联**有几种类型:

*   **名称的一致性** (CoN):当多个组件必须在一个实体的名称上达成一致时。

*   类型 (CoT)的一致性:当多个组件必须一致同意一个实体的类型时。

*   **意义一致** (CoM)或**约定一致** (CoC):当多个组件必须就特定值的含义达成一致时。

*   **位置的从属关系** (CoP):当多个组件必须在值的顺序上一致时。

*   **算法的一致性** (CoA):当多个组件必须就特定算法达成一致时。

**动态伴随**也有几种类型:

*   **执行的相关性** (order) (CoE):当多个组件的执行顺序很重要时。

*   **时序相关性** (CoTm):当多个组件的执行时序很重要时。

*   **值的相关性** (CoV):当对一些共享元素可以取的可能值有约束时。通常和不变量有关。

*   **(CoI):当多个组件必须引用实体时。**

 ****同形性**的另一种重要形式是**同形性**，当要求元素彼此不同时存在(例如，在相同的名称空间中有不同的名称，或者在不同的名称空间中，等等)。**对比**也可以是静态的，也可以是动态的。

### 4。共生的性质。

Page-Jones 谈到了**相关性**的两个重要属性，这有助于衡量它对可维护性的影响:

*   **明确度**:一个**形式越明确**形式就越弱。
*   **局部性** : **跨越封装边界的共生性**比同一封装边界内的元素之间的**共生性**差得多。

一个很好的重新表述方式是使用所谓的**三轴共生**[<sup>【3】</sup>](#nota3):

#### 4.1。度。

一个**实例的关联度**与其影响的大小有关。例如，一个包含数百个元素的软件元素可能会比一个只包含少数元素的软件元素成为更大的问题。

#### 4.2 局部性。

**关联**的一个实例的位置谈论两个软件元素彼此有多接近。靠近的元素(在相同的封装边界中)通常应该比远离的元素(在不同的封装边界中)呈现更多、更高形式的**共生**。换句话说，随着软件元素之间的距离增加，**的形式会减弱。**

#### 4.3 强度。

佩奇-琼斯说**的关联**有一个明确的光谱。一种形式的**关联**越隐晦，检测起来就越费时费钱。此外，更强形式的**相关性**通常更难重构。根据这个推理，我们知道更强形式的**共生**更难检测和/或重构。
这就是为什么**静态**形式的**一致性**比**动态**形式的一致性弱(更容易检测)，或者说，为什么 **CoN** 比 **CoP** 弱得多(更容易重构)。

凯文·卢瑟福的下图展示了我们之前看到的**共生**的不同形式，但是按照强度递减排序。

[![alt text](img/84e4bc289f48ec259992a7f7ec42f6f1.png "Connascence forms sorted by descending strength (from Kevin Rutherford's XP Surgery)")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--r2h6CPqj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8hqgxh8yoxc759o0gs0i.png)

### 5。相关性、设计原则和重构。

与其他设计原则相比，如固体原理、得墨忒耳定律等更简单。事实上，它可以用来从不同的角度来看待这些原则，因为它们可以被视为使用更基本的原则，如[肯特·贝克](https://en.wikipedia.org/wiki/Kent_Beck)的[实现模式](https://www.amazon.com/Implementation-Patterns-Kent-Beck/dp/0321413091)一书的第一章中的原则。

我们使用**代码气味**，这是代码质量反模式的集合，来指导我们的重构和改进我们的设计，但是，根据凯文·卢瑟福的说法，它们不是这项任务的理想工具[<sup>【4】</sup>](#nota4)。有时**相关性**可能是一个比代码气味这个有点模糊的概念更好的衡量耦合的标准。

**给了我们一个**更精确的词汇**来谈论和推理**耦合和内聚**[<sup>【5】</sup>](#nota5)，从而帮助我们更好地从耦合和内聚的角度来判断我们的设计，并决定如何改进它们。用 Gregory Brown 的话来说，“这允许我们更加具体地对待我们正在处理的问题，这使得我们更容易推理出可以用来削弱组件之间的相关性的重构类型”。**

 **它提供了一个系统中耦合形式的分类，更好的是，提供了每种形式的**共生**所产生的耦合的相对强度的标度。正是这种相对优势的规模使得**一致性**成为重构更好的指南。正如凯文·卢瑟福所说:

> “因为它对耦合的相对强度进行了分类，所以相关性可以作为一种工具来帮助确定应该首先重构的内容的优先级”

共生解释了为什么进行给定的重构是一个好主意。

### 6。我们应该如何应用同生？

Page-Jones 提供了三个使用**相关性**来提高系统可维护性的指导方针:

1.  通过将系统分解成封装的元素，最大限度地减少整体相关性。

2.  最小化任何跨越封装边界的剩余**关联**。

3.  最大化封装边界内的**相关性**。

根据凯文·卢瑟福的说法，前两点符合他所谓的 Page-Jones 重构算法[<sup>【6】</sup>](#nota6)。

这些指导原则概括了结构化设计的理念，即**低耦合和高内聚**，并且适用于面向对象，或者如前所述，适用于任何其他具有划分、封装和可见性规则的范例。

他们可能仍然有点主观，所以我们中的一些人更喜欢用一种更具体的方式来应用**connasnce**使用吉姆·威里奇的两个原则或规则:

> T0, T1, T2, T3, Degree Rule T4, T5, T6, 7, T7, T8: Transform the strong association T10 of T9 into the weak association.*   **Locality Rule** : As the distance between software elements increases, a weaker form of correlation is used.

### 7。下一步是什么？

在以后的文章中，我们将会看到**conassence**的具体形式的例子，将它们与设计原则、代码味道以及可能改进设计的重构联系起来。

这篇文章最初出现在 Codesai 的博客上。

* * *

##### 脚注:

[1]参见凯文·卢瑟福的伟大文章[代码气味的问题](https://silkandspinach.net/2012/09/03/the-problem-with-code-smells/)。

这解释了 Jim Weirich 为他的演讲选择的标题:[软件设计的大统一理论](https://www.youtube.com/watch?time_continue=2890&v=NLT7Qcn_PmI)和[模块化的构建模块](https://www.youtube.com/watch?v=l780SYuz9DI)。

[3]参见网络研讨会[conn asence:如何测量耦合](https://www.youtube.com/watch?v=L727roWRfFg)，作者 [Nick Hodges](https://www.linkedin.com/in/nickhodges) 。

[4]再次参见凯文·卢瑟福的伟大文章[代码气味的问题](https://silkandspinach.net/2012/09/03/the-problem-with-code-smells/)。

【5】耦合和内聚的概念可能很难把握，就看这场关于它们的争论
[理解耦合和内聚 hangout](https://www.youtube.com/watch?v=hd0v72pD1MI) 。

【6】参见凯文·卢瑟福的帖子[佩奇-琼斯重构算法](https://silkandspinach.net/2016/06/09/the-page-jones-refactoring-algorithm/)。

【7】尽管他用了**度**这个词，其实他说的是**力**。

* * *

##### 参考文献。

*书籍。*

*   [UML 中面向对象设计的基础](https://www.amazon.com/Fundamentals-Object-Oriented-Design-Meilir-Page-Jones/dp/020169946X/ref=asap_bc?ie=UTF8)，梅里尔·佩奇-琼斯
*   每个程序员应该知道的关于面向对象设计的知识
*   [结构化设计:计算机程序和系统设计学科基础](http://www.goodreads.com/book/show/946145.Structured_Design)作者[爱德华·尤顿](https://en.wikipedia.org/wiki/Edward_Yourdon)和[拉里·l·康斯坦丁](https://en.wikipedia.org/wiki/Larry_Constantine)

*论文。*

*   [通过封装和共生的方式比较技术](http://wiki.cfcl.com/pub/Projects/Connascence/Resources/p147-page-jones.pdf)

*会谈。*

*   [模块化的积木](https://www.youtube.com/watch?v=l780SYuz9DI)，吉姆·威里奇([幻灯片](http://www.slideshare.net/LittleBIGRuby/the-building-blocks-of-modularity)
*   [软件设计的大统一理论](https://www.youtube.com/watch?time_continue=2890&v=NLT7Qcn_PmI)，吉姆·威里奇([幻灯片](https://github.com/jimweirich/presentation_connascence/blob/master/Connascence.key.pdf)
*   [相关性检验](https://yow.eventer.com/yow-2012-1012/connascence-examined-by-jim-weirich-1273)，吉姆·威里奇
*   [共现性检查(较新版本，它进入了各种共现性的相当多的细节)](https://www.youtube.com/watch?v=HQXVKHoUQxY)，吉姆·威里奇([幻灯片](https://github.com/jimweirich/presentation-connascence-examined/blob/master/pdf/connascence_examined.key.pdf)
*   [了解耦合和内聚 hangout](https://www.youtube.com/watch?v=hd0v72pD1MI) ，Corey Haines，Curtis Cooley，Dale Emery，J. B. Rainsberger，Jim Weirich，Kent Beck，Nat Pryce，Ron Jeffries。
*   [红色、绿色、...现在怎么办？！](https://www.youtube.com/watch?v=fSr8LDcb0Y0%EF%BB%BF)、凯文·卢瑟福([幻灯片](http://xpsurgery.eu/resources/connascence-hunt-slides/)
*   [关联](https://dev.to/2016/12/charla-sobre-connascence-en-scbcn16)，弗兰·雷耶斯 y 阿尔弗雷多·卡萨多([幻灯片](http://slides.com/franreyesperdomo/connascence#/)
*   [相关性:如何测量耦合](https://www.youtube.com/watch?v=L727roWRfFg)，Nick Hodges

*帖子。*

*   Gregory Brown 的《作为软件设计度量标准的一致性》。
*   代码气味的问题凯文·卢瑟福
*   原始痴迷的问题凯文·卢瑟福
*   [佩奇-琼斯重构算法](https://silkandspinach.net/2016/06/09/the-page-jones-refactoring-algorithm/)，凯文·卢瑟福
*   一些例子，马克·李约瑟

*其他。*

*   连接。我
*   [维基百科上的关联](https://en.wikipedia.org/wiki/Connascence_(computer_programming))******