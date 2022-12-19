# Elixir beginner-level 09: Re-education

> 原文：<https://dev.to/gumi/elixir-09--1a0p>

本文在 Elixir 官方网站的许可下，根据“[Recursion](https://elixir-lang.org/getting-started/recursion.html) ”的解说，进行修改，对 Elixir 中函数的递归调用进行说明。

# 递归循环

Elixir 的数据是可同步的，所以和其他函数型编程语言一样，循环处理的格式与指令型编程语言不同。 例如，在 C#这样的指令型语言中，将按以下方式处理数组元素的检索。

```
for (i = 0; i < sizeof(array); i++) {
  array[i] = array[i] * 2;
} 
```

Enter fullscreen mode Exit fullscreen mode

在上例中，数组和计数器变量的数据被改写。 Elixir 是可禁用的，所以数据不能改变。 因此，在函数型语言中使用递归。 函数递归地调用自身进行循环。 达到不调用的条件时，递归结束。 如果是这个方法的话，数据不会改变。

以下函数计算并返回列表中的元素数。 函数具有两个子句，它们在参数中进行模式匹配。 如果列表为空，则匹配第一个函数，因此返回 0，不进行递归调用。 这是结束条件。

如果列表中有元素，则传递尾部并递归调用，然后将其返回值加 1。 这意味着，每次递归调用时，它都会递增 1，当它为空时，递归会停止并返回合计值。

```
defmodule Length do
  def of([]), do: 0
  def of([_ | tail]), do: 1 + of(tail)
end 
```

Enter fullscreen mode Exit fullscreen mode

```
iex> Length.of []
0
iex> Length.of [1, 2, 3]
3 
```

Enter fullscreen mode Exit fullscreen mode

另外，第二个函数不在处理中使用头部的变量。 在不使用的变量的开头加上下划线`_`的是 Elixir 的[命名规则](https://hexdocs.pm/elixir/naming-conventions.html#underscore-_foo)。 如果尝试取出以`_`开头的变量中存储的值，将显示警告。 并且，仅将`_`作为变量时，值的检索将发生编译错误。

```
iex> _x = 0
0
iex> _x
warning: the underscored variable "_x" is used after being set. A leading underscore indic
ates that the value of the variable should be ignored. If this is intended please rename t
he variable to remove the underscore

0
iex> _ = 1
1
iex> _
** (CompileError) iex:n: invalid use of _. "_" represents a value to be ignored in a patte
rn and cannot be used in expressions 
```

Enter fullscreen mode Exit fullscreen mode

# 读和映射的算法

首先，修改返回上述列表中元素数的函数，对元素的数值进行合计。 将数字的第二个参数添加到函数中，并在标头中提供了默认值 0。 如果用自变量交接被相加的合计值，则不破坏绝对。

第一个函数在列表为空时返回参数的合计值，递归调用结束。 如果不是空的话，第二个函数会递归调用尾部和合计值作为参数，并加上头部的值。 也就是说，每次递归时，头部的值都将与合计值相加。

```
defmodule Sum do
  def up(list, accumulator \\ 0)
  def up([], accumulator), do: accumulator
  def up([head | tail], accumulator),
    do: up(tail, head + accumulator)
end 
```

Enter fullscreen mode Exit fullscreen mode

```
iex> Sum.up([1, 2, 3])
6
iex> Sum.up([4, 5], 6)
15 
```

Enter fullscreen mode Exit fullscreen mode

从列表中依次取出元素并将其组合成一个值的过程称为 reduce 算法，是函数式编程的重要思想之一。

接下来，对列表要素进行处理，重新创建新的列表。 以下函数将列表元素的数值平方，并作为元素中存储的新列表返回。 另外，`|`也是将两个列表连成一个的运算符(参照“[Elixir 入门 04 :模式匹配](https://dev.to/gumi/elixir-04--1346)”“基于运算符的模式匹配”)。 这样取出列表元素并放入新的列表元素中返回的过程称为 map 算法。

```
defmodule Sum do
  def square([]), do: []
  def square([head | tail]), do:
    [head * head | square(tail)]
end 
```

Enter fullscreen mode Exit fullscreen mode

```
iex> Sum.square([1, 2, 3])
[1, 4, 9] 
```

Enter fullscreen mode Exit fullscreen mode

递归和末尾调用的优化是 Elixir 的关键，在进行循环处理时也能有效利用(参照[末尾递归](https://ja.wikipedia.org/wiki/%E6%9C%AB%E5%B0%BE%E5%86%8D%E5%B8%B0))。 不过，在 Elixir 编程中，很少在列表操作中使用递归。

[`Enum`模块](https://hexdocs.pm/elixir/Enum.html)已经具备了可以在列表中使用的便利功能。 例如，函数[`Enum.reduce/3`](https://hexdocs.pm/elixir/Enum.html#reduce/3)和[`Enum.map/2`](https://hexdocs.pm/elixir/Enum.html#map/2)使用如下。

```
iex> Enum.reduce([1, 2, 3], 0, fn(x, acc) -> x + acc end)
6
iex> Enum.map([1, 2, 3], fn(x) -> x * x end)
[1, 4, 9] 
```

Enter fullscreen mode Exit fullscreen mode

此外，还可以用捕获语法写得更短。

```
iex> Enum.reduce([1, 2, 3], 0, &+/2)
6
iex> Enum.map([1, 2, 3], &(&1 * &1))
[1, 4, 9] 
```

Enter fullscreen mode Exit fullscreen mode

#### Elixir 入门彩票

*   [Elixir 入门 01 :编写代码并尝试](https://dev.to/gumi/elixir-01--2585)
*   [Elixir 入门 02 :型的基本](https://dev.to/gumi/elixir-02--30n1)
*   [Elixir 入门 03 :运算符的基本](https://dev.to/gumi/elixir-03--33im)
*   [Elixir 入门 04 :模式匹配](https://dev.to/gumi/elixir-04--1346)
*   [仙丹入門 05: 条件-情况/条件/条件](https://dev.to/gumi/elixir-05----casecondif-60o)
*   [Elixir 入门 06 :二进制和字符串以及字符列表](https://dev.to/gumi/elixir-06--35na)
*   [Elixir 入门 07 :关键词列表和地图](https://dev.to/gumi/elixir-07--39hi)
*   [Elixir 入门 08 :模块和函数](https://dev.to/gumi/elixir-08--1c4c)
*   Elixir beginner-level 09: Re-education
*   [仙丹入門 10: EnumとStream](https://dev.to/gumi/elixir-10-enumstream-4fpb)
*   [Elixir 入门 11 :流程](https://dev.to/gumi/elixir-11--2mia)
*   [Elixir 入门 12 :输入输出和文件系统](https://dev.to/gumi/elixir-12--4og6)
*   [仙丹入門 13: aliasとrequireおよびimport](https://dev.to/gumi/elixir-13-aliasrequireimport-55c1)
*   [Elixir 入门 14 :模块的属性](https://dev.to/gumi/elixir-14--3511)
*   [T0】 Elixir Beginner 15: Structure
*   [Elixir 入门 16 :协议](https://dev.to/gumi/elixir-16--lif)
*   [T0】 Elixir Beginner's Level 17: Record of Internal Package
*   [Elixir 入门 18 :锡吉尔](https://dev.to/gumi/elixir-18--5791)
*   [仙丹入門 19: tryとcatchおよびrescue](https://dev.to/gumi/elixir-19-trycatchrescue-50i8)
*   [Elixir 入门 20 :类型规格和行为](https://dev.to/gumi/elixir-20--j50)
*   [Elixir 入门 21 :调试](https://dev.to/gumi/elixir-21--21a1)
*   [Elixir 入门 22: Erlang 库](https://dev.to/gumi/elixir-22-erlang-2492)
*   [Elixir 入门 23 :下一步](https://dev.to/gumi/elixir-23--50ik)

##### Fanwai

*   [Elixir 入门:使用 Plug 为](https://dev.to/gumi/elixir-plug-40lb)