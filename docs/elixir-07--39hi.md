# Elixir 入门 07 :关键字列表和地图

> 原文：<https://dev.to/gumi/elixir-07--39hi>

本文在 Elixir 官方网站的许可下，根据“[Keyword lists and maps](https://elixir-lang.org/getting-started/keywords-and-maps.html) ”的解说，进行修改，对将 Elixir 的键值关联起来的数据结构进行说明。

作为将值与键相关联的数据结构，Elixir 提供了关键字列表和映射。 与其他语言中称为字典、散列或关联数组等的数据结构类似。

# 关键词列表

许多函数式编程语言用两个项的元组表示键和值的数据结构。 在 Elixir 中，第一个项目或键中使用原子的两个项目的元组列表称为关键字列表。 关键词列表用`[キー: 値]`的语法写会很方便。 数据与使用元组的基本语法`[{キー: 値}]`时相同。

```
iex> list = [{:a, 1}, {:b, 2}]
[a: 1, b: 2]
iex> list == [a: 1, b: 2]
true 
```

Enter fullscreen mode Exit fullscreen mode

关键字列表可以作为列表进行操作。 例如，想添加其他关键字列表的要素时使用的是`++/2`运算符。

```
iex> list ++ [c: 3]
[a: 1, b: 2, c: 3]
iex> [a: 0] ++ list
[a: 0, a: 1, b: 2] 
```

Enter fullscreen mode Exit fullscreen mode

当列表中有相同的键时，键的引用将检索位置在前的元素。

```
iex> new_list = [a: 0] ++ list
[a: 0, a: 1, b: 2]
iex> new_list[:a]
0 
```

Enter fullscreen mode Exit fullscreen mode

关键字列表中的键具有以下三个特征:

*   是阿童木
*   被排序
*   也可以不是唯一的

例如，Elixir 的库[Ecto](https://hexdocs.pm/ecto/Ecto.html) 提供了一个很好的 DSL，可以用关键字列表写数据库查询。

```
query = from w in Weather,
      where: w.prcp > 0,
      where: w.temp < 20,
     select: w 
```

Enter fullscreen mode Exit fullscreen mode

在 Elixir 中，关键字列表是将选项传递给函数的标准机制。 `if/2`宏的以下语法是示例(请参见“[Elixir 入门 05 :条件- case/cond/if](https://dev.to/gumi/elixir-05----casecondif-60o) ”“do/end 块”)。

```
iex> if(false, do: :this, else: :that)
:that 
```

Enter fullscreen mode Exit fullscreen mode

`:do`和`:else`是一个关键字列表的要素。 因此，改写如下。 一般来说，关键词列表为函数的最后一个参数时，可以省略方括号`[]`。

```
iex> if(false, [do: :this, else: :that])
:that 
```

Enter fullscreen mode Exit fullscreen mode

当然，也可以写如下。

```
iex> if(false, [{:do, :this}, {:else, :that}])
:that 
```

Enter fullscreen mode Exit fullscreen mode

关键字列表也可以使用模式匹配。 但是，要素的数量及其顺序也必须匹配。 因此，实际上很少被使用吧。

```
iex> [a: a] = [a: 1]
[a: 1]
iex> a
1
iex> [a: a] = [a: 1, b: 2]
** (MatchError) no match of right hand side value: [a: 1, b: 2]
iex> [b: b, a: a] = [a: 1, b: 2]
** (MatchError) no match of right hand side value: [a: 1, b: 2] 
```

Enter fullscreen mode Exit fullscreen mode

为了操作关键词列表，Elixir 配备了[`Keyword`模块](https://hexdocs.pm/elixir/Keyword.html)。 但是，请注意，关键字列表是列表。 关于性能，需要考虑与列表相同的因素。 查找密钥和计算元素的过程中，列表越长时间越长。 因此，关键字列表主要用于传递选项值。 如果要容纳很多元素，或者想要使密钥唯一，最好使用地图。

# 图

作为在 Elixir 中存储键值对的数据结构，推荐使用映射。 地图用[`%{}/2`](https://hexdocs.pm/elixir/Kernel.SpecialForms.html#%25%7B%7D/1)的句法制作。

```
iex> map = %{:a => 1, 2 => :b}
%{2 => :b, :a => 1}
iex> map[:a]
1
iex> map[2]
:b
iex> map[:c]
nil 
```

Enter fullscreen mode Exit fullscreen mode

与列表相比，映射键具有以下特征:

*   可以使用任何数据类型
*   不问顺序

与关键字列表不同，模式匹配在地图中非常有用。 地图的模式与子集匹配。 也就是说，只要匹配图案中包含的键就可以了。 因此，空贴图`%{}`将匹配所有贴图。

```
iex> %{} = %{:a => 1, 2 => :b}
%{2 => :b, :a => 1}
iex> %{:a => a} = %{2 => :b, :a => 1}
%{2 => :b, :a => 1}
iex> a
1
iex> %{:c => c} = %{:a => 1, 2 => :b}
** (MatchError) no match of right hand side value: %{2 => :b, :a => 1} 
```

Enter fullscreen mode Exit fullscreen mode

变量也可以用于参照、模式匹配或添加到地图的键。

```
iex> n = 1
1
iex> map = %{n => :one}
%{1 => :one}
iex> map[n]
:one
iex> %{^n => :one} = %{1 => :one, 2 => :two, 3 => :three}
%{1 => :one, 2 => :two, 3 => :three} 
```

Enter fullscreen mode Exit fullscreen mode

模块[`Map`](https://hexdocs.pm/elixir/Map.html)具有与`Keyword`相似的便利函数。 以下代码是使用函数[`Map.get/3`](https://hexdocs.pm/elixir/Map.html#get/3)(第 3 参数为默认值`nil`)和[`Map.put/3`](https://hexdocs.pm/elixir/Map.html#put/3)以及[`Map.to_list/1`](https://hexdocs.pm/elixir/Map.html#to_list/1)的示例。

```
iex> map = %{:a => 1, 2 => :b}
%{2 => :b, :a => 1}
iex> Map.get(map, :a)
1
iex> Map.put(map, :c, 3)
%{2 => :b, :a => 1, :c => 3}
iex> Map.to_list(map)
[{2, :b}, {:a, 1}] 
```

Enter fullscreen mode Exit fullscreen mode

使用`|`的以下语法可以改变地图的键的值。 但是，地图上必须已经有密钥。 这意味着，此语法不会添加新的键。

```
iex> map = %{:a => 1, 2 => :b}
%{2 => :b, :a => 1}
iex> %{map | 2 => :two}
%{2 => :two, :a => 1}
iex> %{map | :c => 3}
** (KeyError) key :c not found in: %{2 => :b, :a => 1}
    (stdlib) :maps.update(:c, 3, %{2 => :b, :a => 1})
    (stdlib) erl_eval.erl:255: anonymous fn/2 in :erl_eval.expr/5
    (stdlib) lists.erl:1263: :lists.foldl/3 
```

Enter fullscreen mode Exit fullscreen mode

按键时请使用`Map.put/3`函数。

```
iex> map = %{:a => 1, 2 => :b}
%{2 => :b, :a => 1}
iex> Map.put(map, :c, 3)
%{2 => :b, :a => 1, :c => 3} 
```

Enter fullscreen mode Exit fullscreen mode

键重复时，会被后面的值复盖。

```
iex> %{:a => 1, 2 => :b, :a => :one}
%{2 => :b, :a => :one} 
```

Enter fullscreen mode Exit fullscreen mode

如果贴图中的所有键都是原子，则输出如下:

```
iex> %{:a => 1, :b => 2}
%{a: 1, b: 2} 
```

Enter fullscreen mode Exit fullscreen mode

而且，这个简单的语法在制作地图时也可以使用。 另外，原子的键也可以在点`.`上参照。

```
ie> map = %{:a => 1, 2 => :b}
%{2 => :b, :a => 1}
iex> map.a
1
iex> map.2
** (SyntaxError) iex:15: syntax error before: "2"
iex> map[2]
:b
iex> map.c
** (KeyError) key :c not found in: %{2 => :b, :a => 1} 
```

Enter fullscreen mode Exit fullscreen mode

比起`Map`模块的函数，Elixir 的开发者似乎更喜欢通过`map.field`的句法和模式匹配来处理地图。 因为可以用清晰易懂的风格进行编程吧(参照“[writing assertive code with elixir](http://blog.plataformatec.com.br/2014/09/writing-assertive-code-with-elixir/)”)。

# 嵌套数据结构

地图和关键字列表可以分别嵌套。 也可以将地图和关键字列表相互嵌套。 值是通过组合各自的语法而得到的。

```
iex> users = [
...>   john: %{name: "John", age: 27, languages: ["Erlang", "Ruby", "Elixir"]},
...>   mary: %{name: "Mary", age: 29, languages: ["Elixir", "F#", "Clojure"]}
...> ]
[
  john: %{age: 27, languages: ["Erlang", "Ruby", "Elixir"], name: "John"},
  mary: %{age: 29, languages: ["Elixir", "F#", "Clojure"], name: "Mary"}
]
iex> users[:mary]
%{age: 29, languages: ["Elixir", "F#", "Clojure"], name: "Mary"}
iex> users[:john].age
27 
```

Enter fullscreen mode Exit fullscreen mode

改写值时使用[`put_in/2`](https://hexdocs.pm/elixir/Kernel.html#put_in/2)。

```
iex> users = put_in(users[:john].age, 31)
[
  john: %{age: 31, languages: ["Erlang", "Ruby", "Elixir"], name: "John"},
  mary: %{age: 29, languages: ["Elixir", "F#", "Clojure"], name: "Mary"}
]
iex> users[:john].age
31 
```

Enter fullscreen mode Exit fullscreen mode

[`update_in/2`宏](https://hexdocs.pm/elixir/Kernel.html#update_in/2)在将第 1 自变量的值传递给第 2 自变量的函数中处理。

```
iex> users = update_in(users[:mary].languages, fn languages ->
...>   List.delete(languages, "Clojure")
...> end)
[
  john: %{age: 31, languages: ["Erlang", "Ruby", "Elixir"], name: "John"},
  mary: %{age: 29, languages: ["Elixir", "F#"], name: "Mary"}
]
iex> users[:mary].languages
["Elixir", "F#"] 
```

Enter fullscreen mode Exit fullscreen mode

[`get_and_update_in/2`](https://hexdocs.pm/elixir/Kernel.html#get_and_update_in/2)
[`put_in/3`](https://hexdocs.pm/elixir/Kernel.html#put_in/3)
[`update_in/3`](https://hexdocs.pm/elixir/Kernel.html#update_in/3)
[`get_and_update_in/3`](https://hexdocs.pm/elixir/Kernel.html#get_and_update_in/3)

#### Elixir 入门彩票

*   [Elixir 入门 01 :编写代码并尝试](https://dev.to/gumi/elixir-01--2585)
*   [Elixir 入门 02 :型的基本](https://dev.to/gumi/elixir-02--30n1)
*   [Elixir 入门 03 :运算符的基本](https://dev.to/gumi/elixir-03--33im)
*   [Elixir 入门 04 :模式匹配](https://dev.to/gumi/elixir-04--1346)
*   [仙丹入門 05: 条件-情况/条件/条件](https://dev.to/gumi/elixir-05----casecondif-60o)
*   [Elixir 入门 06 :二进制和字符串以及字符列表](https://dev.to/gumi/elixir-06--35na)
*   Elixir 入门 07 :关键字列表和地图
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