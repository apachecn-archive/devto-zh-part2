# Kotlin 中列表操作符的工作效率如何

> 原文：<https://dev.to/lalunamel/how-efficient-list-operators-work-in-kotlin-4o3d>

*本帖原本为[blog.codysehl.net](http://blog.codysehl.net)T3】准备*

在上一篇文章中，我们观察了科特林的`List` `map`、`filter`和`reduce`操作符是否有效。

如果你还没有看过那篇文章，请继续吧！

所以，我们知道它们实际上是*非*有效的，这里有效的定义是指对于一个给定的列表，列表被迭代很少的次数。事实证明，使用 Kotlin 的列表操作符意味着每个操作符迭代一次列表！这就是我描述的`A loop for every operator method`。

我提出了一个更高效的方法叫做`Many Operators, One Loop`。该方法只遍历列表一次，并将列表操作符转换成一个`if`语句和一系列内联操作。

## 多运算符，单循环方法会是什么样子？

在前一篇文章的例子中，我描述了一个转换:`.filter()`将变成一个`if`。
不幸的是，我们不是在编写编译器，也没有能力将一位代码转换成另一位——我们想出的任何解决方案都只能使用该语言中可用的工具。

自从我写了上一篇博文以来，我一直在思考这个问题:高效的列表操作符如何工作？

让我们具体了解一下*高效*的含义:

1.  仅遍历列表一次。
2.  只做必要的工作——如果在从列表中删除元素的`filter`之后出现了`map`,那么`map`就不应该运行。

## 列表操作符的工作效率如何

下面是我对列表操作符工作效率的猜测。

对列表执行的列表操作不会立即执行，而是保存在包含源数据和要执行的一系列*其他*操作的地方。当你准备好你的数据时，你要求它计算你的结果——也许用一个叫做`collect`的函数。

`collect`函数只是一个循环，对于每个元素，它应用每个已经按顺序保存的操作。
每个操作都可以返回一个结果或`null`(或其他一些空类型的值)。如果一个操作收到一个`null`，它会立即返回并且不做任何工作。
最后，所有非空值都被舍入到一个列表中并返回。

## 列表操作符实际工作效率如何

让我们来看看[序列](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.sequences/-sequence/index.html)的来源。

(就像以前一样，你可能不想跟随链接——这些文件长达数千行。)

### 地图

[源](https://github.com/JetBrains/kotlin/blob/1.2.60/libraries/stdlib/common/src/generated/_Sequences.kt#L803)描述了一个简单地返回一个`TransformingSequence`的函数，它是通过传递一个源序列和一个转换函数创建的。

好吧！

#### 变换顺序

什么是`TransformingSequence`？

我们来看看[它的实现](https://github.com/JetBrains/kotlin/blob/769344569d7e6b79437221efd6d815e441dc682a/libraries/stdlib/src/kotlin/collections/Sequences.kt#L170)。

大多数情况下，它实现了迭代器的行为。

```
override fun iterator(): Iterator<R> = object : Iterator<R> {
  val iterator = sequence.iterator()
  override fun next(): R {
    return transformer(iterator.next())
  }

  override fun hasNext(): Boolean {
    return iterator.hasNext()
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

向这个`TransformingSequence`请求它的下一个元素将导致它向*的*源序列请求*的*的下一个元素，然后应用给定的转换函数。

这种递归行为可能会持续一段时间——有意思！

### 滤镜

`Filter`怎么样？

[source](https://github.com/JetBrains/kotlin/blob/1.2.60/libraries/stdlib/common/src/generated/_Sequences.kt#L370) 描述了一个函数，它返回一个接受源序列的新的`FilteringSequence`、一个布尔值 <sup id="fnref1">[1](#fn1)</sup> ，以及一个接受输入并返回真或假的函数(一个*谓词*)。

#### 过滤顺序

什么是`FilteringSequence`？

我们来看看[它的实现](https://github.com/JetBrains/kotlin/blob/769344569d7e6b79437221efd6d815e441dc682a/libraries/stdlib/src/kotlin/collections/Sequences.kt#L122)。

```
override fun iterator(): Iterator<T> = object : Iterator<T> {
  val iterator = sequence.iterator()
  var nextState: Int = -1 // -1 for unknown, 0 for done, 1 for continue
  var nextItem: T? = null

  private fun calcNext() {
    while (iterator.hasNext()) {
      val item = iterator.next()
      if (predicate(item) == sendWhen) {
        nextItem = item
        nextState = 1
        return
      }
    }
    nextState = 0
  }

  override fun next(): T {
    if (nextState == -1)
      calcNext()
    if (nextState == 0)
      throw NoSuchElementException()
    val result = nextItem
    nextItem = null
    nextState = -1
    @Suppress("UNCHECKED_CAST")
    return result as T
  }

  override fun hasNext(): Boolean {
    if (nextState == -1)
      calcNext()
    return nextState == 1
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

花几分钟阅读并理解它。

`FilteringSequence`与`TransformingSequence`的相似之处在于它是下一个值的源序列，但是——这就是它的不同之处！-它会一直问，直到找到一个满足给定谓词的！整洁！

### 减少

最后，让我们看看`reduce`和它的[来源](https://github.com/JetBrains/kotlin/blob/1.2.60/libraries/stdlib/common/src/generated/_Sequences.kt#L1272)。

```
public inline fun <S, T : S> Sequence<T>.reduce(operation: (acc: S, T) -> S): S {
  val iterator = this.iterator()
  if (!iterator.hasNext()) throw UnsupportedOperationException("Empty sequence can't be reduced.")
  var accumulator: S = iterator.next()
  while (iterator.hasNext()) {
    accumulator = operation(accumulator, iterator.next())
  }
  return accumulator
} 
```

Enter fullscreen mode Exit fullscreen mode

没有`ReducingSequence`！

那是因为`reduce`是一个*终端*操作。一个*终端*操作是对一个序列所做的最后一件事。它将序列(实际上只是一堆有待计算的潜在值)转换成可供程序其余部分使用的实际值。在`reduce`的情况下，一个序列中的所有值都被压缩成一个值。

如你所见，`Sequence`的`reduce`的实现看起来与`List`的`reduce`非常相似，我们在[的上一篇文章](http://blog.codysehl.net/2018/are-kotlin-list-operators-efficient/#reduce) :
中提到过

```
public inline fun <S, T : S> Array<out T>.reduce(operation: (acc: S, T) -> S): S {
  if (isEmpty())
    throw UnsupportedOperationException("Empty array can't be reduced.")
  var accumulator: S = this[0]
  for (index in 1..lastIndex) {
    accumulator = operation(accumulator, this[index])
  }
  return accumulator
} 
```

Enter fullscreen mode Exit fullscreen mode

## 序列有效率吗？

在查看了科特林的序列和`map`、`filter`、`reduce`的实现后，我们可以看到它们满足了一开始提出的*高效*的定义。上面的实现只遍历列表一次，对于 filter，只在谓词满足时提供一个值。

我对列表操作符的工作效率的猜测与它们的实际实现方式有些出入——首先，我的列表概念被一系列相互调用的序列所取代——但是我认为大纲是匹配的。

## 包装完毕

我希望这次对源代码的探究已经向您展示了学习细节是多么有趣！

此外，我希望您也注意到了猜测解决方案离真正的实现并没有太大的距离。

这只是表明，每个人都有能力解决这样有趣的问题，无论你是创造编程语言的高手，还是只是喜欢把事情搞清楚的普通人。

* * *

1.  内联解释这个布尔值似乎有点冗长。我认为文档很清楚这个参数所扮演的角色: *If `true`，返回谓词返回的值`true`。否则，谓词返回的值将返回`false`*。 [↩](#fnref1)