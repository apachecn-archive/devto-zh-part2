# web 汇编中的 Elm 函数

> 原文：<https://dev.to/briancarroll/elm-functions-in-webassembly-50ak>

在过去的几个月里，我一直非常着迷于试图理解 [Elm](http://elm-lang.org/) 编译器将来如何能够以 [WebAssembly](https://webassembly.org/) 为目标。生成 JavaScript 的主要区别是什么？什么是困难的部分，什么方法是有意义的？

我觉得最有意思的一个问题是:如何在 WebAssembly 中实现一流的功能？JavaScript 内置了它们，但是 WebAssembly 没有。将函数视为值是一种相当高层次的抽象，而 WebAssembly 是一种非常低级的语言。

**内容**
[榆树和 WebAssembly](#Elm-and-WebAssembly)
[榆树的一级函数](#Elms-first-class-functions)
[关键 WebAssembly 概念](#Key-WebAssembly-concepts)
[表示闭包为字节](#Representing-closures-as-bytes)
[函数应用](#Function-application)
[词法闭包](#Lexical-closure)
[代码生成](#Code-generation)
[总结](#Summary)
[下一步是什么？](#Whats-next)
[参考文献](#References)

## 榆树和 web 汇编

在我们开始之前，我想提一下，根据我从核心团队那里听到的，有一个普遍的预期是 Elm 有一天会编译成 WebAssembly，但是目前还没有具体的计划。WebAssembly 仍然是一个 MVP，直到它有了垃圾收集，并且可能还能访问 DOM 和其他 Web APIs，它才真正为 Elm 做好准备。GC 扩展仍在[“功能提案”](https://github.com/WebAssembly/design/issues/1079)阶段，所以它还需要一段时间才可用。

但是...它将在某个时候发布，WebAssembly 是社区成员的建议研究项目之一，嗯，这真的很有趣！因此，让我们来想想 WebAssembly 中的 Elm 会是什么样子！

现在...如何用 WebAssembly 这样的低级语言实现一流的功能？WebAssembly 都只是低级的机器指令，机器指令不是你可以“传来传去”的东西！而部分功能应用呢？难道没有关于从函数范围之外“封闭”值的东西吗？

我们来分析一下。
T2】

## 榆树的一流功能

让我们从查看一些 Elm 示例代码开始，然后列出我们需要实现的 Elm 函数的所有特性。

```
module ElmFunctionsDemo exposing (..)

outerFunc : Int -> (Int -> Int -> Int)
outerFunc closedOver =
    let
        innerFunc arg1 arg2 =
            closedOver + arg1 + arg2
    in
        innerFunc

myClosure : Int -> Int -> Int
myClosure =
    outerFunc 1

curried : Int -> Int
curried =
    myClosure 2

higherOrder : (Int -> Int) -> Int -> Int
higherOrder function value =
    function value

answer : Int
answer =
    higherOrder curried 3 
```

Enter fullscreen mode Exit fullscreen mode

如果你想知道，`answer`是 1+2+3=6。这肯定不是编写这个计算的最简单的方法，但是它确实说明了 Elm 函数的所有最重要的特性！

### 三个关键特征

首先，Elm 函数是一级的，这意味着它们是可以从其他函数(如`outerFunc`)返回并传递给其他函数(如`higherOrder`)的*值*。

其次，它们支持*词法闭包*。`innerFunc`“捕获”来自其父范围的值，称为`closedOver`。这意味着`myClosure`“记住”了创建它时使用的`closedOver`的值，在本例中是`1`。

最后，Elm 函数支持*局部应用*。`myClosure`是一个带两个参数的函数，但是`curried`的主体，我们只对它应用一个参数。结果，我们得到了一个新函数，它在实际运行之前还在等待另一个参数。这个新功能“记住”部分应用的值，以及关闭的值。

### 代码中的线索

请注意，我们现在有几个 Elm 函数，当它们被实际执行时，它们都将最终执行同一行代码！就是这个表情:

`closedOver + arg1 + arg2`

如果有人用另一个参数调用`curried`,这就是将计算返回值的表达式。如果有人用两个参数调用`myClosure`也是一样。

这给了我们一个如何开始实现它的线索。我们传递的所有函数值都需要有一个对同一个 WebAssembly 函数的*引用*，该函数计算主体表达式。

在 WebAssembly 中，我们不能传递函数，只能传递数据。但是，也许我们可以创建一个数据结构，让*代表*一个 Elm 函数值，跟踪规定的参数和封闭值。当我们最终有了所有的参数并准备好评估主体表达式时，我们可以执行一个 WebAssembly 函数来产生一个返回值。

现阶段仍缺少许多细节。为了填补空白，我们需要一些关于 WebAssembly 语言特性的背景知识。
T2】

## web assembly 的关键概念

### 线性记忆

WebAssembly 模块可以访问一块“线性内存”,用于存储和加载数据。这是一个由 32 位整数索引的线性字节数组。WebAssembly 有内置指令来存储和加载整数和浮点数，但是任何更复杂的东西都必须从原始字节开始构建。

事实上，一切都是由原始字节构建的，这意味着 WebAssembly 可以成为许多不同语言的编译目标。不同的数据结构对不同的语言有意义，但它们最终都只是字节。由每个编译器和运行时来定义如何操作这些字节。

### 表格

WebAssembly 有一个名为“表”的特性，用于实现“间接调用”。间接调用几乎是每一种高级语言的特征，但是它们是什么呢？

当机器执行一个函数调用时，它显然需要一些引用来知道要调用哪个函数。在一个*直接调用*中，函数引用是简单的硬编码，所以它每次都调用相同的函数。然而，在*间接调用*中，函数引用由运行时值提供。这是一件非常方便的事情，因为这意味着调用者不需要预先知道它可能需要调用的函数的完整列表。因此，大多数语言都有这样的版本。C 和 C++有函数指针，Java 有基于类的多态，Elm 有一级函数。

WebAssembly *表*是一个函数数组，每个函数由一个 32 位整数索引。有一个特殊的`call_indirect`指令，它获取要调用的函数的索引和一个参数列表，并执行它。程序静态声明哪些函数是表的*元素*，而`call_indirect`只作用于那些函数。(顺便提一下，还有一个直接呼叫的`call`指令，但我们现在不会太关注它。)

顺便说一句，WebAssembly 出于安全考虑有这样的设计。如果函数存储在线性内存中，代码就有可能检查或破坏其他代码，这对于 web 安全性是不利的。但是对于索引函数表，这是不可能的。唯一甚至可以访问表的指令是`call_indirect`，这是安全的。

如果你有兴趣进一步阅读，我推荐 Mozilla 关于[理解文本格式](https://developer.mozilla.org/en-US/docs/WebAssembly/Understanding_the_text_format)的文章，以及关于 [WebAssembly 语义](https://github.com/WebAssembly/design/blob/master/Semantics.md)的设计文档。

但是现在，我们已经有足够的知识来讨论如何实现一流的功能。
T2】

## 用字节表示闭包

如前所述，要在 WebAssembly 中表示一个 Elm 函数，我们需要一个函数和一个数据结构。我们将使用术语“闭包”来指代数据结构，使用“evaluator function”来指代 WebAssembly 函数，该函数将计算主体表达式并生成返回值。

下面是用二进制表示闭包的一种方式，其中每个盒子代表一个整数(4 个字节)。

| `fn_index` | `arity` | `mem_ptr0` | `mem_ptr1` | `mem_ptr2` | ... |
| --- | --- | --- | --- | --- | --- |

**`fn_index`** 是函数表中的整数索引，在函数表中可以找到这个闭包的求值函数。在运行时，一旦所有的参数都应用于闭包，我们就可以调用`call_indirect`指令来查找表，调用赋值函数，并返回结果。

**`arity`** 是要应用于闭包的剩余个参数的*。每当我们应用另一个参数时，我们插入一个指向该参数的指针，并递减 arity。当它达到零时，我们准备调用赋值函数。*

**`mem_ptr*`** 是指针，代表自变量和封闭值的线性存储器中的地址。它们都以“空”(零)开始，并在应用参数时以相反的顺序填充。因此，如果闭包的 arity 为 2，那么`mem_ptr0`和`mem_ptr1`将为“空”。当我们应用下一个参数时，`mem_ptr1`将用参数值的地址填充，`arity`将从 2 递减到 1，而`mem_ptr0`仍然为空。

## 功能应用

我们已经提到了当闭包应用于一些参数时需要发生的一些事情，但是完整的算法如下:

*   制作封口的新副本
*   对于每个应用的参数
    *   设`a`为闭包的剩余 arity
    *   将自变量的地址写入位置`a-1`处的`mem_ptr`
    *   递减 arity `a`
*   如果剩余的 arity 大于 0
    *   退回新的封口
*   其他
    *   使用`call_indirect`执行`func_index`引用的函数，将闭包作为参数传递

让我们看一个例子，将两个参数应用于 arity 2 的闭包。

在我们应用任何参数之前，这是数据结构的样子。所有的指针都被设置为零(`null`指针)。

| `fn_index` | `arity` | `mem_ptr0` | `mem_ptr1` |
| --- | --- | --- | --- |
| `123` | `2` | `null` | `null` |

在应用闭包之前，我们需要创建一个新的副本，这样旧的闭包仍然可供其他代码使用。所有的 Elm 值都是不可变的，闭包也不例外。

现在让我们应用一个论点。我们的算法说，对于 arity `2`，我们应该将参数地址放入`mem_ptr`中的位置`2-1=1`。换言之，`mem_ptr1`。让我们看看那是什么样子。

| `fn_index` | `arity` | `mem_ptr0` | `mem_ptr1` |
| --- | --- | --- | --- |
| `123` | `1` | `null` | `arg0` |

请注意，我们反向填充参数指针。这只是一个提高效率的把戏。如果我们按升序排列，我们需要知道已经应用了多少，这样我们就可以跳过它们，把下一个参数放在下一个空位置。该信息必须作为一个额外的字段存储在闭包中，占用额外的空间。

但是如果我们反向填充参数，我们只需要知道当前的 arity。如果当前的 arity 是 2，那么前两个位置是自由的，不管这是一个简单的双参数函数，还是一个已经应用了 3 个其他参数的五参数函数。

再来应用一个论点，`arg1`。和以前一样，我们将把参数的地址放入最高可用的`mem_ptr`，也就是`mem_ptr0`，并递减 arity。

| `fn_index` | `arity` | `mem_ptr0` | `mem_ptr1` |
| --- | --- | --- | --- |
| `123` | `0` | `arg1` | `arg0` |

应用了我们得到的所有参数后，我们检查剩余的 arity。如果非零，这一定是部分应用，我们可以只返回闭包。但是如果是零，那就意味着所有的参数都被应用了。在这种情况下，是时候调用赋值函数了，并返回它给我们的值。

注意，evaluator 函数将闭包结构作为唯一的参数。它包含所有必要的数据，因为这正是它的设计目的！
T2】

## 词法闭包

让我们再来看一下我们的例子，封闭外部作用域的值。

```
outerFunc : Int -> (Int -> Int -> Int)
outerFunc closedOver =
    let
        innerFunc arg1 arg2 =
            closedOver + arg1 + arg2
    in
        innerFunc 
```

Enter fullscreen mode Exit fullscreen mode

为了帮助我们思考如何为`innerFunc`生成 WebAssembly，让我们首先将源代码重构为下面的等价版本。

```
outerFunc : Int -> (Int -> Int -> Int)
outerFunc closedOver =
    let
        -- Replace inner function definition with partial application
        innerFunc =
            transformedInnerFunc closedOver
    in
        innerFunc

-- Move definition to top level, inserting a new first argument
transformedInnerFunc closedOver arg1 arg2 =
    closedOver + arg1 + arg2 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们将内部函数的定义移到了顶层，并插入了`closedOver`作为新的第一个参数，而不是实际封闭它。这对任何调用`outerFunc`的人来说没有任何区别——它仍然创建了一个`innerFunc`，它记得创建它时使用的`closedOver`的值。

最大的好处是我们不再有嵌套的函数定义。相反，它们都是在顶层定义的。这很有用，因为我们需要将所有的赋值函数放入一个全局 WebAssembly 函数表中。记住，表是 WebAssembly 支持间接函数调用的方式。所以我们需要编译器对所有嵌套的函数定义进行这种转换。
T2】

## 代码生成

我们现在准备看看编译器为 Elm 函数生成代码需要采取的步骤。

1.  生成主体表达式，跟踪主体中引用的所有*本地名称*(我们可以忽略顶级名称)。
2.  从本地名称集中，删除参数名称和任何定义的名称`let`子表达式。只有封闭的名字会保留下来。
3.  将封闭名称列表添加到函数参数列表的前面，以获得赋值函数的参数列表。
4.  生成赋值函数
5.  将赋值函数声明为函数表的一个元素
6.  将执行以下操作的代码插入父范围
    *   在内存中创建新的闭包结构
    *   部分应用来自父范围的封闭值

## 总结

在帖子的顶部，我们首先注意到将 Elm 编译成 WebAssembly 的一个有趣的挑战是如何实现一流的功能。

Elm 函数有很多 WebAssembly 中没有的高级特性。它们的行为类似于值，可以部分应用，并且可以从外部范围捕获值。

尽管 WebAssembly 本身没有这些特性，但它提供了构建这些特性的基础。WebAssembly 支持使用函数表的间接函数调用，允许我们以表索引的形式传递对 WebAssembly 函数的引用。

我们可以使用 WebAssembly 函数和数据结构来表示 Elm 函数。我们看到了数据结构的字节级表示可能是什么样子。数据结构是在程序中传递的东西，记录部分应用的参数和封闭值。它还包含赋值函数的表索引，这是最终将产生返回值的内容。

我们讨论了实现词法闭包的方法。它包括自动转换 Elm 代码，展平嵌套的函数定义，以便可以将它们插入到 WebAssembly 函数表中。这种转换将词法闭包变成了部分函数应用。

最后，我们概述了编译器的代码生成器需要采取的一些步骤，并查看了函数应用程序的运行时算法。
T2】

## 接下来是什么？

我正在开发一个原型代码生成器来证明这些想法。我正在取得合理的进展，似乎没有任何主要的障碍，但它需要更多的工作来让它工作。如果/当我走到那一步的时候，我可能会分享更多的东西！

我还想到了一些关于 WebAssembly 中 Elm 主题的博客帖子:

*   其他 Elm 数据结构的字节级表示(可扩展记录、联合类型、数字、可比较数据、可追加数据...)
*   代码生成架构(WebAssembly AST，从 Haskell 生成 Wasm 是否合理？铁锈呢？)
*   WebAssembly 中的 Elm 运行时(平台、调度程序、任务、流程、效果管理器...)
*   DOM、HTTP 和端口。Wasm MVP 和后 MVP 的区别。
*   字符串和 Unicode
*   用蹦床消除尾音

如果你想看这些，请在评论中告诉我！
T2】

## 参考文献

[Haskell 的实现闭包](https://ghc.haskell.org/trac/ghc/wiki/Commentary/Rts/Storage/HeapObjects#FunctionClosures)
[维基百科闭包文章](https://en.wikipedia.org/wiki/Closure_(computer_programming)#Implementation_and_theory)
[维基百科λ提升文章](https://en.wikipedia.org/wiki/Lambda_lifting)

感谢阅读！