# 用漫画理解 JavaScript 的引擎

> 原文：<https://dev.to/howtocodejs/explain-javascripts-engine-like-im-five-3loj>

* * *

*最初由 Raji Ayinla 在 codeburst.io 上发表，他知道为 howtocodejs.com 开发内容。想以有趣的方式学习 JavaScript 吗？那就来 howtocodejs.com 吧。

## 概述

JavaScript 被编译。是的，你没看错。不过，与其他语言编译器不同，JavaScript 编译器的编译阶段允许早期优化，它被迫在最后一秒编译代码。用于编译 JavaScript 的技术被恰当地命名为实时(JIT)。这种“动态编译”已经出现在现代 JavaScript 引擎中，以加速实现它们的浏览器。

当开发人员将 JavaScript 称为解释型语言时，可能会有点混乱。这是因为直到最近，JavaScript 引擎总是与解释器联系在一起。现在，有了像 Google 的 V8 引擎这样的引擎，开发者可以鱼和熊掌兼得——一个引擎可以同时拥有解释器和编译器。

我们将向您展示如何使用这些新式的 JIT 编译器来处理 JavaScript 代码。我们不会向您展示这些新 JavaScript 引擎优化代码的复杂机制。这些机制包括内联(删除空白)、利用隐藏类和消除冗余等技术。相反，本文将略述编译理论的广泛概念，让您了解 JavaScript 的现代引擎如何在内部工作。

声明:你可能会成为一名严格的素食主义者。

## 语言和代码

