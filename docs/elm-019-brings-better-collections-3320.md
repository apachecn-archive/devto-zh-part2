# Elm 0.19 带来更好的收藏

> 原文：<https://dev.to/robinheghan/elm-019-brings-better-collections-3320>

你可能已经意识到了，但是 Elm 0.19 刚刚发布。当您通读发行说明和核心库的文档时，您可能会认为内置集合类型没有什么变化:`List`、`Array`、`Dict`和`Set`。

你错了。

虽然 API 确实完全保持不变，但在幕后有巨大的变化，这带来了更高的性能和一个无 bug 的`Array`实现。让我们仔细看看。

## 列表

`List`是变化最小的数据结构。事实上，底层的数据结构根本没有改变。唯一改变的是`List.foldr`的实现，现在快了大约 30%。

这本身似乎没什么大不了的，但是考虑到`map`、`filter`和`append`都是在`foldr`之上实现的，这意味着`List`在所有方面都快了不少。

如果想了解更多的实际执行情况，可以看看这个 [PR](https://github.com/elm/core/pull/872) 。

## 字典和集合

`Dict`实际上是从头开始重新编写的，同时保留了原来的 API。这种新实现的好处主要在于性能。我们说的是插入速度快 170%，移除速度快 30%。

`Set`只是围绕`Dict`的一层薄薄的包装，所以`Set`也从新的实现中受益。

更多信息在 [PR](https://github.com/elm/core/pull/959) 和这些发展更新: [#1](https://groups.google.com/forum/#!topic/elm-dev/--fK-wMoDig) 、 [#2](https://groups.google.com/forum/#!topic/elm-dev/T-PHQNrkNss) 。

## 阵列

和`Dict`一样，`Array`完全被改写了。最初的代码是 Javascript，存在严重的错误，可能会导致运行时崩溃和突变。新的实现主要是在 Elm 中，所有已知的错误都被修复了。

从性能的角度来看，实现在某些情况下更快，而在另一些情况下更慢。本[开发更新](https://groups.google.com/forum/#!topic/elm-dev/TPUKXVXr1Hs)中的更多信息。

## 总结

API 可能是相同的，但是，已经做了大量的工作来使这些数据结构比以前更好。

对于`Random`模块也有类似的故事，Max Goldstein 重新实现了它，使它更快，你知道，更随机。你可以通过查看这个 [PR](https://github.com/elm/core/pull/778) 了解更多。