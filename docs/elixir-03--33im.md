# Elixir 入门 03 :运算符基本知识

> 原文：<https://dev.to/gumi/elixir-03--33im>

本文在 Elixir 官方网站的许可下，根据“[Basic operators](https://elixir-lang.org/getting-started/basic-operators.html) ”的解说，加以修改，对 Elixir 的基本运算符进行说明。

# Arithmetic calculus

四则运算如下。 请注意，即使结果为整数，运算符`/`的除法也会变为浮点数。

```
iex> 4 + 2
6
iex> 4 - 2
2
iex> 4 * 2
8
iex> 4 / 2
2.0
iex> (1 + 2) / 3 + 4 * 5
21.0 
```

Enter fullscreen mode Exit fullscreen mode

通过整数的除法，得到商的整数部分使用[`div/2`函数](https://hexdocs.pm/elixir/Kernel.html#div/2)。 另外，求出整数的馀数的是[`rem/2`函数](https://hexdocs.pm/elixir/Kernel.html#rem/2)。

```
iex> div(10, 2)
5
iex> rem(10, 3)
1 
```

Enter fullscreen mode Exit fullscreen mode

# 列表的加减运算

在列表中添加其他列表的要素时使用运算符[`++/2`](https://hexdocs.pm/elixir/Kernel.html#++/2)，扣除时使用[`--/2`](https://hexdocs.pm/elixir/Kernel.html#--/2)。

```
iex> list ++ ["Cherry"]
[3.14, :pie, true, "Apple", "Cherry"]
iex> ["π"] ++ list
["π", 3.14, :pie, true, "Apple"]
iex> list -- [true, false]
[3.14, :pie, "Apple"] 
```

Enter fullscreen mode Exit fullscreen mode

# 字符串的运算

连接字符串的运算符是`<>`。

```
iex> name = "alice"
iex> "hello " <> name
"hello alice" 
```

Enter fullscreen mode Exit fullscreen mode

原子可以通过用`#{}`括起来并进行字符串插补，包含在字符串中。

```
iex> "hello #{:world}"
"hello world" 
```

Enter fullscreen mode Exit fullscreen mode

# 逻辑和与逻辑积及否定

逻辑和`||`和逻辑积`&&`的运算符将左操作数(被运算符)作为逻辑值进行评估。 在 Elixir 中，除了`false`和`nil`之外的值被评价为`true`。 每个运算符返回的值如下两个表 001 和 002 所示。

#### Table 001■ Theory and || Calculator

| 评估左操作数 | 返回的值 |
| --- | --- |
| `true` | 左操作数的值 |
| `false` | 右操作数的值 |

#### Table 002■ Theory and & & Calculator

| 评估左操作数 | 返回的值 |
| --- | --- |
| `true` | 右操作数的值 |
| `false` | 左操作数的值 |

```
iex> -1 || true
-1
iex> false || 2
2
iex> nil && 3
nil
iex> true && 4
4 
```

Enter fullscreen mode Exit fullscreen mode

否定的运算符`!`将操作数评估为逻辑值，然后返回反转的逻辑值。

```
iex> !true
false
iex> !1
false
iex> !nil
true 
```

Enter fullscreen mode Exit fullscreen mode

还有严密的逻辑运算符`or`和`and`以及`not`。 在这两种情况下，第一个操作数必须是逻辑值。

```
iex> is_atom(:example) and 1
1
iex> false or 1
1
iex> not is_number(false)
true
iex> 1 and true
** (BadBooleanError) expected a boolean on left-side of "and", got: 1
iex> not 1
** (ArgumentError) argument error
    :erlang.not(1) 
```

Enter fullscreen mode Exit fullscreen mode

如果逻辑和逻辑与运算符返回左操作数，则不执行右操作数。 例如，如果执行，将参数作为错误输出的[`raise/1`函数](https://hexdocs.pm/elixir/1.0.5/Kernel.html#raise/1)放在右操作数上的以下代码不会发生错误。

```
iex> false and raise("実行されればエラー")
false
iex> true || raise("実行されればエラー")
true 
```

Enter fullscreen mode Exit fullscreen mode

# comparison

用于比较值的大小、等于或不等于的运算符如下:

*   `==`:等于
*   `!=`:不等于
*   `<=`: the following
*   `>=`: above
*   `<`:小
*   `>`:大

```
iex> 1 > 2
false
iex> 1 != 2
true
iex> 1 == 1
true
iex> 1 <= 2
true 
```

Enter fullscreen mode Exit fullscreen mode

此外，还有严格等价`===`和不等价`!==`的运算符。

```
iex> 1 == 1.0
true
iex> 1 === 1.0
false 
```

Enter fullscreen mode Exit fullscreen mode

Elixir 可以比较不同类型的数据的大小。 因为在考虑数据排序的时候，就不用担心类型了。

```
iex> :hello > 999
true
iex> {:hello, :world} > [1, 2, 3]
false 
```

Enter fullscreen mode Exit fullscreen mode

根据数据类型的大小顺序如下(参照“[Term ordering](https://hexdocs.pm/elixir/operators.html#term-ordering) ”)。

```
number < atom < reference < function < port < pid < tuple < map < list < bitstring 
```

Enter fullscreen mode Exit fullscreen mode

#### Elixir 入门彩票

*   [Elixir 入门 01 :编写代码并尝试](https://dev.to/gumi/elixir-01--2585)
*   [Elixir 入门 02 :型的基本](https://dev.to/gumi/elixir-02--30n1)
*   Elixir 入门 03 :运算符基本知识
*   [Elixir 入门 04 :模式匹配](https://dev.to/gumi/elixir-04--1346)
*   [仙丹入門 05: 条件-情况/条件/条件](https://dev.to/gumi/elixir-05----casecondif-60o)
*   [Elixir 入门 06 :二进制和字符串以及字符列表](https://dev.to/gumi/elixir-06--35na)
*   [Elixir 入门 07 :关键词列表和地图](https://dev.to/gumi/elixir-07--39hi)
*   [Elixir 入门 08 :模块和函数](https://dev.to/gumi/elixir-08--1c4c)
*   [Introduction T0】 Elixir 09: Rethink](https://dev.to/gumi/elixir-09--1a0p)
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