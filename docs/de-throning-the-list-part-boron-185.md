# 删除列表:硼部分

> 原文：<https://dev.to/robinheghan/de-throning-the-list-part-boron-185>

在本系列的[最后一篇文章](https://dev.to/skinney/de-throning-the-list-part-sc4k-4e3n)中，我们谈到了为`Array`扩展 API，使其像`List`一样可用。在这篇文章中，我们将看看如何让`Array`成为默认集合来减少开销，从而提高性能。

在 Elm 0.18 中，当编译器看到这样的表达式:

```
list = [1, 2, 3] 
```

Enter fullscreen mode Exit fullscreen mode

它将其编译成下面的 javascript 代码:

```
var _some_namespace_list = {ctor: '::', _0: 1, _1: {ctor: '::', _0: 2, _1: {ctor: '::', _0: 3, _1: {ctor: '[]'}}}}; 
```

Enter fullscreen mode Exit fullscreen mode

这实际上很好，虽然看起来很丑，因为理论上这是创建`List`最快的方法。不幸的是，由于 Chrome 的工作方式，如果`List`足够大的话，它也会使浏览器崩溃。这与嵌套的对象文字有关，javascript 引擎在崩溃前只能支持这么多嵌套。你可以在接下来的[期](https://github.com/elm-lang/elm-compiler/issues/1521)中读到更多。

所以在 Elm 0.19 中，为了避免这个问题，它将改为编译成这个 javascript 代码:

```
var _some_namespace_list = _elm_core_fromArray([1, 2, 3]); 
```

Enter fullscreen mode Exit fullscreen mode

这是可行的，但这确实意味着每当你的程序中有一个`List`文字时，Elm 将在运行时从 javascript 数组中转换它。这种额外的转换，可能会使你的应用程序有点慢。

## 慢了多少？

我以为你不会问。基准时间到了！

该基准测试使用 Elm 0.18 的文字表达式(嵌套对象)和 0.19 运行时转换来比较调用`List.foldl (\a b -> a + b) 0`和`[1,2,3,4,5,6,7,8,9]`。通过手工编辑编译后的输出，我们可以得到一个带有`Array`文字的等价代码的性能基准。你可以在这里阅读基准代码。

基准测试是在 2012 年年中的 Macbook Air 上运行最新版本的 Chrome (v66)，运行最新版本的 Mac OS X (High Sierra)。

```
List literal sum: 873,847 ops/sec
List runtime sum: 1,959,202 ops/sec (124.2% faster than list literal)
Array literal sum: 3,329,392 ops/sec (40.61% faster than list runtime) 
```

Enter fullscreen mode Exit fullscreen mode

这些结果非常有趣。你会发现运行时转换实际上比 Chrome 中的文字表示要快得多。这可能与前面描述的运行时崩溃问题联系在一起。如果我们通过 Firefox 和 Safari 运行这个基准测试，您会发现运行时转换比文字表示慢 10-20%。

更有趣的是，文字会更快。通过在具有不同大小的`List`文字的不同浏览器上运行这个基准，我认为这与对象的嵌套有关。显然，嵌套的对象文字对性能没有好处。一个`List`是“仅仅”嵌套的对象，而`Array`具有非常扁平的结构。

## `Array`字面意思会是什么样子？

如果`Array`是默认的集合类型，编译器会将它转换成下面的 javascript 代码:

```
var _some_namespace_list = {ctor: 'Array', _0: 3, _1: 5, _2: [], _3: [1,2,3]}; 
```

Enter fullscreen mode Exit fullscreen mode

这比`List`的原始文字表示要短。嵌套也比较少。一个有 1024 个元素的`List`需要 1024 个嵌套的对象文字，而一个`Array`需要 3 个嵌套的文字。

## 总结

虽然 Chrome 中的`List`文字像一只气喘吁吁的蚂蚁一样慢，甚至可能让 Elm 应用程序崩溃，但`Array`文字是快速而安全的。

`List`的文字语法在 Elm 中大量用于 Html 节点之类的东西，因此能够快速构造这样的文字具有巨大的价值。通过将默认集合类型切换到`Array`，可以获得明显的性能优势。

我所有关于将`Array`设为默认集合类型的观点和想法现在都已经形成了。阅读这篇[下一篇](https://dev.to/skinney/de-throning-the-list-summary-3f3c)文章，了解我迄今为止所写内容的总结，以及我下一步的计划。