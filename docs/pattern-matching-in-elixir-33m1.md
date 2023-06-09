# 仙丹中的模式匹配

> 原文：<https://dev.to/flatironschool/pattern-matching-in-elixir-33m1>

在熨斗学校，我们的任务是帮助人们学习如何编码。这意味着，作为工程团队的一员，我的工作几乎每天都在提醒我一个重要的普遍真理:学习新东西很难。

以学习演奏一种乐器为例，比如吉他。当你开始时，你有这些崇高的愿望。你想成为下一个大卫·鲍依。但是当你第一次开始的时候，这个梦想是如此的遥远。要达到这个目标需要付出大量的努力，而且很容易气馁。没有一些早期的胜利，你可能会放弃。

你需要学习一个让你着迷的很酷的重复片段，在那里你不想放下吉他，因为现在你已经融入其中了。

长生不老药也是同样的道理。

许多人对这种语言感到兴奋，因为使用它可以获得很多好处——并发性、容错性、可伸缩性——大肆宣传的事情不胜枚举。但是这些都不是你可以马上享受的事情。在你真正开始看到这些好东西之前，你必须构建并发布一个完整的应用程序。

你需要一个快速的胜利来让你继续，你需要那个很酷的片段。对我来说，这个很酷的片段就是模式匹配。

所以让我们来分解一下它是什么，为什么它如此伟大。

### 匹配符

要理解 Elixir 中的模式匹配，首先要重新思考将值绑定到变量的方式。拿声明`x = 1`来说。你可能把它理解为“x 等于 1”，这里我们把值`1`赋给变量`x`，对吗？

Welp，不在仙丹里。

