# 工作方式如下所示:Elixir 的 doctest 宏

> 原文：<https://dev.to/sampriddy/works-as-shown-elixirs-doctest-macro-2gmj>

## 什么是 doctest？

`doctest`是一个宏，在指定的模块中搜索代码示例，并自动生成测试用例。

## 为什么要用 doctest？

主要是为了确保模块文档的正确性。Doctest 示例也很容易编写，可以取代简单的单元测试，并为指定代码示例和演示模块的 API 提供了标准格式。

随着时间的推移，未经测试的文档趋向于不正确，最终变得比根本没有文档更糟糕。

## 一个小例子

```
# lib/small_math.ex
defmodule SmallMath do
  @doc """
  Adds two numbers together.

  ## Examples
    iex> SmallMath.add(1, 2)
    3

    iex> SmallMath.add(5, 7)
    23
  """
  def add(a, b), do: a + b
end

# test/small_math_test.exs
defmodule SmallMathTest
  use ExUnit.Case, async: true
  doctest SmallMath
end 
```

Enter fullscreen mode Exit fullscreen mode

### 输出

```
$ mix test

Compiling 1 file (.ex)
.

  1) doctest SmallMath.add/2 (2) (SmallMathTest)
     test/small_math_test.exs:3
     Doctest failed
     code: SmallMath.add(5, 7) === 23
     left: 12
     stacktrace:
       lib/small_math.ex:9: SmallMath (module)

Finished in 0.03 seconds
2 doctests, 1 failure

Randomized with seed 888764 
```

Enter fullscreen mode Exit fullscreen mode

正如我们所看到的，doctest 从我们的文档中生成了两个测试用例，其中一个失败了。

## 它是如何工作的？

`doctest`通过搜索以`iex>`开头的代码行来寻找代码示例，并像坐在控制台前的用户一样评估它们。之后，指定一个空值(即不以`iex>`或`...>`开头)的行被解释为结果，并且生成一个测试来断言指定的值是用户在评估最后一个表达式时将看到的值。

```
@doc """
iex> 5 + 5  # expression
10          # result
""" 
```

Enter fullscreen mode Exit fullscreen mode

### 多行表达式

您可以使用`...>`构建一个多行表达式。

```
@doc """
  iex> add_one_fn = fn(number) ->
  ...>   number + 1
  ...> end
  iex> add_one_fn.(5)
  6
""" 
```

Enter fullscreen mode Exit fullscreen mode

### 测试异常

还支持编写演示异常的示例！

```
@doc """
  iex> 1 + "hello"
  ** (ArithmeticError) bad argument in arithmetic expression
""" 
```

Enter fullscreen mode Exit fullscreen mode

## 告诫

由`doctest`生成的测试不会彼此孤立地运行。产生副作用的代码不是很好的匹配。在 doctest 中绑定变量或定义代码也会带来代码在另一个测试中被重用的风险。