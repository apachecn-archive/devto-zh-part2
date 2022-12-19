# 通过创建命令行应用程序了解 Elixir

> 原文：<https://dev.to/jorinvo/learn-elixir-by-creating-a-command-line-application-3766>

我想分享我通过编写一个简单的命令行应用程序
开始使用 Elixir 的经验，并介绍基本的 Elixir 项目的设置。

最近我迷上了长生不老药。这是一种建立在坚如磐石的基础上的很好的小语言:BEAM - Erlang 的虚拟机。
不仅仅是语法，它有着 Ruby 风格的外观，看起来很舒服，可用的文章和工具也用简单的说明和直观的界面欢迎你。

Erlang 从一开始就是一种函数式语言，函数式概念是仙丹的核心，它们不会被放错位置，也不会像我经常在 JVM 或 JavaScript 运行时之上使用函数式语言时产生的那种感觉一样。

[模式匹配](https://elixir-lang.org/getting-started/pattern-matching.html)是一种编写声明性代码并从输入中过滤出惊喜的极其优雅的方式。[递归](https://elixir-lang.org/getting-started/recursion.html)得到了很好的支持。数据是[不可变的](http://madlep.com/presentations/immutable_data_in_elixir/)。与有状态程序不同，您可以用与处理并发和与外部服务通信相同的一致方式优雅地处理状态:通过 [actor 模型](https://theerlangelist.blogspot.de/2013/01/actors-in-erlangelixir.html)和消息传递。通过[进程和管理器](https://elixir-lang.org/getting-started/mix-otp/supervisor-and-application.html)的概念构建弹性、容错系统是 Erlang 和 Elixir 的核心部分，管理器的概念是接受错误作为系统的一部分，并允许程序的一部分在不关闭整个系统的情况下优雅地失败。

Elixir 社区已经做了很好的工作:编辑器支持、工具、标准库和第三方包使用起来很愉快，也很容易上手。

Elixir 生态系统的一大焦点是围绕着 [Phoenix](http://phoenixframework.org/) ，一个类似于 [Ruby on Rails](http://rubyonrails.org/) 的全功能 web 框架。Phoenix 是一款非常棒的软件，对于构建 web 应用程序绝对有帮助。它也是[模块化](http://theerlangelist.com/article/phoenix_is_modular)，如果你只需要它的一小部分功能，你不需要使用它的所有部分。除此之外，Elixir 非常适合许多其他用例，甚至是编写[嵌入式软件](https://nerves-project.org/)。

然而，开始学习一门语言时，首先关注语言本身，然后学习 Phoenix 和其他建立在语言之上的抽象概念会更有帮助。

* * *

为了对 Elixir 中的工作流和工具有所了解，我创建了一个非常基本的示例项目。

prepend 是一个非常简单的命令行工具，它将给定的字符串添加到它在 stdin 上接收的每一行，并将其写入 stdout。

使用它看起来像这样:

```
$ echo -e "ice cream\npizza\ncats" | ./prepend "I like "
I like ice cream
I like pizza
I like cats 
```

要开始编写一个普通的 Elixir 应用程序，比如这个 CLI，您需要使用 [Mix](https://hexdocs.pm/mix/Mix.html) ，Elixir 的内置构建工具:`mix new prepend`创建一个新项目

所有构建配置和依赖项都在一个名为`mix.exs`的文件中定义。
我编辑了文件来配置 [escript](https://hexdocs.pm/mix/master/Mix.Tasks.Escript.Build.html) ，它允许您从您的 Elixir 项目构建一个文件，该文件包括 Elixir 本身和所有附加的依赖项，并且可以在任何安装了 Erlang VM 的机器上运行。

如果您编写了非常简单的没有依赖关系的脚本，并且您已经安装了 Elixir，那么您也可以将它们保存在一个以`.exs`结尾的文件中，并直接作为 Elixir 脚本运行，而无需编译:`elixir myscript.exs`。

在编写任何实际代码之前，我设置了[信条](http://credo-ci.org/)，并确保我的编辑器向我显示了内联的林挺警告
，我还设置了我的编辑器自动运行 [`mix format`](https://hexdocs.pm/mix/master/Mix.Tasks.Format.html) ，这样我自己就不用担心这些了。

很容易发现，对于从 stdin 到 stdout 的读写，我可以使用 [`IO.Stream`](https://hexdocs.pm/elixir/IO.Stream.html) 模块，文档解释说它实现了 [`Enumerable`](https://hexdocs.pm/elixir/Enumerable.html) ，这是我可以用来转换输入的协议。

为了转换输入，我想使用 [`Stream`](https://hexdocs.pm/elixir/Stream.html) 模块，因为——与 [`Enum`](https://hexdocs.pm/elixir/Enum.html) 不同——它工作缓慢，允许一次处理一行，而无需先从输入中读取所有内容。这样`prepend`可以更快地处理文件，即使是大文件也能保持一致的内存使用。

由于`Enumerable`是一个协议，我可以先实现核心逻辑，而不使用实际的标准输入和输出。
我决定先在 REPL 试一试。
仙丹自带 [IEx](https://hexdocs.pm/iex/IEx.html) 。你可以在任何地方通过输入`iex`来启动 REPL。

因为[列表](https://hexdocs.pm/elixir/List.html)也实现了`Enumerable`，所以我可以在使用实际的行之前对它们进行实验:

```
iex> ["one", "two"] |> Enum.map(&("$ " <> &1))
["$ one", "$ two"] 
```

这里的实际逻辑非常简单，但是为了进行实验，我必须使用`Enum`,因为`Stream`的输出如下所示:

```
iex> ["one", "two"] |> Stream.map(&("$ " <> &1))
#Stream<[enum: ["one", "two"], funs: [#Function<48.58052446/1 in Stream.map/2>]]> 
```

但是`Stream`也做了正确的事情，您可以在提取结果后看到:

```
iex> ["one", "two"] |> Stream.map(&("$ " <> &1)) |> Enum.to_list()
["$ one", "$ two"] 
```

创建新项目时，Mix 会自动在`lib/`目录下创建一个模块。

我把 REPL 的代码作为函数添加到这里:

```
def stream_lines(in_stream, str) do
  in_stream |> Stream.map(&(str <> &1))
end 
```

现在，我可以使用`iex -S mix`在我的项目中启动一个 REPL，并直接使用该模块:

```
["one", "two"] |> Prepend.stream_lines("$ ") |> Enum.to_list
["$ one", "$ two"] 
```

在修改了代码中的任何东西之后，我总是可以输入`r Prepend`来从 REPL 中重新加载代码，看看是否一切都还正常。

`iex`还有其他有用的快捷方式，比如`h`来查找文档——比如`h Stream.map`。

我发现的另一个非常酷的工具是`:observer.start`。这将启动一个 GUI，它实际上来自 Erlang 而不是 Elixir，但它向您展示了您可能想知道的关于您的应用程序状态和底层运行时的所有细节。

当编写长时间运行的应用程序而不是 CLI 时，您可以用一个`iex` REPL 连接到正在运行的进程，并使用所有相同的工具。

我将我的工作`Prepend`包装在一个 [`Prepend.CLI`](https://github.com/jorinvo/prepend/blob/master/lib/prepend/cli.ex) 模块中，该模块在启动时从系统接收参数并创建一个`IO.stream`。这是我实际添加到我的`escript`配置中的模块，我直接从命令行测试了它。

但我并没有就此止步。我决定重复使用我在 REPL 的实验作为测试，并探索不同的测试方法。

我给`test/prepend_test.exs`添加了一个简单的测试:

```
test "handles multiple words" do
  expected = ["I like ice cream", "I like pizza", "I like cats"]

  received =
    ["ice cream", "pizza", "cats"]
    |> Prepend.stream_lines("I like ")
    |> Enum.to_list()

  assert received == expected
end 
```

由于我不想一直重新输入`mix test`，所以我安装了 [`mix_test_watch`](https://github.com/lpil/mix-test.watch) 。每次我按下保存，它都会重新运行测试。

接下来，我记录了我的函数，并意识到我也可以在文档字符串中直接添加测试作为例子，并且它仍然作为 test:

```
@doc """
Prepend the given string to each line if a stream
and returns a stream of the resulting lines.

## Example:

iex> ["one", "two"] |> Prepend.stream_lines("$ ") |> Enum.to_list
["$ one", "$ two"]

""" 
```

我还在函数中添加了一个类型规范注释，就像我在标准库的文档中看到的那样:

```
@spec stream_lines(Enumerable.t(), String.t()) :: Enumerable.t() 
```

我使用了 [Dialyxir](https://github.com/jeremyjh/dialyxir) 来检查类型中的错误。
Dialyxir 是 Erlang 工具[透析器](http://erlang.org/doc/apps/dialyzer/dialyzer_chapter.html)的包装器，适用于 BEAM 上运行的所有语言。很高兴看到 Elixir 和 Erlang 生态系统是如何很好地协同工作的，并且它允许使用许多久经考验的工具和库。

之后，我决定使用 [StreamData](https://hexdocs.pm/stream_data/) 包为模块添加一个属性测试:

```
@tag timeout: 300_000
  property "streams" do
    check all lines <- list_of(string(:printable)),
              prefix <- string(:printable),
              max_runs: 1000 do
      lines
      |> Prepend.stream_lines(prefix)
      |> Stream.each(&String.starts_with?(&1, prefix))
    end
end 
```

当使用增加的`max_runs`时，我不得不增加测试的超时，当运行`mix test`时，我不想每次都运行这个，所以我修改了`test/test_helper.exs`来排除属性测试:

```
ExUnit.start(exclude: [:property]) 
```

它们仍然可以显式运行:`mix test --only property`

作为最后一件事，我把我的模块包装在一个混合任务中。
这样它可以在项目目录中直接作为`mix prepend`使用，而不是先编译成二进制文件。

* * *

虽然这是一个过度设计的解决方案的小例子，但我喜欢它的易用性和可用工具的简单性。

所有的源代码都可以在 [Github](https://github.com/jorinvo/prepend) 上找到，我很乐意听到我错过的工具，并获得对我的解决方案的反馈。我确信还有更多惯用的做事方法，我会非常高兴地发现这种灵丹妙药。

* * *

*(照片由丹尼尔·莱维斯·佩卢西在 Unsplash 上拍摄)*