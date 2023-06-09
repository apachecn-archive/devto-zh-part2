# Functors 和 ReasonML 的乐趣

> 原文：<https://dev.to/schniz/fun-with-functors-and-reasonml-iim>

> 或者:使用 Reason 的合理函数式编程，第 1 部分:
> 初学者对 ReasonML 类型和仿函数类型的简短介绍。

在阅读了[布莱恩·朗斯多夫](https://medium.com/u/a3129fead82b)的令人惊叹的书[“JavaScript 函数式编程基本指南”](https://github.com/MostlyAdequate/mostly-adequate-guide)之后，我开始学习一些函数式模式。这本书让我对这种我从未学过的新范式着迷，并让我想在某个地方实践这些模式。

我发现自己在有趣的项目中使用它，但从未在实际工作项目中使用过——它总是属于“大多数 javascript 程序员不会这样编码”的范畴。我的同事是对的，所以我决定学习一种不同的语言。最初是哈斯克尔。我开始学习它，但是它对我来说太理论化了(或者说太“学术化了”):我是一个非常实际的人，我喜欢能够用我首先学习的范例和语言来使用和构建软件。

所以…在过去的几天里，我一直在学习[原因](https://reasonml.github.io/)。

* * *

### 原因？

Reason，或 ReasonML，是 OCaml 的一种新语法。它看起来更像 JavaScript，所以更容易上手。OCaml 部分不需要告诉你太多，除了你背后有一个很大的社区，这种语言已经存在很多年了。OCaml 将 opam 作为他们的包管理工具，这个社区非常活跃。查看 [awesome-ocaml](https://github.com/rizo/awesome-ocaml) 来看看来自 ocaml 社区的一些很酷的东西。

“新语法”是什么意思？拥有 OCaml 的新语法就像使用 CoffeeScript 或者使用 Babel 将新的 JS 语法转换成旧的 JS 语法——用一种语法编写，然后编译成另一种语法。Reason“编译”成 OCaml，和 BuckleScript，一个把 OCaml 编译成非常高效的 JavaScript 的工具一起，你可以看到这个链条:reason➡·ocaml➡JavaScript。

OCaml 和 Reason 都有完整的声音(和推断)类型系统，所以 100%的代码在编译时都覆盖了类型。这意味着更少的错误。推理部分也很神奇；这意味着您不必自己编写类型。编译器理解你的代码并为你做这些。

对于开发类似 Java 的类型系统的开发人员来说，像 Reason 一样对系统进行类型化——注意，在 Reason 中，空值不是一个东西。你不能把 null 作为一个函数的参数发送，这个函数想要得到一个字符串。这不可能。*这并不是说缺席的整个概念没有融入理性*，只是你有一种特殊的方式来做到这一点。那种方式叫做`option`。

### `option`式

按理说，您可以将类型声明为构造函数。构造函数*可以在*中包含数据，但这不是强制性的。让我们以`option`类型为例，它的声明如下:

```
type option('a) = None | Some('a) 
```

喔呼，对于第一个来的人来说这里有很多事情要做。让我们一起回顾一下这个定义。

首先，我们声明一个`type`，叫做`option`。然后，还有*类型参数*:那个`'a`的东西被称为“类型参数”——它像泛型或模板一样在 Java、C++等语言中使用。这就是为什么 option 可以“包装”许多不同的类型:如果你有一个可空的整数，你可以有`option(int)`;如果你有一个可空的字符串，你可以有`option(string)`!类型参数总是以一个记号(`'`)为前缀，所以在例子中你不能看不到它。您可以有多个类型参数，稍后会详细介绍。

然后，我们说我们的类型是两个构造函数的联合:`None`，它不接收任何数据，和`Some`，它接收类型为`'a`的数据，正如类型参数所说。

内建的`option`类型是我们合理处理缺席的方式。在 Haskell 中，它被称为`Maybe`。我们总是知道我们收到了一个`option`类型，当我们有一个`option`类型时，我们总是要在使用它之前打开它。我们用一个叫做[模式匹配](https://reasonml.github.io/docs/en/pattern-matching.html)的很酷的特性来解包数据:

```
let greet = (optionalName: option(string)) : string =>
  switch optionalName {
  | Some(name) => "Hello, " ++ name
  | None => "Who are you?"
  };

print_endline(greet(Some("Gal"))); /* prints "Hello, Gal" */ print_endline(greet(None)); /* prints "Who are you?" */ 
```

看看我如何轻松地解开数据。当我得到`None`时，我返回一个包含`Who are you?`的字符串。当我得到有名字的`Some`时，我返回`Hello, ${name}`。它实际上是这样工作的，这可能看起来很奇怪，但它确实如此！([你可以点击这里，在理由尝试页面](https://reasonml.github.io/en/try.html?reason=DYUwLgBA5gTi4QLwQBQHsAOYCWaB2AhsAHIEC2IAXBJjvigM5gzZ5QCU7E1TLbSAPgBQECAwDu2MAGMAFqlq5CJciC4BvERAA+EAMpoKKQhS6IBEAEQAJEMGBoANFYgBqVxBMgtu4vhCCVgDqsmgQBHAQAJ5oAK4A-JZaAL4A3EJCGHxgAPogeAAmwKwgKLDwYCgGRpYA4kSWnOypEAD0AFQQWaxgDFa29k4Q9cCWEO2tmdl5hcV4peXgKH7znC0dXdl9liFhEQExCWMTQA)中查看它编译成什么样子)

### 好的好的……可选类型。什么是函子？

现在我们知道了什么是`option`类型，我们可以从这里继续，并在本文的剩余部分中使用它作为我们的例子。

函子是一种可以被*映射*的数据类型。这意味着你可以在上面调用`map`函数。`map`函数接受一个仿函数和一个函数。然后，将提供的函数应用于仿函数的值(解开它)，并用仿函数的实例再次包装结果。

你可能已经熟悉来自 ES5 的[Array # map:Array . map(fn)。这是因为*数组是一个函子*，其中有多个值。提供的函数用数组中的每个值来计算，作为回报，你得到一个新的数组！顺便说一句，从道理上讲，你不会对一个值调用方法——这就是 OOP。调用方法的方式是调用一个函数并将值传递给它:](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)

```
let increment = x => x + 1;
map(Array[1], increment) /* => Array[2] */ 
```

选项类型和数组一样，也是一个函子。为了理解它是如何工作的，让我们把它分成两个构造函数:一些和没有。

如果我们映射到`None`上会发生什么？我建议它应该具有与空数组映射相同的行为:它不应该做任何事情，它根本不应该执行所提供的函数——它应该只返回`None`。对于`Some`，我建议它应该具有与映射一个只有一个值的数组相同的行为。所以单个值将被应用到函数中，然后用`Some`再次打包:

```
let increment = x => x + 1;
map(None, increment); /* => None */
map(Some(1), increment); /* => Some(2) */ 
```

实现这个 map 函数只需要几行代码:

```
let mapOption = (opt, fn) =>
  switch opt {
  | None => None
  | Some(value) => Some(fn(value))
  }; 
```

Reason 惊人的类型系统保证了`fn`是一个接收提供的选项值的函数，并返回一个新的选项。mapOption 的完整类型声明是:

```
let mapOption = (opt: option('a), fn: 'a => 'b) : option('b) 
```

让我们一起回顾一下:我们首先声明一个名为`mapOption`的函数，它接收一个名为`opt`的参数，类型为`option('a)`。正如我们已经谈到的，`'a`是一个类型参数，使得这个函数得到*任何类型的`option`。*然后，我们接收一个函数，该函数接收`'a`(我们在 opt 参数中的类型参数)，并返回`'b`，这是或不是一个不同的类型。然后，该函数返回`option('b)`，它与内部函数结果类型相同，但被包装在一个`option`中。

换句话说，我们接收一个`option('a)`，然后我们接收一个将值从类型`'a`转换为`'b`的函数，然后我们返回`option('b)`:我们支持内部类型之间的转换，而我们仍然在`option`的土地上:

```
let stringify = x => string_of_int(x); /* int => string */
mapOption(Some(1), stringify); /* => Some("1") */
mapOption(None, stringify); /* => None */ 
```

### 更多函子例子

我们可以声明另一种类型——`result`函子。结果函子几乎和`option`一样，但是它的负端也有一个值。你可以把它的值当作一个`reason`，或者`error`，或者仅仅是`failure`。当你返回`result`仿函数时，你基本上是在说“这个方法可能会失败”。您可以在验证和数据库查询中使用 result，或者将其用于任何错误很重要的事情:

```
let result('s, 'f) = Success('s) | Failure('f) 
```

地图行为几乎与`optionMap`行为相同。如果我们有`Success`，我们将获取它的数据并将其应用到所提供的函数中。如果我们有一个`Failure`，我们不会将值应用到函数中，只是让那个`Failure`保持原样。实现也是几行代码:

```
let mapResult = (res, fn) =>
  switch res {
  | Failure(failure) => Failure(failure)
  | Success(success) => Success(fn(success))
  }; 
```

这种数据结构帮助我们将程序构建成一棵树。当我们遇到问题时，我们停止运用我们的逻辑去解决它。我们基本上停止了“快乐之路”,直到我们处理了失败。*当我们映射到`Failure`时，我们仍然会遇到同样的失败，所以一旦有了`Failure`，就不会发生数据转换。*

在下一篇文章中，我将讨论单子，它是函子的一个特例，在构建数据转换时可以给你很大的帮助。

> 在那之前，你对自定义函子有什么想法？

感谢阅读！

* * *