# 长生不老药入門 10: EnumとStream

> 原文：<https://dev.to/gumi/elixir-10-enumstream-4fpb>

本文在 Elixir 官网的许可下，根据“[Enumerables and Streams](https://elixir-lang.org/getting-started/enumerables-and-streams.html) ”的解说，经过修改后对 Elixir 的模块`Enum`和`Stream`进行说明。

# 枚举

Elixir 具备基于[列举型](https://ja.wikipedia.org/wiki/%E5%88%97%E6%8C%99%E5%9E%8B)( enumerable )的想法的[`Enum`模块](https://hexdocs.pm/elixir/Enum.html)。 [使用`Enum.map/2`](https://hexdocs.pm/elixir/Enum.html#map/2)函数，用第 2 参数的函数对第 1 参数的列表要素进行处理，得到存储了其返回值的要素的新列表。

```
iex> Enum.map([1, 2, 3], fn x -> x * x end)
[1, 4, 9]
iex> Enum.map(%{2 => :one, 4 => :two}, fn({k, v}) -> {v, div(k, 2)} end)
[one: 1, two: 2] 
```

Enter fullscreen mode Exit fullscreen mode

`Enum`模块中有很多函数，用于变换、排序、分组、过滤、项目检索等。 是 Elixir 开发者使用最多的模块之一。 `Enum`的函数具有可用于各种可列举的数据类型的[多态性](https://ja.wikipedia.org/wiki/%E3%83%9D%E3%83%AA%E3%83%A2%E3%83%BC%E3%83%95%E3%82%A3%E3%82%BA%E3%83%A0)。 处理的数据安装[`Enumerable`协议](https://hexdocs.pm/elixir/Enumerable.html)即可。

连号整数的数据也可以用[`../2`](https://hexdocs.pm/elixir/Kernel.html#../2)表示。 [`Enum.reduce/3`](https://hexdocs.pm/elixir/Enum.html#reduce/3)是将各数据处理成一个值后返回的函数。 第三参数函数处理第一参数中的数据，并将返回值传递给下一个数据回调。 第二个参数是传递给第一个数据回调的初始值。

```
iex> Enum.map(1..3, &(&1 * &1))
[1, 4, 9]
iex> Enum.reduce(1..3, 0, &+/2)
6 
```

Enter fullscreen mode Exit fullscreen mode

另外，`&+/2`用`&`运算符捕捉`+/2`(请参阅“[Elixir 入门 08 :模块和函数](https://dev.to/gumi/elixir-08--1c4c)”“函数捕捉”)。

`Enum`模块的函数的工作仅限于取出(枚举)数据结构的所有要素进行处理。 插入要素或改写值时，需要根据其数据类型进行操作。 例如，在列表的索引中插入值时，请使用`List`模块的[`List.insert_at/3`](https://hexdocs.pm/elixir/List.html#insert_at/3)。

# 管道算子

管道运算符[`|>`](https://hexdocs.pm/elixir/Kernel.html#%7C%3E/2)将左操作数的值传递给左操作数的第一参数。 另外，[`List.flatten/1`](https://hexdocs.pm/elixir/List.html#flatten/1)是对传递给参数的列表内的嵌套进行平坦化的函数。

```
iex> [1, [[2], 3]] |> List.flatten
[1, 2, 3] 
```

Enter fullscreen mode Exit fullscreen mode

如果将函数返回值传递给下一个函数的参数的过程重叠，函数将嵌套在一起。

```
iex> Enum.reduce(Enum.map(1..3, fn x -> x * x end), 0, fn(x, acc) -> x + acc end )
14 
```

Enter fullscreen mode Exit fullscreen mode

`|>`运算符在这种情况下，可以采用将左边的输出向右输入的易懂的写法。 在以下示例中还同时使用了捕获运算符`&`。

```
iex> 1..3 |> Enum.map(&(&1 * &1)) |> Enum.reduce(0, &+/2)
14 
```

Enter fullscreen mode Exit fullscreen mode

`Enum.reduce/3`是一个用途广泛的函数。 但是，只是把所有的要素加起来就可以使用[`Enum.sum/1`](https://hexdocs.pm/elixir/Enum.html#sum/1)。

```
iex> 1..3 |> Enum.map(&(&1 * &1)) |> Enum.sum
14 
```

Enter fullscreen mode Exit fullscreen mode

另外，使用管道运算符`|>`时，如果函数有第 2 个参数以后的参数，请用括号`()`括起来。 例如，[`List.flatten/2`](https://hexdocs.pm/elixir/List.html#flatten/2)在连接第 2 自变量的列表后进行平面化。 此时如果省略`()`，则会显示警告要添加。 因为很容易对代码产生误解。

```
iex> [1, [[2], 3]] |> List.flatten [4, [5]]
warning: parentheses are required when piping into a function call. For example:

    foo 1 |> bar 2 |> baz 3

is ambiguous and should be written as

    foo(1) |> bar(2) |> baz(3)

Ambiguous pipe found at:
  iex:

[1, 2, 3, 4, [5]] 
```

Enter fullscreen mode Exit fullscreen mode

# 先行和延迟

`Enum`模块的函数都是预处理。 例如，上面的代码与下面的处理一样，第一个函数立即返回返回值列表，第二个函数根据该数据生成结果列表。 也就是说，如果函数的调用有好几个重叠，就可以列出相应数量的中途经过。 如果数据的要素数量进一步增加，可能就不得不考虑其负荷了。

```
iex> square = Enum.map(1..3, &(&1 * &1))
[1, 4, 9]
iex> sum = Enum.sum(square)
14 
```

Enter fullscreen mode Exit fullscreen mode

`Stream`模块与`Enum`一样，具有处理`Enumerable`协议数据的函数，而且是延迟处理。 也就是说，在被要求处理之前，不进行数据的检索(枚举)。

# 流

[`Stream`](https://hexdocs.pm/elixir/Stream.html)是多个函数组合的延迟处理的列举型。 `Stream`不管枚举处理持续多少次，都会从数据中一个一个地取出要素。 也就是说，不制作中途经过的清单。

例如，用`../2`规定的连号整数为`Stream`。 对`Stream`调用`Enum`的函数时，数据将延迟执行。 另外，[`Stream.filter/2`](https://hexdocs.pm/elixir/Stream.html#filter/2)只取出第 2 参数的回调返回`true`的要素。

```
iex> range = 1..6
1..6
iex> odd = Stream.filter(range, &(rem(&1, 2) != 0))
#Stream<[enum: 1..6, funs: [#Function<40.58052446/1 in Stream.filter/2>]]>
iex> square = Stream.map(odd, &(&1 * &1))
#Stream<[
  enum: 1..6,
  funs: [#Function<40.58052446/1 in Stream.filter/2>,
   #Function<48.58052446/1 in Stream.map/2>]
]>
iex> sum = Enum.reduce(square, 0, &(&1 + &2))
35 
```

Enter fullscreen mode Exit fullscreen mode

`Stream`表示不制作中途经过的列表，而是列举处理的步骤。 然后，传递给`Enum`模块的函数时，从原始数据中一个一个地取出项目。 `Stream`在处理庞大的数据时有效。 可以做无限的数据。

`Stream`模块具备很多处理`Enumerable`协议数据的函数，返回值为`Stream`。 另外，还有根据指定制作`Stream`的函数。 例如，[`Stream.cycle/1`](https://hexdocs.pm/elixir/Stream.html#cycle/1)是从参数的列举型数据中返回无限的`Stream`的函数。 如果传递给`Enum`的列举函数，处理就会结束，请注意。 [`Enum.take/2`](https://hexdocs.pm/elixir/Enum.html#take/2)只取出自变量的数量进行处理。

```
iex> [1, 2, 3] |> Stream.cycle |> Enum.take(10)
[1, 2, 3, 1, 2, 3, 1, 2, 3, 1] 
```

Enter fullscreen mode Exit fullscreen mode

[`Stream.unfold/2`](https://hexdocs.pm/elixir/Stream.html#unfold/2)的处理流程与`Enum.reduce/3`相似。 不过，回调之间传递的数据采用元组`{現在値, 集計値}`的形式。 然后，第 1 自变量变为初始值。

这是用数学数列决定第 1 项和第 2 项，求出之后的数的想法。 只要回调没有用于结束的处理，就会变成无限的`Stream`。 例如，以下代码从[斐波那契数列](https://kotobank.jp/word/%E3%83%95%E3%82%A3%E3%83%9C%E3%83%8A%E3%83%83%E3%83%81%E6%95%B0%E5%88%97-615599)中检索 10 项并在列表中返回。

```
iex> fibs = Stream.unfold({1, 1}, fn({n0, n1}) -> {n0, {n1, n0 + n1}} end)
#Function<64.58052446/2 in Stream.unfold/2>
iex> Enum.take(fibs, 10)
[1, 1, 2, 3, 5, 8, 13, 21, 34, 55] 
```

Enter fullscreen mode Exit fullscreen mode

[`File.stream!/3`](https://hexdocs.pm/elixir/File.html#stream!/3)将从自变量的路径中读取的文件设为`Stream`([`File.stream`](https://hexdocs.pm/elixir/File.Stream.html))。 开始流式传输时，Elixir 会自动打开文件。 然后，读取结束或失败时就会被关闭。 处理读取的文件后，将显示按行划分的元素列表。

```
iex> stream = File.stream!("test.txt")
%File.Stream{
  line_or_bytes: :line,
  modes: [:raw, :read_ahead, :binary],
  path: "test.txt",
  raw: true
}
iex> Enum.take(stream, 10) 
```

Enter fullscreen mode Exit fullscreen mode

`Stream`适合处理大文件和网络重资源。 一开始用`Enum`慢慢习惯比较好吧。 需要延迟处理，或者需要处理沉重的资源和庞大的数据时，请考虑使用`Stream`。

#### Elixir 入门彩票

*   [Elixir 入门 01 :编写代码并尝试](https://dev.to/gumi/elixir-01--2585)
*   [Elixir 入门 02 :型的基本](https://dev.to/gumi/elixir-02--30n1)
*   [Elixir 入门 03 :运算符的基本](https://dev.to/gumi/elixir-03--33im)
*   [Elixir 入门 04 :模式匹配](https://dev.to/gumi/elixir-04--1346)
*   [仙丹入門 05: 条件-情况/条件/条件](https://dev.to/gumi/elixir-05----casecondif-60o)
*   [Elixir 入门 06 :二进制和字符串以及字符列表](https://dev.to/gumi/elixir-06--35na)
*   [Elixir 入门 07 :关键词列表和地图](https://dev.to/gumi/elixir-07--39hi)
*   [Elixir 入门 08 :模块和函数](https://dev.to/gumi/elixir-08--1c4c)
*   [Introduction T0】 Elixir 09: Rethink](https://dev.to/gumi/elixir-09--1a0p)
*   长生不老药入門 10: EnumとStream
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