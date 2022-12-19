# 在 Elixir 中解析数字

> 原文：<https://dev.to/appsignal/parsing-numbers-in-elixir-4odo>

像任何现代编程语言一样，Elixir 有用于完成基本任务的内置工具，比如从字符串中解析数字。尽管它们是内置的，随时可以使用，但理解底层算法是很重要的。

在这篇文章中，我们将首先解释如何在 Elixir 中将字符串转换成整数。这将是快速和有用的。之后，我们将直接进入兔子洞，解释底层算法。这是我们热爱的炼金术。它可能会帮助您实现一个类似的解析器，但它肯定会满足您对 Elixir 中解析数字背后的数学思想的好奇心。

## 快速(且枯燥)内置

在 Elixir 中，可以使用`Float.parse/1` :
将字符串转换成浮点数

```
iex> Float.parse("1.2")
{1.2, ""} 
```

Enter fullscreen mode Exit fullscreen mode

这将返回一个 tuple，其中第一个元素是解析后的数字，第二个元素是找到非数字字符后输入字符串的剩余部分。如果您不确定您的输入是否包含附加数据，这很有用:

```
iex> Float.parse("3 stroopwafels")
{3.0, " stroopwafels"}

iex> Float.parse("stroopwafels? 3, please")
:error # This fails because the number needs to be at the beginning of the string 
```

Enter fullscreen mode Exit fullscreen mode

如果您确定您输入的是一个格式良好的浮点数，没有额外的字符，您可以使用一种更直接的方法:

```
iex> String.parse_float("1.2")
1.2

iex> String.parse_float("3 stroopwafels")
** (ArgumentError) argument error
    :erlang.binary_to_float("3 stroopwafels") 
```

Enter fullscreen mode Exit fullscreen mode

为了满足我们的技术好奇心，让我们深入了解一下这在内部是如何工作的。我们不会实现可靠解析浮点数和整数所需的所有东西，但是我们会学到足够的知识来理解基本原理。

## 下兔子洞

一种思考数字解析的方法是将一个数字分解成多个部分:

```
1234 = 1000 + 200 + 30 + 4 
```

Enter fullscreen mode Exit fullscreen mode

利用这一知识，我们可以使用分而治之的策略来解析这个数字，方法是分别解析它的每个数字。Elixir 的模式匹配和递归功能也非常适合这里。

### 解析单个数字

出于演示目的，让我们从一个一位数的整数开始。

```
defmodule Parser do
  def ascii_to_digit(ascii) when ascii >= 48 and ascii < 58 do
    ascii - 48
  end
  def ascii_to_digit(_), do: raise ArgumentError
end 
```

Enter fullscreen mode Exit fullscreen mode

