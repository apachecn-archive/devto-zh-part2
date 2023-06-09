# 编程语言的形成:石板[第 1 部分]

> 原文：<https://dev.to/nektro/the-making-of-a-programming-language-slate-part-1-4528>

我创造了一种新的编程语言！(((注意:现在它只能将数字文字彼此相加并导出常量，但是它可以运行！！))

# 【第一部分】简介

# 石板！

在最近的时间里，我一直致力于开发一种全新的编程语言，它叫做 Slate！这将是一个系列，或多或少开始“结束”,因为我记录了我制作编译器、标准库，甚至可能是 Slate 中的一些程序的过程。

石板是(第一？)直接从源代码编译到 [WebAssembly](https://webassembly.org/) 的编程语言。是的，这就是为什么我问了这么久关于 WASM 的问题:)。语法主要受 JavaScript ES2015+的启发，并受到 Java、Kotlin 等的影响。

# 那么它现在能做什么呢？一种新语言？为什么？有什么不同？我能用它吗？

现在这就是它所做的一切。

```
/**
 * https://github.com/nektro/slate/blob/master/tests/02.grammar/01.operators/001.slate
 */
//
export const expected = 80;
//
export function main(): i32 {
    return 48 + 32;
} 
```

Enter fullscreen mode Exit fullscreen mode

做什么？

您可以导出整数常量文字，也可以导出将整数文字相加的函数。大概就是这样。但是`slate.js`可以完全解析它，并导出一个 WASM 模块来做同样的事情，尽管现在还很简单。

为什么要制造？

我真的很喜欢 JavaScript。这种热爱源于对作为一个整体平台的网络的更广泛的热爱，而 JS 是我们所得到的一切。直到现在！WebAssembly 回答了一个老问题“除了 JavaScript，Web 还会有其他语言吗？”。随着 WASM 获得所有的语言[1]。所以，部分出于对 JS 的热爱，部分出于想制作自己的语言的愿望，因为，为了尝试实现我从未见过的功能，我着手通过 WASM 制作一种专门针对 Web 的语言。

*【1】:前提是前述语言有合适的工具链*

通过这个系列，我将或多或少地记录整个旅程。

有多不同？

Slate 是强类型的。这是不同的一点。但是我还想添加一些东西，比如操作符重载、对象扩展(比如添加到`<Object>.prototype`上，但是使用静态类型的语言)等等。

我能用它吗？

技术上说是的！如果你想编译上面的程序，并在你自己的支持 WebAssembly 的浏览器中运行它，你可以这样做:

```
import * as slate from "https://rawgit.com/nektro/slate/master/src/slate.js"

const slate_program = `
/**
 *
 */
//
export const expected = 80;
//
export function main(): i32 {
    return 48 + 32;
}
`;

Promise.resolve(slate_program)
.then(x => slate.parse(x))
.then(x => x.instance)
.then(x => x.exports)
.then(x => {
    // `x` == { expected: 80, main: func() { [native code] } }
}); 
```

Enter fullscreen mode Exit fullscreen mode

# 你是怎么做到的？

像任何其他语言一样，在制作编译器和所有发生在 Slate 中的步骤之间有许多相似之处。

*   词汇分析
*   句法分析程序
*   语义分析程序
*   代码生成
*   连接物

## 1。词汇分析

这一步很简单，因为当我制作一个 HTML 预处理器并添加到我的[玄武岩](https://github.com/nektro/basalt/blob/master/src/lex.js) javascript 库时，用于这一部分的大部分代码已经写好了[。Slate 的 lexer 的代码可以在这里](https://dev.to/nektro/how-i-accidentally-wrote-an-awesome-html-preprocessor-995)找到[。](https://github.com/nektro/slate/blob/master/src/lexer.js)

我们的 lexer 将获取我们程序的文本，并为我们做一些非常方便的事情。它必须删除注释，并将代码转换成带有数据的标记列表，我们可以稍后将这些数据传递给解析器。

因此，正确设置 lexer 后，玄武岩将把我们的测试程序变成类似下面的代码。

```
[
    Key("export"),
    Key("const"),
    Id("expected"),
    Symbol(=),
    Int(80),
    Symbol(;),
    Key("export"),
    Key("function"),
    Id("main"),
    Symbol("("),
    Symbol(")"),
    Symbol(":"),
    Id("i32"),
    Symbol("{"),
    Key("return"),
    Int(48),
    Symbol("+"),
    Int(32),
    Symbol(";"),
    Symbol("}")
] 
```

Enter fullscreen mode Exit fullscreen mode

## 2。句法分析程序

当我制作 HTML 预处理器时，这部分过程也非常复杂，所以解析也是玄武岩中的[模块。玄武岩帮助我们建立一个解析器，但我们仍然需要添加所有的魔法。Slate 的解析器](https://github.com/nektro/basalt/blob/master/src/parse.js)[在这里](https://github.com/nektro/slate/blob/master/src/parser.js)。熟悉这里的计算机科学的人，我们正试图通过伪[上下文无关语法](https://en.wikipedia.org/wiki/Context-free_grammar)来创建一种[形式语言](https://en.wikipedia.org/wiki/Formal_language)。ANTLR 是这个领域的另一个大项目，它以一种更类似于[Backus–Naur 形式](https://en.wikipedia.org/wiki/Backus%E2%80%93Naur_form)的格式创建一个词法分析器/解析器。

简而言之，我们必须想出一系列模式，这些模式可以从之前的令牌列表中提取并压缩成一个单一的表达式，然后我们可以分析它来创建我们的程序。

在这个过程之后，我们的测试程序看起来更像这样:

*注意:我跳过了代码演示部分，因为解析器的输出非常冗长，下一步我们将把它压缩一点，以更有用的格式显示同样的信息*

## 3。语义分析程序

这一部分由“转换器”在 Slate 中完成，它从解析器获取非常详细的输出，验证它，并生成 [AST](https://en.wikipedia.org/wiki/Abstract_syntax_tree) 。石板转换器的来源可以在这里找到[。](https://github.com/nektro/slate/blob/master/src/converter.js)

那么现在我们的程序看起来像什么？

```
File(
    Export(
        Const(
            "expected"
            80
        )
    )
    Export(
        Function(
            "main"
            Parameters(
            )
            "i32"
            Block(
                Return(
                    Add(
                        48
                        32
                    )
                )
            )
        )
    )
) 
```

Enter fullscreen mode Exit fullscreen mode

## 4。代码生成

咻！快到了！在这一点上，我们有了一个很好的 AST，但是现在需要编译成 WebAssembly，这样它就可以由`WebAssembly.instantiateStreaming()`等运行。因为我想让这个*小*对我来说更容易，我决定让我的编译器生成[文本格式](https://webassembly.org/docs/text-format/)的 WASM，而不是[二进制格式](https://webassembly.org/docs/binary-encoding/)，然后使用 [wabt](https://github.com/WebAssembly/wabt/) 将文本转换成二进制 WASM。相信我，我喜欢 WebAssembly 和它所代表的东西，但是即使试图弄清楚文本格式也很困难。目前很少有关于格式的文档，我所做的大部分都是 WASM 平台规范测试和来自 WASM 各个游乐场的输出。

从我们的 AST 生成 WAST 的代码实际上是附加在从转换器发出的对象上的，所以代码[在这里是](https://github.com/nektro/slate/blob/master/src/objects.js)。代后说，我们应该得到以下:

```
(module
    (memory $0 1)
    (global i32 (i32.const 80) )
    (export "expected" (global 0) )
    (func (export "main") (result i32)
        (i32.add (i32.const 48) (i32.const 32) )
    )
) 
```

Enter fullscreen mode Exit fullscreen mode

万岁！🙌

## 5 个。左边！左边

现在我们已经完成了。导入目前还没有实现，也没有标准库，所以这个阶段将不得不推迟。

* * *

感谢阅读！如果你喜欢这个，让我知道你想在 Slate 的未来看到什么，并继续关注更多！

在以后的文章中:

*   设计理念和长期目标
*   更多操作员
*   类型推断添加对浮点数和对象的支持
*   变量
*   `if`、`while`、`for`等
*   用线串
*   更多功能
*   班级
*   元编程

* * *

为您保存卷轴的链接:

在 GitHub 上关注 Slate！[https://github.com/nektro/slate](https://github.com/nektro/slate)
在推特上关注我！[https://twitter.com/nektro](https://twitter.com/nektro)
跟我上 Dev！[https://dev.to/nektro](https://dev.to/nektro)