[![racers](../Images/198d7a6af742be9d8ad5deb8c700a495.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ci-ah7_n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/06gh7f6gezcdyvw7vkj1.jpg)

为了探究编译器是如何读取代码的，考虑一下你正在使用哪种语言来阅读这篇文章是很有帮助的:英语。我们在开发控制台中都遇到过醒目的红色语法错误，但是当我们绞尽脑汁寻找丢失的分号时，我们可能从未停下来想想诺姆·乔姆斯基。乔姆斯基将语法定义为:

> "对特定语言中句子构造的原则和过程的研究."

我们将根据诺姆·乔姆斯基的定义调用我们的“内置”函数。

```
simplify(quote, "grossly");

//Result: Languages order their words differently. 
```

Enter fullscreen mode Exit fullscreen mode

当然，乔姆斯基指的是像德语和斯瓦希里语这样的语言，而不是 JavaScript 和 Ruby。然而，高级编程语言是模仿我们说的语言。本质上，JavaScript 编译器已经被精明的工程师“教会”阅读 JavaScript，就像我们的父母和老师训练我们的大脑阅读句子一样。

在语言学研究中，我们可以观察到三个与编译器相关的领域:词汇单元、句法和语义。换句话说，研究单词的意义及其关系，研究单词的排列，研究句子的意义(我们限制了语义的定义以适应我们的目的)。

就拿这句话来说:我们吃了牛肉。

### 词汇单位

注意句子中的每个单词是如何被分解成词汇意义单位的:We/ate/beef

### 语法

那个基本句子在句法上遵循主语/动词/宾语的一致。让我们假设每个英语句子都必须这样构成。为什么？因为编译器必须按照严格的准则工作，以便检测语法错误。所以，我们吃的牛肉，虽然可以理解，但在我们过于简单的英语中是不正确的。

### 语义

从语义上说，这个句子有恰当的含义。我们知道很多人过去都吃过牛肉。我们可以把这个句子改写成，We+beef eat，来去掉它的意思。

* * *

现在，让我们把原来的英语句子翻译成 JavaScript 表达式。

```
let sentence = "We ate beef"; 
```

Enter fullscreen mode Exit fullscreen mode

### 词汇单位

表达可以分解成词位:let/sentence/=/“我们吃了牛肉”/；

### 语法

我们的表达，就像一个句子，必须符合句法。JavaScript 和大多数其他编程语言一样，遵循(类型)/变量/赋值/值的顺序。基于上下文，类型是适用的。如果你和我们一样被类型声明的松散所困扰，你可以简单地加上“使用严格的”；程序的全局范围。“使用严格”；是一个强制 JavaScript 语法的专横的语法学家。使用它的好处大于它带来的麻烦。相信我们。

### 语义

从语义上讲，我们的代码具有我们的机器最终将通过编译器理解的含义。为了从代码中获得语义，编译器必须读取代码。我们将在下一节深入探讨这个问题。注意:上下文不同于范围。进一步解释将超出本文的“范围”。

## [T1】LHS/RHS](#lhsrhs)

我们从左向右阅读英语，而编译器从两个方向阅读代码。怎么会？具有左侧(LHS)查找和右侧(RHS)查找。让我们把它们分解开来。

LHS 查找焦点是作业的“左手边”。这真正的意思是，它对任务的目标负责。我们应该概念化目标而不是位置，因为 LHS 查找的目标可以在它的位置上变化。此外，赋值并不显式引用赋值运算符。

请看下面的例子以获得澄清:\

```
function square(a){
    return a*a;
}
square(5); 
```

Enter fullscreen mode Exit fullscreen mode

函数调用触发了对 a 的 LHS 查找，为什么？因为将 5 作为参数传递隐式地给 a 赋值.注意目标是如何不能通过定位一眼就确定的，必须推断。

相反，RHS 查找侧重于值本身。因此，如果我们回到前面的例子，RHS 查找将在表达式 a*a 中找到 a 的值；重要的是要记住，这些查找发生在编译的最后阶段，即代码生成阶段。一旦我们到达那个阶段，我们将进一步详细说明。现在，让我们探索一下编译器。

## 编译器

[![compiler](../Images/454bee6936dec7405408bb9ddba7eccf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Vkt7m6hT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9dnpzcc6jr12f7h6oxb7.png)

把编译器想象成一个肉类加工厂，有几个机制把代码磨成我们的计算机认为可食用或可执行的包。在这个例子中，我们将处理表达式。

### 令牌

[![tokenizer](../Images/51e49ede7ff5cf86675ce869933b5679.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WvG1B7fc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n4ma6mnv2krffgpl9sqp.jpg)

首先，记号赋予器将代码分解成称为记号的单元。

然后，这些记号由记号赋予器识别。当记号赋予器发现不属于该语言的“字母表”时，将发生词法错误。记住，这不同于语法错误。例如，如果我们使用@符号而不是赋值操作符，标记器会看到那个@符号并说，“嗯……这个词位在 JavaScript 的词典中找不到……关闭所有东西。红色代码。”

注意:如果同一个系统能够在一个标记和另一个标记之间建立关联，然后像解析器一样将它们组合在一起，那么它将被认为是一个词法分析器。

[![lexer](../Images/b904f177773bbd7c62dfc877a166308f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--i7LHlNa8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/spkdlfndrudbvebkqvcv.jpg)

### 解析器

[![parser](../Images/7a5ad60bb3b8d4298430923af009d838.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--X7GTiW5n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7q6mrviwtgdw7wtaqp1h.jpg)

解析器寻找语法错误。如果没有错误，它会将令牌打包到一个称为解析树的数据结构中。在编译过程的这一点上，JavaScript 代码被认为是被解析的，然后进行语义分析。同样，如果遵循 JavaScript 的规则，就会产生一种称为抽象语法树(AST)的新数据结构。

* * *

有一个中间步骤，由解释器一条一条地将源代码转换成中间代码——通常是字节码。然后，字节码在虚拟机中执行。
之后，对代码进行优化。这包括删除空白、死代码和冗余代码，以及许多其他优化过程。

* * *

### 代码生成器

一旦代码被优化，代码生成器的工作就是将中间代码转换成机器容易理解的低级汇编语言。此时，发电机负责:

(1)确保低级代码保留与源代码相同的指令
(2)将字节码映射到目标机器
(3)决定值应该存储在寄存器还是存储器中，以及应该在哪里检索值。

* * *

这是代码生成器执行 LHS 和 RHS 查找的地方。简单地说，LHS 查找将目标值写入内存，而 RHS 查找从内存中读取值。

如果一个值同时存储在缓存和寄存器中，生成器必须通过从寄存器中取值来进行优化。从内存中取值应该是最不可取的方法。

* * *

最后…

(4)决定指令执行的顺序。

## 最后的想法

理解 JavaScript 引擎的另一个方法是看看你的大脑。当你读这篇文章的时候，你的大脑正在从你的视网膜获取数据。这个数据，由你的视神经传输，是这个网页的反转版本。你的大脑通过翻转图像来编辑图像，使其可以被理解。除了翻转图像和给它们着色，你的大脑还可以根据其识别模式的能力来填充空白，就像编译器从缓存中读取值的能力一样。

所以如果我们写，请访问我们的网站 ______，你应该很容易就能执行那段代码。

哦，向莱克斯问好。我们内置的交互式 JavaScript 编辑器。

[![lex](../Images/86cc04a38b6615f1887179f801094af0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OsHMJVba--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/os529whhjlfba8kdfw46.png)

* * *

## 资源

詹姆斯·艾伦·法雷尔的《编译器剖析》
你不知道 JS 第 1 章【JavaScript 如何工作
编译器设计