# 区分使用数据结构- Elixir 图表- [翻译文章]

> 原文：<https://dev.to/nabbisen/-elixir---508k>

本文是以下文章的翻译:
[Which Data Structure Should I Use？ An Elixir Cheat Sheet](https://dev.to/talum/which-data-structure-should-i-use-an-elixir-cheat-sheet-3epe) by [Tracy Lum](https://dev.to/talum)
<small>*在笔者许可的基础上刊登。</small>

* * *

作为 Elixir 的初学者，我有一件正在考虑的事情。
开始 Elixir 时，最辛苦的事情之一就是理解应该使用哪个数据结构。
我所属的团队最近开始努力用 Elixir 做所有的事情。
因此，我现在正式重新学习。
但是，在读取团队代码的过程中经常会遇到的事情，有时甚至很难理解自己现在看到的东西。
Elixir 的语法与 Ruby 非常相似。
(对我来说是非常熟悉的语言。 )
但是其模式、规章和数据结构，*略有不同*。
我脑子里明白那件事。
因为 Elixir 不是面向对象的语言，而是函数型的语言:
你在 Ruby 中使用对象的场景中，Elixir 将会改为使用流程的生成。

暂且不论，我现在正在学习 Elixir。
其中，我认为如果有以下内容应该会很有帮助。
即总结了在调查图表和作为 Ruby 使用者的 Elixir 的过程中注意到的、关于数据结构的不同点。

## 数据类型

如果您已经学习了 Ruby (或者许多其他的程序语言)，整数型/浮点数型/范围(范围) /正则表达式一定都很熟悉吧。
幸运的是，这些都存在于 Elixir 中。
虽然有很小的差异，但是碰到它们的事情，在我的经验中还不多。

阿童木就像 Ruby 的象征。
以冒号开始，其名称为原子的值。
例如`:hello`就是 Elixir 中的正经阿童木。
有时会作为标签的值使用。

Elixir 也有字符串。
字符串必须用双引号括起来。
另一方面，字符列表用单引号括起来。
字符串是二进制数据，但字符列表实际上只是代码点列表。
我至今为止几乎没有使用过文字列表。

现在，让我们快速看看以上类型的表现。

```
iex> 2         # integer            : 整数型
iex> 2.0       # floating point     : 浮動小数点数型
iex> false     # boolean            : 真偽値
iex> 1..4      # range              : 範囲
iex> ~r/hello/ # regular expression : 正規表現
iex> :hello    # atom               : アトム
iex> "world"   # string             : 文字列
iex> 'world'   # charlist           : 文字のリスト 
```

Enter fullscreen mode Exit fullscreen mode

Elixir 中除了这些以外还存在数据类型。
端口(`Port`)和`PID`。
用于过程的交流。
是 Erlang VM 上可用的实体。

### 端口

端口(`Port`)用于与 APP 应用程序外部的资源进行交换(读写)。
经常启动操作系统进程并与它们进行交互。
例如打开端口执行 OS 的`echo`等命令的情况。

要打开端口并向对方发送消息:

```
iex> port = Port.open({:spawn, "echo sup"}, [:binary])
#Port<0.1305> 
```

Enter fullscreen mode Exit fullscreen mode

之后执行 IEx.Helpers 模块的`flush()`，可以输出从端口接收到的消息。

```
iex> port = Port.open({:spawn, "echo sup"}, [:binary])
#Port<0.1305>
iex> flush()
iex> {#Port<0.1305>, {:data, "sup\n"}}
iex> :ok 
```

Enter fullscreen mode Exit fullscreen mode

可以将任何表示形式的二进制数据发送到端口并执行。
例如，试着从我的 jekyll 博客的目录中，建立`iex`会话，打开端口，然后发送`bundel install`命令。
Ruby 在 gem 中的依存关系全部安装好了。
这是输出内容的一部分。

```
iex> port = Port.open({:spawn, "bundle install"}, [:binary])
#Port<0.1306>
iex> flush()
{#Port<0.1306>, {:data, "Using concurrent-ruby 1.0.5\n"}}
{#Port<0.1306>, {:data, "Using i18n 0.9.5\n"}}
{#Port<0.1306>, {:data, "Using minitest 5.11.3\n"}}
{#Port<0.1306>, {:data, "Using thread_safe 0.3.6\n"}}
{#Port<0.1306>, {:data, "Using tzinfo 1.2.5\n"}}
{#Port<0.1306>, {:data, "Using activesupport 4.2.10\n"}}
{#Port<0.1306>, {:data, "Using public_suffix 2.0.5\n"}}
{#Port<0.1306>, {:data, "Using addressable 2.5.2\n"}}
{#Port<0.1306>, {:data, "Using bundler 1.16.2\n"}}
{#Port<0.1306>, {:data, "Using coffee-script-source 1.11.1\n"}}
{#Port<0.1306>, {:data, "Using execjs 2.7.0\n"}}
{#Port<0.1306>,
 {:data,
  "Bundle complete! 4 Gemfile dependencies, 85 gems now installed.\nUse `bundle info [gemname]` to see where a bundled gem is installeそれから d.\n"}}
:ok 
```

Enter fullscreen mode Exit fullscreen mode

### PID

`PID`是对进程的引用。
生成新进程时，总是会返回新的 PID。
关于 PID，有很多想说的事情。
为了能够向其他进程发送消息，必须花时间理解 PID 吧。

以下是接收生成进程后返回的 PID 的示例。

```
iex> pid = spawn fn -> IO.puts("hello world") end
iex> hello world
iex> #PID<0.123.0> 
```

Enter fullscreen mode Exit fullscreen mode

过程一完成工作就死去。
PID 和端口可以保证不受外部影响地工作。
但是目前，我认为只要意识到它们的存在就足够了。

那么，这是追记了新出现的模具的、基本的图表。

# Elixir 的数据类型的图表

```
iex> 2             # integer            : 整数型
iex> 2.0           # floating point     : 浮動小数点数型
iex> false         # boolean            : 真偽値
iex> 1..4          # range              : 範囲
iex> ~r/hello/     # regular expression : 正規表現
iex> :hello        # atom               : アトム
iex> "world"       # string             : 文字列
iex> 'world'       # charlist           : 文字のリスト
iex> #Port<0.1306> # port               : ポート
iex> #PID<0.123.0> # pid                : PID 
```

Enter fullscreen mode Exit fullscreen mode

在我看来，要真正与 Elixir 合作，需要的不仅仅是这个。
需要了解如何使这些基本数据类型可以用作结构。
那么，就让我们来看看各种各样的收藏型以及使用它们的各自理由吧。

## 收藏型

您将看到的收藏类型如下:

*   元组
*   列表
*   关键字列表
*   地图
*   Tectonic body

这些话，一定至少在过去的某个地方听过一次吧。
但是，如果你熟悉 Ruby 的话，一定会对为什么所有这些扩展了收藏型的东西都需要产生疑问。
让我们仔细看看吧。

### 元组

元组是具有顺序的值的集合。
表现如下:

```
iex> {:hello, "world"}
iex> {1, 2}
iex> {:ok, "this is amazing!", 2}

# タプルなのかどうかをチェックできます
iex> tuple = { "hello", "world"}
iex> is_tuple tuple
iex> true

# インデックスを指定することにより、タプルから要素を取り出すことができます
iex> elem(tuple, 1)
iex> "world" 
```

Enter fullscreen mode Exit fullscreen mode

在我看来，元组有点原始。
是因为看起来像应该成为散列的东西。
另一方面，也有表现出类似 Ruby 排列的行为。
尽管如此，还是被称为“元组”(<数学>组，顺序组)！
我几次在混乱的最后都在对自己说，习惯了这些事情的话应该就不会在意了吧。

元组会出现在 Elixir 的所有地方。
函数的返回值有时会变成元组。
在这种情况下可以进行模式匹配。
因为有这样的事情，所以我理解通过元组可以看到 Elixir 的世界。
元组通常具有 2~4 个元素。
目前，元组是我喜欢的数据结构。
处理元素多于 4 个的数据结构时，大体上来说，应该不用元组，而用映射和结构体比较好。

### 列表

列表是由链接连接的数据结构。
表现如下:

```
iex> [1, 2, 3, 4]
iex> ["hello", "world"] 
```

Enter fullscreen mode Exit fullscreen mode

你可能觉得是 Ruby 的数组。
但是在 Elixir 上是列表！
列表作为用链接连接的数据结构安装。
因此适合递归处理。
相反，不适合随机获取要素或计算列表长度。
为什么这么说，是因为为了掌握列表的大小，需要扫描整个列表。
到目前为止，我大多数情况下都是用元组而不是列表来的。
我想，如果必须在这两者中选择一方，就必须在考虑集合的大小会是多少的基础上，研究什么样的操作才能取得良好的性能吧。

### 关键词列表

为了应对更复杂的问题，Elixir 还有一个关键词列表。
这本质上是由具有 2 个值的元组组成的列表。

```
# キーワードリスト
iex> [ phrase: "oh hello", name: "tracy" ]

# 実際には 2 つの値を持つタプルたちです
iex> [ {:phrase, "oh hello"}, {:name, "tracy"} ] 
```

Enter fullscreen mode Exit fullscreen mode

我意识到这是一般的存在方式，但这件事至今仍让人困惑。
关键字列表的优点在于允许重复同一个键。

```
iex> keyword_list = [food: "peanut butter", food: "ice cream", flavor: "chocolate"] # まっとうなキーワードリストです 
```

Enter fullscreen mode Exit fullscreen mode

关键字列表非常适合在命令行中作为参数或选项使用。

### 图

下一个轮到地图了。
如果想使用真正的关键价值商店，不是由关键和价值组成的列表，而是地图能实现希望的东西。
是与 Ruby 的哈希有点相似的表现:

```
iex> %{"greeting" => "hello", "noun" => "world"}
iex> %{:greeting => "hello", :noun => "world"}
iex> %{greeting: "hello", noun: "world"} # キーがアトムである場合、ハッシュロケット（=>）を省くことができます

iex> greeting = %{spanish: "hola", chinese: "ni hao", english: "hello"}
iex> greeting[:spanish]
iex> "hola"
iex> greeting.chinese
iex> "ni hao" 
```

Enter fullscreen mode Exit fullscreen mode

地图适用于发送关联数据。
而且，非常适合处理超过适合用元组处理的大小的任何东西。

### Structure

结构就像一个扩展的地图。
键的格式是严格规定的，必须是阿童木。
结构必须在模块中与适当的默认值一起定义。
可以说是有规则的地图。

```
iex> defmodule IceCream do
....   defstruct flavor: "", quantity: 0
.... end

iex> chocolate = %IceCream{flavor: "chocolate"}
iex> chocolate.flavor
iex> "chocolate" 
```

Enter fullscreen mode Exit fullscreen mode

结构与贴图一样，由百分号`%`定义。 但是，对于结构，符号后面是模块的名称。
看到这个格式，就会切身感受到结构体只不过是规则变得严格的地图。

在 Elixir 的过去的版本中也存在过`HashDict`这个东西。
用于在地图上操作超过数百个值。
但是这个模块因为有旧的好的`Map`，所以不推荐使用。

到此为止。
我们看到了在 Elixir 中可以看到的一般数据类型和集合类型。
语言变化的话，会出现很多随心所欲的地方，但也有共同点。 需要学习的东西当然有很多。 关于 Elixir、规章、用 Elixir 能实现的一些很棒的事情。
但是(在我看来)从以上内容开始，在熟悉语言方面不是更好吗？
你接下来可能会遇到关于 Elixir 的难解的事情。
如果这篇文章能成为解决它们的好路标，那就太好了！

## Elixir 的收藏型的图表

```
iex> {:ok, "this is amazing!", 2}                                         # tuple        : タプル
iex> ["hello", "world"]                                                   # list         : リスト
iex> [ phrase: "oh hello", name: "tracy" ]                                # keyword list : キーワードリスト
iex> greeting = %{spanish: "hola", chinese: "ni hao", english: "hello"}   # map          : マップ
iex> chocolate = %IceCream{flavor: "chocolate"}                           # struct       : 構造体 
```

Enter fullscreen mode Exit fullscreen mode

## References

*   [基本类型](https://elixir-lang.org/getting-started/basic-types.html)@ Elixir 语言的公式 HP
*   [编程 Elixir](https://pragprog.com/book/elixir16/programming-elixir-1-6) by Dave Thomas
*   [Hex 文档](https://hexdocs.pm/elixir/)

* * *

谢谢您的阅读。

本記事は、以下の記事の翻訳です:
[我应该使用哪种数据结构？仙丹秘籍](https://dev.to/talum/which-data-structure-should-i-use-an-elixir-cheat-sheet-3epe)作者[特雷西·卢姆](https://dev.to/talum)

致 Tracy:非常感谢你允许我翻译你的帖子。