`ascii_to_digit/1`函数期望得到一位数的 [ASCII 码](https://en.wikipedia.org/wiki/ASCII)，并返回相应的整数。这只适用于实际的数字字符，在 ASCII 表的 48 到 57 范围内。任何其他值都将导致引发异常。

知道了数字在 ASCII 表中是顺序声明的，我们可以简单地减去 48 来得到实际的数值。在下一节中，这个函数将是一个有用的助手。

### 解析一个整数

现在让我们添加一个函数来处理包含整数的整个字符串:

```
defmodule Parser do
  def parse_int(str) do
    str
    |> String.reverse()
    |> do_parse_int(0, [])
  end

  def do_parse_int(<<char :: utf8>> <> rest, index, cache) do
    new_part = ascii_to_digit(char) * round(:math.pow(10, index))

    do_parse_int(
      rest,
      index + 1,
      [new_part | cache]
    )
  end
  def do_parse_int("", _, cache) do
    cache
    |> Enum.reduce(0, &Kernel.+/2)
  end

  # ...
end 
```

Enter fullscreen mode Exit fullscreen mode

这里，`do_parse_int/3`函数遍历字符串，使用两个辅助参数:一个计数器，它随着每一个新数字而递增，给我们遍历中的当前索引；一个数组，我们在其中保存中间值。

另外，请注意，我们首先反转了字符串。这是因为 Elixir 的模式匹配只允许我们匹配一个字符串的开头，而不是结尾。我们希望从最低有效位开始，它位于数字的右端。所以我们首先反转字符串，然后从左向右遍历。

对于每一个数字，我们都用`10^index`乘以它。这意味着对于字符串`"1234"`，我们最终得到了下面的数组:

```
[1000, 200, 30, 4] 
```

Enter fullscreen mode Exit fullscreen mode

剩下的就是对所有的元素求和，一旦我们匹配了一个空字符串就完成了。

*注意*:它的一个优化版本可以对字符串的原始字符运行`Enum.reduce`调用，立即对数字求和，而不是保存一个临时列表。我们在这里没有这样做，这样我们可以更好地划分责任，让代码更具可读性。

### 解析浮动

要实现一个`parse_float/1`函数，剩下的就是处理小数。幸运的是，我们可以重用我们现有的`parse_int/1`函数，以及几个奇特的技巧来让一切工作:

```
defmodule Parser do
  @float_regex ~r/^(?<int>\d+)(\.(?<dec>\d+))?$/

  def parse_float(str) do
    %{"int" => int_str, "dec" => decimal_str} = Regex.named_captures(@float_regex, str)

    decimal_length = String.length(decimal_str)

    parse_int(int_str) + parse_int(decimal_str) * :math.pow(10, -decimal_length)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我们定义了一个`@float_regex`模块变量，它保存了一个正则表达式，能够捕捉浮点数的左右两边。小数点分隔符及其后面的数字是可选的，所以这个正则表达式将匹配`"123"`，就像它匹配`"123.456"`一样。

解释这个正则表达式的细节超出了本文的范围，但是可以在您的 Elixir 控制台中随意使用它。

当我们运行正则表达式时，针对我们的输入，比如说`"123.456"`，我们最终得到了下面的映射:

```
%{
  "int" => "123"
  "dec" => "456"
} 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以看到`parse_int/1`派上了用场。它可以用在两个零件上，分别得到`123`和`456`。但是我们如何将它们结合起来，以获得想要的结果呢？

数学再次拯救了我们。将小数部分乘以`10^-3`，其中`3`是长度，得到`0.456`，我们可以将它加到整数部分得到最终结果。

我们的最终结果可以解析字符串中的整数和浮点数。

```
defmodule Parser do
  def parse_int(str) do
    str
    |> String.reverse()
    |> do_parse_int(0, [])
  end

  def do_parse_int(<<char::utf8>> <> rest, index, cache) do
    new_part = ascii_to_digit(char) * round(:math.pow(10, index))

    do_parse_int(
      rest,
      index + 1,
      [new_part | cache]
    )
  end
  def do_parse_int("", _, cache) do
    cache
    |> Enum.reduce(0, &Kernel.+/2)
  end

  @float_regex ~r/^(?<int>\d+)(\.(?<dec>\d+))?$/

  def parse_float(str) do
    %{"int" => int_str, "dec" => decimal_str} = Regex.named_captures(@float_regex, str)

    decimal_length = String.length(decimal_str)

    parse_int(int_str) + parse_int(decimal_str) * :math.pow(10, -decimal_length)
  end

  def ascii_to_digit(ascii) when ascii >= 48 and ascii < 58 do
    ascii - 48
  end
  def ascii_to_digit(_), do: raise(ArgumentError)
end 
```

Enter fullscreen mode Exit fullscreen mode

## 未处理的案件

这在某种程度上概括了低级数字解析器如何在 Elixir 中工作。然而，它并没有涵盖每个可能的场景。没有涵盖的一些内容有:

*   支持负数
*   支持科学符号(例如`1.23e7`)
*   更优雅的错误处理。如果您正在为自己的用例构建一个解析器，那么错误处理也应该取决于用例是什么，以及它的条件。因此，这里没有讨论它。
*   处理更多的数字系统。你注意到我们在一些地方使用`:math.pow(10,x)`了吗？使`10`可配置应该允许我们支持二进制、八进制或十六进制字符串。

我们很想知道您对这篇文章的看法，或者您是否有任何问题。我们总是在寻找可以研究和解释的话题，所以如果你想阅读《长生不老》中的任何内容，请不要犹豫，留下你的评论。