在该语句中，`=`被称为“匹配操作符”，它不做任何赋值。相反，它评估右边的值*是否与左边的模式*匹配。如果匹配，那么这个值就被绑定到变量[ [1](#footnotes) ]。如果不是，则产生一个`MatchError`。

| x | = | one |
| --- | --- | --- |
| 模式 | 匹配运算符 | 价值 |

“匹配”是什么意思？这意味着右边的值与左边模式的形式和顺序相匹配。

### 简单的例子

让我们通过下面这些简单的例子来浏览一下模式匹配的基础知识。

#### 绑定在火柴上

```
x = 1 
```

这里，匹配结果为 true，因为右边的任何内容都将匹配一个空变量，所以左边的空变量被绑定到右边的值。

#### 匹配无绑定

```
x = 1
1 = x 
```

这两个语句都是有效的表达式，并且都匹配(！！！)

在最上面的表达式中，匹配结果为 true，值绑定到变量。在下面的表达式中，匹配结果为 true，但是没有绑定任何东西，因为变量只能绑定在`=`匹配操作符的左侧。例如，语句`2 = y`会抛出一个`CompileError`，因为`y`没有被定义。

#### 重新绑定

```
x = 1
x = 2 
```

如果你对一个绑定变量进行模式匹配，就像上面的`x`一样，如果匹配，它将被反弹。

#### 销符

```
 x = 1
^x = 2
#=> ** (MatchError) no match of right hand side value: 2 
```

如果你不希望变量在匹配时被反弹，使用`^` [引脚操作符](https://elixir-lang.org/getting-started/pattern-matching.html#the-pin-operator)。pin 运算符通过强制严格匹配变量的现有值来防止变量被反弹。

#### 列表

```
iex(1)> [a, b, c] = [1, 2, 3]
iex(2)> a
#=> 1
iex(3)> b
#=> 2
iex(4)> c
#=> 3 
```

我们可以在更复杂的数据结构上进行模式匹配，比如[列表](https://hexdocs.pm/elixir/List.html)。同样，任何左侧变量都将绑定到一个匹配上。

#### 列表`[head | tail]`格式

```
iex(1)> [head | tail] = [1,2,3,4]
iex(2)> head
#=> 1
iex(3)> tail
#=> [2,3,4] 
```

你可以对列表做的一件很酷的事情是头尾模式匹配。使用`|`语法将最左边的变量绑定到列表中的第一个元素，将剩余的元素绑定到最右边的变量(这些变量不必命名为`head`和`tail`；你可以选择任何你想要的名字)。

当您有一个想要逐个操作的元素列表时，这种语法非常方便，因为它允许您非常干净简洁地递归遍历列表。

```
iex(1)> list = [2,3,4]
iex(2)> [1 | list]
#=> [1,2,3,4] 
```

如果您喜欢，也可以使用这种语法将元素添加到列表中。

```
iex(1)> [first | rest] = []
#=> ** (MatchError) no match of right hand side value: [] 
```

不过，要小心空列表。如果在空列表上使用这个语法，将会引发一个`MatchError`,因为没有任何东西可以绑定任何一个变量。

#### 匹配错误

```
iex(1)> [x,y] = [4,5,6,7]
#=> ** (MatchError) no match of right hand side value: [4,5,6,7] 
```

请记住，如果您比较不同大小的列表，匹配将会失败。

```
iex(1)> [foo, bar] = {:foo, :bar}
#=> ** (MatchError) no match of right hand side value: {:foo, :bar} 
```

如果您尝试比较两种不同的数据结构，如列表和元组，匹配也会失败。

#### 元组

```
iex(1)> {a, b, c} = {1,2,3}
iex(2)> a
#=> 1
iex(3)> b
#=> 2
iex(4)> c
#=> 3 
```

使用[元组](https://hexdocs.pm/elixir/Tuple.html)进行模式匹配的操作与使用列表非常相似。

```
iex(1)> {:ok, message} = {:ok, "success"}
iex(2)> message
#=> "success"
iex(3)> {:ok, message} = {:error, "womp womp"}
#=> ** (MatchError) no match of right hand side value: {:error, "womp womp"} 
```

你将在 Elixir 中看到的一个常见模式是返回元组的函数，其中第一个元素是表示状态的[原子](https://elixir-lang.org/getting-started/basic-types.html#atoms)，如`:ok`或`:error`，第二个元素是[字符串](https://elixir-lang.org/getting-started/basic-types.html#strings)消息。

#### `_`下划线变量

```
iex(1)> {_, message} = {:ok, "success"}
iex(2)> message
#=> "success"
iex(3)> {_, message} = {:error, "bummer"}
iex(4)> message
#=> "bummer"
iex(5)> [ head | _ ] = [1,2,3,4]
iex(6)> head
#=> 1 
```

当您想进行模式匹配，但不在乎捕获任何值时，您可以使用`_`下划线变量。这个特殊的保留变量匹配一切；这是一个完美的总括。

```
iex(1)> {_, message} = {:ok, "success"}
iex(2)> _
#=> ** (CompileError) iex:2: unbound variable _ 
```

请注意`_`实际上是一个可丢弃的变量，因为你不能从中读取。如果你尝试，仙丹会抛出一个`CompileError`。

### 那又有什么大不了的？

也许你没有被上面的例子打动。Elixir 为模式匹配提供了一些很好的语法糖...但是这有什么突破性的呢？

[![Shania Twain That Don't Impress Me Much](img/70896842552353e28f962f3986d1d0cf.png "Shania Twain That Don't Impress Me Much")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EGCx8VL2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7vwlqech3gionk9wr50z.jpg)

让我们来看看一些实际的真实世界的应用。

### 真实世界的例子

我们将从大多数 web 开发人员可能都熟悉的一个问题开始:根据用户输入的数据显示面向公众的用户“显示名称”。

这是我最近在[Learn.co](https://learn.co)代码库中从事的工作。在我们的网站上，我们喜欢鼓励积极、友好的社区意识，所以我们在网站的很多地方显示用户的名字(根据用户自愿提供的信息构建)，包括[提问聊天功能](http://blog.flatironschool.com/troubleshooting-learn-co-closer-look-ask-question-feature/)。

[![Learn.co Ask a Question](img/888ac3bcd5b38500d7e097a63e028185.png "Learn.co Ask a Question")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zzoM7cJm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k1njvieocryrhjuyb6gc.png)

问题是，我们不要求用户给我们他们的全名，甚至设置一个用户名，所以当涉及到建立一个面向公众的显示名称时，不能保证任何“友好的”识别信息——名、姓或用户名——是可用的。此外，所有这些信息都是由用户手动输入的，虽然我们在保存之前对其进行了一定程度的清理，但奇怪的东西仍然可以通过。

为了解决这个问题，我们的产品团队提出了以下要求:

1.  如果用户提供了他们的名和姓，则同时显示他们的全名
2.  如果我们没有名字或姓氏，请检查用户是否提供了用户名，如果是，则显示用户名而不是全名
3.  如果我们没有以上任何一个，显示一个合理的通用默认值(这里，我们将只使用“新用户”)

[![Display Name Logic](img/4b2c688dd8bcc9953dad1533f3fb29f1.png "Display Name Logic")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--u5hcbWHJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s0lazciw766dk5bwk3fa.png)

我们如何用代码来表示这些条件呢？

#### Javascript 示例

用 [Javascript](https://developer.mozilla.org/en-US/docs/Web/JavaScript) 编写这个函数可能看起来像这样:*

```
export const displayName = (user) => {
  if (user.firstName.length > 0) {
    if (user.lastName.length > 0) {
      return `${user.firstName}  ${user.lastName}`.trim();
    } else {
      return `${user.firstName}`.trim();
    }
  } else if (user.username.length > 0) {
    return user.username;
  } else {
    return 'New User';
  }
} 
```

*我知道这些例子有些做作，但请耐心听我说。它们是为了说明的目的，而不是代码审查。

有很多因素使得这个函数很难一目了然。首先，Javascript 有大量标点符号的语法，如果你离开它一段时间，可能会觉得有点粗糙。所有嵌套的条件句也增加了复杂性和精神负担。此外，我们还做了一些零检查(通过`length`)和一些字符串清理。All-in-all，不是超级可读。

#### 红宝石示例

如果我们改用被誉为“开发者友好”的语言 [Ruby](https://www.ruby-lang.org/en/) ，情况不会有太大改善。

```
def display_name(user)
  if user.first_name.length > 0
    if user.last_name.length > 0
      "#{user.first_name}  #{user.last_name}".strip
    else
      "#{user.first_name}".strip
    end
  elsif user.username.length > 0
    user.username
  else
    'New User'
  end
end 
```

我们仍然有嵌套的条件句，这个长而“尖”的方法显然没有通过 Sandi Metz 的“斜视测试”。

#### 仙丹例子

让我们看看长生不老药能不能让我们过得更好。

```
defmodule Account do
  def display_name(%{first: first, last: last}) do
    String.trim("#{first}  #{last}")
  end

  def display_name(%{username: username}), do: "#{username}"

  def display_name(_), do: “New User”
end 
```

在这里，每个条件都被分离到它自己的函数子句中。与 Ruby 等其他语言不同，当我们像这样“重载”一个函数时(例如，用相同的函数名进行多个函数声明)，我们并没有覆盖原始函数。相反，这些被称为多子句函数，当你调用一个有几个子句的函数时，它会尝试每个子句(从文件的顶部开始，向下移动)，直到找到一个匹配的子句。

你应该把你最具体的子句放在最上面，因为那些子句最先匹配。如果你把一些过于笼统的东西放在顶部，那么它会匹配所有的东西，而它下面的子句不会被命中。幸运的是，Elixir 非常酷，如果你犯了这个错误，它通常会发出警告。

多子句函数允许我们将条件逻辑分解成最小的、原子的部分，从而保持它的独立性、封装性和可读性。很容易一眼就看出这些功能子句在做什么。

#### 处理不愉快的道路

但是你可能已经注意到我们这里的长生不老药的例子有一点不公平的优势。Ruby 和 Javascript 示例中增加的大部分复杂性来自于处理`nil`案例，而我们在 Elixir 示例中根本没有检查这些。

您可能想在第一个`display_name/1` function 子句中加入一个`case`语句(这里有更多关于函数`name/arity`语法[的内容](https://elixirschool.com/en/lessons/basics/functions/#function-naming-and-arity))。不过，你会想要抵制，因为`case`语句不是灵丹妙药。

您的下一个想法可能是尝试在文件的顶部添加更多更高特异性的子句:

```
defmodule Account do
  # Unwieldy nil checks
  def display_name(%{first: nil, last: nil, username: nil}), do: display_name(%{})
  def display_name(%{first: nil, last: nil, username: username}) do
    display_name(%{username: username})
  end
  def display_name(%{first: nil, last: nil}), do: display_name(%{})

  # Happy paths
  def display_name(%{first: first, last: last}), do: do_trim("#{first}  #{last}")
  def display_name(%{username: username}), do: "#{username}"
  def display_name(_), do: “New User”
end 
```

然而，正如你所看到的，这会变得很快。今天，我们在三个领域检查 nils，但是如果需求改变了呢？考虑到我们需要检查的 User 上所有可能字段的可能排列，您可能会得到一个超长、臃肿的模块。

改做什么？长生不老药在这方面也是我们的后盾。

#### 守护条款

```
defmodule Account do
  def display_name(%{first: first, last: last}) when not is_nil(first) do
    String.trim("#{first}  #{last}")
  end

  def display_name(%{username: username}) when not is_nil(username) do
    "#{username}"
  end

  def display_name(_), do: "New User"
end 
```

Elixir 函数声明支持 [guard 子句](https://hexdocs.pm/elixir/master/guards.html)，这是一个用更复杂的检查来增加模式匹配的便利工具。保护子句是匹配更复杂模式的好方法，不会给函数增加太多混乱。只有少数[表情](https://hexdocs.pm/elixir/master/guards.html#list-of-allowed-expressions)被支持，它们应该简短而甜蜜。

在上面的代码块中，我们已经在前两个子句中添加了`not is_nil()`保护。多亏了 guard 子句，我们只需要添加几个额外的字符来防止零值。

#### 自定义守卫条款

让我们再来一个曲球。对于显示名称，我们还需要防范另一种情况，那就是用户给了我们他们的全名，但是它包含了[个人识别信息(PII)](https://en.wikipedia.org/wiki/Personally_identifiable_information) 。

这种情况实际上在 Learn.co 经常发生。出于某种原因，在我们免费的公开训练营课程注册页面上，用户通常会在全名栏中输入他们的电子邮件。

[![Learn.co Bootcamp Prep course sign up form](img/e86bc9407db6094d408549d3fc34302a.png "Learn.co Bootcamp Prep course sign up form")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NzgEYHYe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m6ujk4njij5rfkpbhkis.png)

显然，我们需要对这个 UI 做一些改变(并在用户输入上添加更多的验证，但这是一篇单独的博客文章)。然而，由于坏数据的存在，我们需要防止它，我们可以通过一些更复杂的模式匹配技术来做到这一点。

到目前为止，我们的`display_name/1`函数子句看起来是这样的:

```
defmodule Account do
  def display_name(%{first: first, last: last}) when not is_nil(first) do
    String.trim("#{first}  #{last}")
  end

  def display_name(%{username: username}) when not is_nil(username) do
    "#{username}"
  end

  def display_name(_), do: "New User"
end 
```

你可能会问自己，现在我们是不是终于放弃了模式匹配，而只是在第一个 function 子句的主体中添加了一些逻辑？惊讶(不惊讶)——答案是否定的，我们还没有用尽 Elixir 的模式匹配工具箱。

除了预定义的 guard 子句表达式，Elixir 还支持[自定义 guard 子句](https://hexdocs.pm/elixir/master/guards.html#defining-custom-guard-expressions)。现在“自定义”并不意味着你可以在其中加入任何功能；自定义保护子句仍然必须从允许的表达式的有限列表中构建。但是它们对于保持事物的干燥和简单仍然非常方便。

你可以用宏创建自定义的保护，但是文档建议用`defguard`或`defguardp`来定义它们，因为它们执行“额外的编译时检查”(这听起来不错)。

```
# Not recommend: macros
defmodule Account.Guards do
  defmacro is_private(first_name, email) do
    quote do
      not(is_nil(unquote(first_name))) and
      not(unquote(email) == unquote(first_name))
    end
  end
end

# Recommended: defguard
defmodule Account.Guards do
  defguard is_private(first_name, email) when not(is_nil(first_name)) and not(email == first_name)
end 
```

现在我们可以在模块的顶部再添加一个函数子句来满足我们的 PII 需求。

```
defmodule Account do
  import Account.Guards, only: [is_private: 2]

  def display_name(%{first: first, last: last, email: email}) when is_private(first, email) do
    “<<Redacted>>”
  end

  def display_name(%{first: first, last: last}) when not is_nil(first) do
    String.trim("#{first}  #{last}")
  end

  def display_name(%{username: username}) when not is_nil(username) do
    "#{username}"
  end

  def display_name(_), do: "New User"
end 
```

#### 总结起来

由于模式匹配和多子句函数的强大功能，我们现在有了清晰、干净和有效的代码来处理用户名的显示。随着新需求的出现，我们不需要接触任何现有的方法。我们可以根据需要简单地添加新的子句。

```
defmodule Account do
  import Account.Guards, only: [is_private: 2]

  # function heads only

  def display_name(%{first: first, last: last, email: email}) when is_private(first, email)
  def display_name(%{first: first, last: last}) when not is_nil(first)
  def display_name(%{username: username}) when not is_nil(username)
  def display_name(_)
end 
```

### 外卖

正如在开始时提到的，在 Elixir 中使用模式匹配需要您有一点不同的想法——但这种不同是好的方面。语言的设计方式——它所包含的范例，它所支持的功能——鼓励你遵循一般的编程最佳实践。模式匹配就是最好的例子之一。

以多子句函数的模式匹配为例。通过支持这一点，Elixir 引导您编写小的声明性函数——只做一件事的短函数，例如遵循[单一责任原则](https://en.wikipedia.org/wiki/Single_responsibility_principle)的函数。

同样，通过声明您想要匹配的模式，您发出了一个关于您期望接收什么输入的明确信号。默认情况下，您的代码变得更加自文档化。

另外，由于模式匹配在语言中是不确定的，一旦你掌握了这个概念，你就可以掌握它。这是探索围绕这一核心概念构建的 Elixir 中所有其他神奇事物的完美起点，如[发电机](https://elixir-lang.org/getting-started/mix-otp/genserver.html)、[插头](https://elixirschool.com/en/lessons/specifics/plug/)...这个清单还在继续。

总而言之，Elixir 鼓励你编写 1)声明性的 2)自文档化的 3)良好范围的代码。它将帮助你成为一名更强的程序员，并使你成为一名真正的 rockstar Elixir 开发者。

真令人印象深刻。

[![That Does Impress Me Much](img/895ce13b46224adcdda8bccabbc7a5e9.png "That Does Impress Me Much")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1LNBXj31--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ximup82et6hxwdcwbnaj.gif)

有什么问题吗？请在下面的评论中留下它们。感谢阅读！

想在一个团队中工作，在 Elixir 中开发很酷的东西吗？[熨斗学校正在招聘！](http://flatironschool.com/careers)

关于我们团队最近开发的更酷的东西的例子，请查看我们新推出的[数据科学训练营预备课程](https://flatironschool.com/programs/free-data-science-bootcamp-prep/?utm_campaign=Sponsored_Content&utm_source=Dev.to&utm_medium=DSBCP)，该课程以仙丹支持的 [Jupyter 笔记本](https://jupyter.org/)集成为特色。

### 资源

#### 读数:

*   仙丹文档:[模式匹配](https://elixir-lang.org/getting-started/pattern-matching.html)
*   仙丹派:[模式匹配](https://elixirschool.com/en/lessons/basics/functions/#pattern-matching)
*   安娜·奈兹伯格，[《仙丹中的模式匹配:要记住的五件事》](https://blog.carbonfive.com/2017/10/19/pattern-matching-in-elixir-five-things-to-remember/)

#### 视频:

*   若昂·冈萨尔维斯，[“药剂入门:模式匹配与赋值”](https://www.youtube.com/watch?v=zwPqQngLn9w&index=6&list=PLCFmW8UCDqfCA9kpbFirPEDoQYc9nCy_W)
*   迪夫·托马斯，[与众不同(ElixirConf2014 主题演讲)](https://www.youtube.com/watch?v=5hDVftaPQwY)
*   兰斯·哈尔沃森[《自信的灵丹妙药》(ElixirConf 2015)](https://www.youtube.com/watch?v=E-3G7g0Dm7c)

#### 教程:

*   代码学校，[尝试仙丹模式匹配](http://campus.codeschool.com/courses/try-elixir/level/3/section/1/pattern-matching)

### 脚注

[1]绑定与转让

变量绑定和变量赋值之间的区别很小，但是当涉及到 Elixir 中的模式匹配时就很关键了。对于任何熟悉 Erlang 的读者来说，上面所有的绑定和重新绑定变量可能看起来很奇怪。在 Erlang 中，变量是不可变的，由于 Elixir 构建在 Erlang VM 之上，因此变量在 Elixir 中也是不可变的。

如果变量是不可变的，那么为什么我们可以通过模式匹配将值绑定到变量上？

我们必须下降到机器级内存管理来得到答案。赋值将数据分配到内存中的某个位置，所以重新给变量赋值会改变数据的位置。绑定创建了对内存中某个位置的引用，所以重新绑定只是改变了引用，而不是数据本身。

把变量想象成一个手提箱。绑定变量就像在行李箱上贴标签一样。赋值就像换出内容[ [源](https://stackoverflow.com/a/48103225/3880374) ]。

关于更多的上下文，Elixir 的创造者 José Valim 在[上发表了一篇比较 Elixir 和 Erlang 变量](http://blog.plataformatec.com.br/2016/01/comparing-elixir-and-erlang-variables/)的文章。