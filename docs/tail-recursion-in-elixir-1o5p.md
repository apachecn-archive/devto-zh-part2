# 酏剂中的尾部递归

> 原文：<https://dev.to/marcosx/tail-recursion-in-elixir-1o5p>

[![A pendant and a wall with wood pieces on the background](img/5fb88b16ea0ffc64bceea824c0c71648.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2PAf-B_h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1s6rjwku98btrqxgh655.jpg)

你可能听说过(也写过)一些递归函数/方法。您可能也听说过递归函数比非递归函数更昂贵。

让我们看看递归函数最简单的例子，阶乘实现。这里我们定义了`Factorial.recursive_factorial/1`的两个子句，on 用于`0`(返回`1`)，另一个用于任何其他值:

```
defmodule Factorial do
  def recursive_factorial(0), do: 1
  def recursive_factorial(n), do: n * recursive_factorial(n-1)
end 
```

在每次调用`recursive_factorial(n)`时，我们将`n`乘以`recursive_factorial(n-1)`的结果(这是一个递归调用，直到`n`到达`0`)。在每一步中，我们都需要保持`n`的当前值，这样一旦`recursive_factorial(n-1)`返回，我们就可以乘以它。

这就是为什么递归调用通常被认为是更昂贵的(特别是在空间方面)，每次调用都需要保存堆栈，直到结果返回。保存一个堆栈，即使只是当数量变得太高时`n`可能开始引起麻烦(比如说`500_000`或者更多，这取决于你在哪里运行它)。

但是我们可以做一些事情来使它变得更好，和非递归替代方法一样有效

## 尾部递归

看一下另一个实现，并将其与前一个进行比较:

```
defmodule Factorial do
  def tail_recursive_factorial(n), do: tail_recursive_factorial(n, 1)
  defp tail_recursive_factorial(0, acc), do: acc
  defp tail_recursive_factorial(n, acc), do: tail_recursive_factorial(n-1, acc*n)
end 
```

对`Factorial.tail_recursive_factorial/1`的第一次调用通过调用私有的`Factorial.tail_recursive_factorial/2`来启动这个过程，私有的`Factorial.tail_recursive_factorial/2`执行递归过程。

请注意，乘法现在被传递给使用第二个参数的递归调用。这意味着不需要为每次调用存储函数堆栈！

现在每个调用都有了它所需要的一切，一旦递归调用开始，堆栈就可以被丢弃。这里的 end 子句是当`n`为`0`时，它只返回`acc`上的值。

当递归函数不需要存储任何在递归调用后使用的临时值时，它被认为是尾递归函数。让我们检查一个更有趣的问题。

## 锻炼中的“紧张”练习

在 exercism.io 上有一个很好的小练习，要求你实现两个函数，`keep`和`discard`。给定一个项目的`list`和一个函数`fun` , `keep`将返回项目列表，其中`fun`返回`true`,`discard`将返回项目列表，其中`fun`返回`false`。

我和一些朋友一起解决了这个问题，我们最初对`keep`的实现是在列表的`head`上执行`fun`，如果是`true`则返回一个带有`head`的列表，然后在`tail`上递归，如果不是，则忽略列表的`head`再次调用:

```
def keep([], _fun) do
  []
end

def keep([head | tail] = list, fun) do
  keep_if(list, fun, fun.(head))
end

defp keep_if([head | tail] = list, fun, true) do
  [head | keep(tail, fun)]
end

defp keep_if([head | tail] = list, fun, false) do
  keep(tail, fun)
end 
```

这里的主要问题是，对于每个调用，当`fun`为真时，我们需要保存存储在`head`上的值，并且在递归调用结束后，返回必须被附加到包含`head`的列表中。

对于我们的测试场景，这不是一个大问题，但我们想更进一步，考虑如何使它成为一个尾部递归函数。

我们的优化类似于我们对`Factorial.tail_recursive_factorial/1`所做的，我们创建一个累加器参数并在每次调用时发送它，而不是等待返回结果。这是它最后的样子:

```
defmodule Strain do
 def keep(list, fun) do
   keep(list, [], fun)
 end

 def keep([], filtered, _fun) do
   filtered
 end

 def keep([head | _tail] = list, filtered, fun) do
   keep(list, filtered, fun, fun.(head))
 end

 defp keep([head | tail], filtered, fun, true) do
   keep(tail, filtered ++ [head], fun)
 end

 defp keep([_head | tail], filtered, fun, false) do
   keep(tail, filtered, fun)
 end
end 
```

另一个尝试和应用尾部递归优化的很好的练习是“列表操作”，它要求你实现我们通常认为理所当然的基本函数，比如`each`、`map`、`filter`等。

递归函数在函数式语言中相当常见，大多数甚至没有循环，因此学习尾部递归并练习如何实现它是一项不错的投资:)