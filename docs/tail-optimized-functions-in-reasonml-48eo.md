# ReasonML 中的尾部优化函数

> 原文：<https://dev.to/hoichi/tail-optimized-functions-in-reasonml-48eo>

所有的例子都在 [ReasonML](https://reasonml.github.io) 中，非常粗略地说，这是我目前非常感兴趣的一个 OCaml & JS 的混合体。

先不说这个介绍，让我们写一个函数来生成一个 int 的范围，也就是说，接受一个元组`(beginning, end)`并返回一个列表，其中包含所有介于两者之间的`int`。
天真的做法

```
let rec genRange = ((min, max)) =>
  min > max
    ? []
    : [min, ...genRange((min + 1, max))]; 
```

它的问题是，如果范围相当长，可能会导致堆栈溢出。看到这个表情:`[min, ...genRange((min + 1, max))]`？为了对它求值，机器必须等待递归调用返回，然后使用返回值来构建更长的列表。

(顺便说一下，使用数组会更糟。您将拥有与最终结果中的元素一样多的数组，并且它们之间不会共享任何内存，实际上分配了大约`(len+1) * len/2`个元素，在最深层的嵌套调用开始返回之前，这些元素都不会被垃圾收集或释放。)

现在，原生 OCaml 编译器和 BuckleScript(后者将 OCaml/ReasonML 编译成 JS)都支持尾部调用优化。这意味着编译器不会将另一个帧添加到堆栈中，而是让生成的代码重用当前的代码。(这在 JS 代码中有点不同，但我们稍后会讲到。)

为了使该机制工作，您必须确保除了按原样返回递归调用的结果之外，不对它做任何其他事情。这样的话，你根本不用回来。最后调用的函数实例计算最终结果，并将其直接返回给最初的被调用者。

但是，这意味着您最后调用的实例应该拥有评估最终值的所有数据。作为参数传递(因为我们说的是 FP)。所以，我们的老天真`gen_range`不切实际。

`gen_range`已经有了`min`和`max`作为它的参数。在最后一步评估完整列表时，`[min, ...gen_range((min + 1, max))]`这个表达式缺少什么？

`min`？我们已经有了。`gen_range(min + 1, max)`的结果？这还差不多。我们需要传递列表的当前版本来构建下一个版本。我们再加上`list`作为参数:

```
let rec genRangeTco = (list, (min, max)) =>
  max < min
    ? list
    : genRangeTco([max, ...list], (min, max - 1)); 
```

你可能已经注意到，现在我们不增加`min`，而是减少`max`。看起来有点不自然，尤其是如果你的“自然”是古老的`i++`，但它更接近于列表是如何建立的:昨天是头部，今天是尾部的一部分。

另一个相当值得注意的事情是，签名现在不太方便了:您必须提供一个空列表作为参数。这就是为什么对于 TCO 函数，递归函数通常是一个帮助函数，用户得到的是一个简化的版本:

```
let genRangeTco = {
  let rec helper = (list, (min, max)) =>
    max < min
      ? list
      : helper([max, ...list], (min, max - 1));

  helper([]);
}; 
```

`helper([])`是一个局部应用，顺便说一句，将一个空列表预应用到`helper`并返回一个期望一个`(min, max)`元组的函数，就像我们的第一个版本一样。

最后一件事。当我写这篇文章的时候，尾音优化提议的前景还很不明朗。但是 BuckleScript 编译器(这也是一种 powers ReasonML)不会被否认，尽管它对它生成的 JS 的性能有极大的兴趣。先看看它用天真版做什么:[好 ole 递归调用](https://reasonml.github.io/en/try?rrjsx=true&reason=DYUwLgBATiDGEHMQDsBKBDZSIF4IAp8BbAS2QBoIj0APASjtwD4AoCKsiJq2t9iAPwQA2gF0+7AFwjSFCADpFSNJiSFZEANQQAjJWr06ogNwsgA)，没什么特别的，amirite？现在，请看，[尾部优化版](https://reasonml.github.io/en/try?rrjsx=true&reason=DYUwLgBA5iB2BKBDWMAqBjA9hAvBA3gFAQSiQBOI6EAFiMAA4jm4QAUwAlgM5gA07ALadYAwYgAeASim4AfMRIRxEiAB5lIxUogB+UjzDalALlr0m5NgG0VAgHSOuvALoC2w0cskQAtBABGGQBuQkU6RmYbFylQgF9QoA)。它被编译成了一个循环！

明白我为什么对理性感兴趣了吧？

* * *

先贴在 [hoichi.io](https://hoichi.io) ，因为家甜家。