# 类型脚本与流程

> 原文：<https://dev.to/stereobooster/typescript-vs-flow-1d7e>

# 打字稿 vs 流

这不是严格的比较，更像是反思。对我来说，Flow 和 TypeScript 差不多，都不理想，但比无类型 JavaScript 好。它们在许多方面是不同的，但是对于简单的用例来说是相似的。

我正在用 Redux、类型系统、有限状态机、副作用等做一个[小实验。我想试用 io-ts，它有 TypeScript 的类型定义(实际上是用 ts 编写的)，但没有 Flow 的类型定义(它们在 v1 之前就被否决了)。可以更新旧的流定义，但是我决定简单地用 TypeScript 重写应用程序。这篇文章基本上是对这个过程的反思。](https://dev.to/stereobooster/pragmatic-types-how-to-turn-redux-to-finite-state-machine-with-the-help-of-types-5f08)

## react-scripts-ts

react-scripts-ts 带有 tslint 而不是 eslint，并且它的配置是公开的，这与 Create React App 的理念相违背，因为它应该是零配置设置。

默认的 tslint 配置过于固执己见。它让我按字母顺序排列属性。说真的，谁有时间做这个？我希望 linter 指出 footguns，它应该有助于不要让我做更多的工作。我喜欢 CRA 的方法，eslint 指出了一些明显的错误，格式留给更漂亮的人。

Linter 错误显示为错误，而不是警告。有时那些错误会“卡住”——我进入一个错误状态，然后我修复它，但是错误不会消失，直到我重新启动项目。我不得不禁用大多数 tslint 配置，这是一次不愉快的经历。我不想花时间配置它，我只想做一些原型。

看起来，但我没有实际测量，响应周期(点击保存按钮和更改传播到浏览器之间的时间)比流量 CRA 慢。不确定这是否是由于 TypeScript 或 tslint 或 react-scripts-ts。

## 实际打字稿 vs 流

当我在做的时候。有些人倾向于比较 TypeScript 和 Flow。我无法表达他们对我来说有多相似。我将解释为什么——我有这样一个关于类型脚本和流程的内部对话:

*   Flow 是围绕可靠的思想构建的，而 TypeScript 是围绕“实用的”类型系统的思想构建的(实际上我并不接受这种思想，看起来他们只是不想要可靠)
*   是的，但是流程在执行中有错误，所以它希望是正确的，但实际上不是。如果你想要可靠，使用 ReasonML (Ocaml)。此外，流有时会放弃类型检查，即所谓的未覆盖类型代码区域，您需要使用[流覆盖报告](https://github.com/rpl/flow-coverage-report)
*   好的，但是 flow 有所有这些实用程序类型，比如`$ObjMap`、`$Call`等等。
*   是的。TypeScript 最近也添加了很多这样的内容。参见[实用类型](https://github.com/piotrwitek/utility-types)和[流向类型脚本](https://github.com/bcherny/flow-to-typescript)
*   然而[不可能在打字稿](https://github.com/piotrwitek/utility-types/issues/18)中实现`$ObjMap`
*   谁在乎。从文字中不可能得到精确的字符串和精确的对象类型

```
const a = "b"; 
```

*   在流类型中`a`是一个字符串，在 TS 中它是`"b"`字符串文字(而且不可能做`a[0]="c"`)
*   我听说确切的对象类型即将出现
*   最后，他们从 TypeScript 中复制了它
*   而 TypeScript 从 Flow 中复制了 [`typesVersions`](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-1.html) 的创意，现在我们需要等到这个创意将被复制回 [flow-typed](https://github.com/flow-typed/flow-typed)
*   脸书使用心流，所以 React 自然适合心流，在 TS 中你需要做些什么
*   好吧，但是 TS 也有最大数量的类型签名
*   我们可以使用一些 [flowgen](https://github.com/joarwilk/flowgen) 将其转化为流量
*   流量有 [idx](https://github.com/facebookincubator/idx)
*   TS 有[单片眼镜 ts](https://github.com/gcanti/monocle-ts) 、 [io-ts](https://github.com/gcanti/io-ts) 等等
*   TS 有编译器标志，这不是个好主意，因为每个项目都有不同的严格性。这同样适用于类型签名，你永远不知道它们有多严格。
*   TS 有更大的社区，更开放的开发周期。流程开发发生在关闭的门后

这样的对话持续不断。所以我自己决定 Flow 和 TypeScript 是一样的。两者都不健全，两者都不理想。然而，我会选择 TypeScript 或 Flow，而不是普通的 JavaScript。

不要让我开始谈论 ReasonML/BuckleScript

> 马特·琼斯在 Unsplash 上拍摄的照片

* * *

[这篇文章是](https://dev.to/t/pragmatictypes)系列文章的一部分。在[推特](https://twitter.com/stereobooster)和 [github](https://github.com/stereobooster) 上关注我。