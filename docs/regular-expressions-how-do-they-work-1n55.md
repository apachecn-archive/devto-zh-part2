# 正则表达式:它们是如何工作的？

> 原文：<https://dev.to/katafrakt/regular-expressions-how-do-they-work-1n55>

这篇文章最初发布在我的个人博客 上 **[。](http://katafrakt.me/2016/07/06/regular-expressions/)**

* * *

亲爱的程序员！我想是时候谈谈正则表达式了。你怎么想呢?

是的，我知道你知道它们，并且可以用在最常见的现实生活中。太棒了！我认识一些程序员，对他们来说，更复杂的正则表达式是一种魔法。即使他们可能有好几年的经验。但是我不想告诉你如何构建一个正则表达式 <sup id="fnref1">[1](#fn1)</sup> 来解决你的问题。我打算解释它们如何在内部工作，以及它可能对我们产生什么样的后果。

[![xkcd regex comic](img/07fe2d9022e1e48b8f42f4594175f3b6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ItGRX60K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://imgs.xkcd.com/comics/regular_expressions.png)

那么，我们从哪里开始？它实际上始于数学。特别是，有一个数学结构叫做**有限自动机**。尽管它的正式定义听起来很可怕，但它是一个非常简单的想法。而且很容易形象化，不用担心。

有限自动机是一组**状态**(俗称 *q1，q2，q3* 等。)，由**转场**连接。后者就像有向图中的边:它们是单向的(因此用箭头来表示)，并且有一个标签。其中一个(且仅一个)状态被标记为初始或开始(通常也称为 *q0* )，至少一个是*最终*或*接受*状态。

我们说自动机是建立在一个**字母表**之上的，这是一组允许在转换标签中使用的字符。我们还有一个**输入**，它只是一个自动机在其上运行的字符数组。这是什么意思？

我们从初始状态开始。我们从输入中取出第一个字符，并找到一个由该字符标记的转换。我们跟随转换，将我们的**当前状态**改变为它结束时的状态(这可能是相同的状态)。然后我们选择另一个角色，做同样的事情。重复，直到输入结束。(我提到过它必须有有限的长度吗？嗯，必须的。)

结果可能是以下情况之一:

*   我们在其中一个接受州结束。很好。这意味着自动机**接受了**输入。
*   我们最终处于一种不被接受的状态。这意味着我们的自动机不接受输入。
*   在某一点上，我们没有找到由当前处理的字符标记的转换。这不是一个例外，只是意味着输入也没有被接受。显然，如果输入的字符不在自动机的字母表中，它就不会被接受。

对于正则表达式，我们获取它们的定义(例如`/abc/`)，然后构建一个自动机，并通过它运行一个字符串。

## 例子

我们来看一些正则表达式的有限自动机。

[![](img/9f1088ff06e53c24fa3502405a84dddf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FOHMOluh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://katafrakt.me/assets/regexp/001-simples.png)

在这个非常简单的例子中，我们从`q0`状态开始。如果我们的输入是`"a"`，我们可以通过唯一的转移到`q1`。输入被使用，我们处于接受状态(用双圈表示)，所以字符串匹配正则表达式。

注意，对于字符串`"aa"`,在消耗完第一个字符后，我们还有一个`a`。但是没有从`q1`传出的转换，所以我们不能接受这个字符串。这可能与你从编程中学到的东西相违背，但实际上`/a/`正则表达式实际上是`/\Aa\z/`。我们将在接下来的文章中这样对待他们。

同时，让我们看看更多的例子。

[![](img/1c40819bce55f81545fedcb298dfc790.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RUT9x3dy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://katafrakt.me/assets/regexp/002-simple_loop.png)

[![](img/0d372da61855129e69ad190c57596391.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gv1JQNKz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://katafrakt.me/assets/regexp/003-simple_asterisk.png)

[![](img/3c537f38a3e71a24bcb7338398926651.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pCCWpGqq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://katafrakt.me/assets/regexp/004-ranges.png)

## ε跃迁

最后一个例子稍微复杂一点，引入了我们还没有谈到的东西:**ε跃迁**。这是一个方便的机制，让你的图/自动机更简单，更容易推理。它们是什么？

ε转换是在不消耗任何输入字符的情况下完成的。所以，如果我们在`q1`，就可以“免费”去`q2`。

听起来很蠢？对我来说是的。这意味着在消耗了上一个例子中的`a`(`/a[bcd]*/`)之后，我们都处于状态`q1`和`q2` <sup id="fnref2">[2](#fn2)</sup> 。幸运的是，有一个简单的算法可以消除ε跃迁。它创造了更“精确”的自动机，但不太美观，也更难阅读。使用ε转换从正则表达式构建一些自动机通常也更容易。

这里是没有ε跃迁的等价自动机:

[![](img/8884d49ebbbcb4ed344d9ce9f380cc2a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aMRJqy_p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://katafrakt.me/assets/regexp/005-ranges-without-epsilon.png)

关于算法我就不赘述了，但主要思想是:

对于每一个跃迁，当它导致状态，ε跃迁起源于哪个状态时，用ε跃迁的目标状态的跃迁来代替它。然后移除ε跃迁。重复直到没有ε跃迁。然后删除孤立的状态(没有进出转换)。

有时候比这要难一点，但是我们在这里不会用到很多ε跃迁，所以我们不需要专注于此。

## 确定性与否？

到目前为止，我们的自动机相当简单。你可能已经思考过一两次如何实现它们。然而，我们没有涵盖正则表达式的一个真正重要的部分- **交替**。考虑一个正则表达式:`/ab(cd|ce)*/`。当然，这可以用更聪明的方式来写(`/ab(c[de])*/`)，但是我们没有太多的选择。正则表达式是有效的，所以我们必须为它创建一个自动机:

[![](img/f929700c1606f40a6497d5e0e76800e6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FqC0mDS0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://katafrakt.me/assets/regexp/006-alternation1.png)

当我们处于状态`q2`并且输入端有`c`时，问题就出现了。我们应该去`q3`还是`q5`？作为人类，我们可以向前看并清楚地看到它，然而这种奢侈是自动机所不具备的。我们总是需要在消耗一个角色的那一刻改变状态。所以在实现中，我们需要同时处于许多状态。虽然这并不坏，但实际上，对于较大的自动机来说，它可能会有点慢。我们能做得更好吗？

举个问题:**我们的自动机是非确定性的**。它的定义是它不是一个确定性的自动机。___.).那么后者是什么呢？来自[维基百科](https://en.wikipedia.org/wiki/Nondeterministic_finite_automaton):

> *   Each transition is uniquely determined by its source state and input symbol, and each state transition
> 
> 都需要*   Read an input symbol

所以，我们不能有ε跃迁，对于每个状态，只有一个给定标签的跃迁可以来自。简单。我们开始吧。

## NFA 的确定化

出于解释的目的，我将确定以下自动机:

[![](img/6bf6c608fbabca8810b55bb3c538abe6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7HDEFtQW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://katafrakt.me/assets/regexp/007-non-determ1.png)

您可能会立即注意到，当我们处于`q1`状态并且在输入中有一个字符`a`时，问题就出现了。我们正在转换到哪个状态？嗯，根据非确定性自动机的非正式定义，我们同时处于三种状态:`q2`、`q4`和`q6`。

确定化过程利用了这个定义，并通过创建一个表示它的新状态使它变得明显。为方便起见，我们称之为`q2,q4,q6`。我们通过`a`创建一个从`q1`到新状态的转换，并删除所有其他从`q1`开始的`a`标记的转换。这是我们的自动机如何处理第一步:

[![](img/b41500f67283cf76765cedaaa6d624c5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--T5k9m8Pi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://katafrakt.me/assets/regexp/008-non-determ2.png)

如你所见，它现在有点坏了。我们需要照顾分离的部分，从`q2`、`q6`和`q4`开始。这就是我们的命名惯例发挥作用的地方。从`q2`开始，我们将获取它的所有转换，并将它们附加到包括`q2`在内的所有“设置状态”(在这种情况下，它是我们新的大状态)。我们通过`b`创建到`q3`的转换。

然后我们对其他分离的国家也这样做。从`q4`我们可以经由`b`到达`q5`，所以现在我们创建一个经由`b`从`q2,q4,q6`到`q5`的过渡。对于`q6`——通过`b`我们可以到达`q7`，但是我们已经有了一个由`b`标记的转换(到`q3`)。为了解决这个冲突，我们创建另一个“设置状态”`q3,q7`，而不是`q3`。

另一件重要的事情是:接受国。我们新创建的`q3,q7`状态也应该接受。这也来自非正式的 NFA 定义:如果我们处于多个状态，但其中至少有一个正在接受，那么自动机接受到目前为止所消耗的输入。

[![](img/9cca8139b744a60157143dd69258fdc6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6-3JYvEY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://katafrakt.me/assets/regexp/009-non-determ3.png)

只剩下一个分离状态，我们必须重复我们在上一步中所做的:获取它，找到所有包含`q7`(在本例中只有`q3,q7`)的“设置状态”并重写转换。

[![](img/470c245e07d6d67b6c97cfc12d3b2044.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6wlKh2Bn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://katafrakt.me/assets/regexp/010-non-determ4.png)

我们现在可以享受我们最初的非确定性自动机的确定性等价物。它变得简单了一点(7 个状态而不是 9 个，6 个转换而不是 9 个)。通过逆向工程将其转化为正则表达式，我们还有一个更简单的表达式！NFA 的 regexp 是`/a(ab|aac|abc)/`，我们 DFA 的 regexp 是`/aa(bc?|ac)/`。

这并不总是那么明显，有时确定性自动机比它们的非确定性对应物更复杂。

## 完成自动机

如果你了解自动机理论，特别是如果你是一名数学家，你可能会非常生气，几乎会滚动到评论告诉我，以上是**而不是**确定性有限自动机。实际上，你是对的。我漏掉了一件事，但你很快就会明白为什么。

确定性自动机的正式定义要求它是**完全的**。这是什么意思？对于字母表中的每个状态和每个字符，应该有一个从状态开始并由字符标记的转换。这意味着我们需要进行大量的过渡，但是作为一个额外的奖励，我们永远不会陷入这样一种情况:我们没有消耗完所有的输入，但是由于没有匹配的过渡，我们无法继续。

这是通过引入一个额外的状态实现的。我们习惯称之为“地狱”。有了它，假设我们的字母表是简单的`{a,b,c,d}`，我们可以使我们的 DFA 完整。像这样:

[![](img/0c17579808e200f309094009934bf840.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--leibgOL3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://katafrakt.me/assets/regexp/011-non-determ-with-hell.png)

所以，现在它不可读，尽管我用简洁的方式画了出来。然而，对于编程中的正则表达式，我们通常希望我们的字母表全部由 unicode 字符组成。而且有很多。我是说，很多。这就是为什么我们通常不想创建完整的自动机。

## 我们到了

目前基本上就是这样。确定性自动机是正则表达式编译的结果。现在我们可以向它扔一些字符串，它会接受或不接受它们。我也计划放一个示例实现，但是这篇文章已经很长了，所以我将它安排在第 2 部分。关于我们到目前为止所做的一些最后说明:

*   我们现在知道如何为包含一些基本特征的正则表达式构造自动机，比如`*`、`+`、`()`、`|`。我没有提到它，但是也有可能有`{n}`、`{n,}`、`{n,m}`和`?`。
*   我们不知道如何处理范围和组。此外，如何处理一个点(匹配任何字符)。从理论角度来看，这些实际上是无关紧要的实现细节。尤其是 dot，当我们希望它匹配任何 unicode 字符时，需要一些技巧，但我们现在不会谈论它。
*   我们不能使用[反向引用](http://www.regular-expressions.info/backref.html)、前瞻或类似功能。即使更强，我们也无法使用它们，因为不可能创造有限自动机来处理它们。你需要不同的理论设备(我认为是带栈的自动机)，但那是完全不同的故事...
*   以上所有的例子实际上都是`/\Aabc\z/`，而不仅仅是`/abc/`。您必须创建一组所有的子字符串来运行自动机，并检查是否有任何匹配。这不方便，但也是为什么你**应该尽可能使用`\A`和`\z`**，因为这样会更快。
*   创建一个自动机并随后确定它的成本很高，相比之下，只是针对输入运行它。如果你不确定你的语言会在内部优化它，你应该总是在任何循环之外编译一个正则表达式。就是即时免费优化。现在你知道为什么了。
*   另外，如果您正在编写一些性能关键的代码，您应该首先考虑使用 regexp 之外的东西。检查一个字符串是否是另一个字符串的子串比用正则表达式检查要快得多。所以从现在开始不要再这样了，好吗？

[![](img/5b116da0a1b1819817c5cadd4bab8b80.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vuLGV29L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.meme.am/instances/63754152.jpg)

## 脚注

* * *

1.  “Regex”和“regexp”在互联网上似乎可以互换使用。我自己更喜欢后者，也将使用它，但这是一回事。 [↩](#fnref1)

2.  我认为引入它们是为了帮助自动机连接的数学证明。不过，我不确定。 [↩](#fnref2)