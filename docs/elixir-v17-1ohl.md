# Elixir v1.7 已发布

> 原文：<https://dev.to/gumi/elixir-v17-1ohl>

Elixir v1.7 已发布。 本文基于 2018 年 8 月 25 日的博客文章“[Elixir v1.7 released](https://elixir-lang.org/blog/2018/07/25/elixir-v1-7-0-released/) ”，对新的主要功能进行说明。 对文档、错误处理、记录器报告、ExUnit、测试库等也进行了改进。

# 文档的元数据

Elixir v1.7 实现了[EEP 48](http://erlang.org/eep/eeps/eep-0048.html) 。 这是为了使文档能够在 Erlang VM 上工作的所有语言之间相互处理。 此外，EEP 48 还允许您用元数据注释文档，并通过 Elixir 加以利用。

```
@moduledoc "A brand new module"
@moduledoc authors: ["Jane", "Mary"], since: "1.4.0" 
```

Enter fullscreen mode Exit fullscreen mode

提供元数据的有以下三个。

*   [T2`@moduledoc`](https://hexdocs.pm/elixir/Module.html#module-moduledoc)
*   [T2`@typedoc`](https://hexdocs.pm/elixir/Module.html#module-doc-and-typedoc)
*   [T2`@doc`](https://hexdocs.pm/elixir/Module.html#module-doc-and-typedoc)

```
defmodule MyModule do
  @typedoc "This type"
  @typedoc since: "1.1.0"
  @type t :: term

  @doc "Hello world"
  @doc since: "1.1.0"
  def hello do
    "world"
  end

  @doc """
  Sums `a` to `b`.
  """
  def sum(a, b) do
    a + b
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

更新了 ExDoc 工具，可以将元数据用于更好地面向开发人员的文档中。 改善的主要有以下三点。

*   不推荐的模块和函数、回调和类型会自动发出警告。 例如，请看已过时的“[Behaviour](https://hexdocs.pm/elixir/Behaviour.html) ”模块。
*   函数、宏、回调和类型现在会显示它们的完整版本。 例如，请参阅“[`defguard/1`](https://hexdocs.pm/elixir/Kernel.html#defguard/1)”的右端。
*   未来的 Elixir 版本将在文档和边栏中包含防护部分(请参见 v1.8.0-dev「 [Guards](https://hexdocs.pm/elixir/master/Kernel.html#guards) ”)。 目前正在讨论如何在 ExDoc 本身中将此功能一般化(请参阅“[arbitrary grouping of functions with`@doc group: :some_group`](https://github.com/elixir-lang/ex_doc/issues/876)”)

Elixir 的交互式外壳的 IEx 也将更新以输出元数据。

[![iex-metadata.png](../Images/acd5573d91ca323cfcc405bc8be249b0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ch3SsckQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://elixir-lang.org/images/contents/iex-metadata.png)

Elixir 可以给出元数据。 但是，可以使用工具的只有`:deprecated`和`:since`。 其他键将来也可能出现。

改进的不仅仅是标准库。 适用于所有 Elixir 库和 APP 应用程序。 未来希望扩展到 Erlang VM 的所有 APP 应用程序。

请使用[`Code.fetch_docs/1`](https://hexdocs.pm/elixir/Code.html#fetch_docs/1)参照新文档的格式。 文档很受重视，能加入结构化的信息是其中的一步。

# __ 堆栈跟踪 _ _ 構造体

在 Erlang/OTP 21.0 中，改进了字符作用域的堆栈跟踪的获取方式。 因此，不像 v1.6 那样依赖`System.stacktrace/0`的副作用即可(不推荐使用 v1.7 到[`System.stacktrace/0`)。](https://hexdocs.pm/elixir/System.html#stacktrace/0) 

```
try do
  ... something that may fail ...
rescue
  exception ->
    log(exception, System.stacktrace())
    reraise(exception, System.stacktrace())
end 
```

Enter fullscreen mode Exit fullscreen mode

在 v1.7 中使用[`__STACKTRACE__/0`](https://hexdocs.pm/elixir/Kernel.SpecialForms.html#__STACKTRACE__/0)。

```
try do
  ... something that may fail ...
rescue
  exception ->
    log(exception, __STACKTRACE__)
    reraise(exception, __STACKTRACE__)
end 
```

Enter fullscreen mode Exit fullscreen mode

*   `__STACKTRACE__`:返回当前正在处理的异常的堆栈跟踪。 只能在`try/1`式的`catch`和`rescue`子句中使用。

此更改还将提高未来的性能。 这是因为，在光线作用域中可以正确追踪何时使用了堆栈跟踪，`try`结构结束后就不用继续参照堆栈跟踪的内容了。

例外系统的其他部分也进行了改善。 例如，`ArgumentError`、`ArithmeticError`和`KeyError`的消息会显示更多的信息。

# 与 Erlang/OTP 的记录器集成

Erlang/OTP 21 配备了新的[`:logger`模块](http://erlang.org/documentation/doc-10.0-rc1/lib/kernel-6.0/doc/html/logger.html)。 Elixir v1.7 与此模块完全集成，可以使用其元数据系统。 [`Logger.Translator`](https://hexdocs.pm/logger/Logger.Translator.html)的机制也经过了改进，可以导出元数据，在自定义`Logger`的后端可以使用以下信息(请参见逻辑[元数据](https://hexdocs.pm/logger/Logger.html#module-metadata))

*   `:crash_reason`: 2 要素的元组，第 1 要素为慢速/错误/结束的理由，第 2 要素为堆栈跟踪。
*   `:initial_call`:开始进程的第一个调用。
*   `:registered_name`:是在进程中登记的名字的阿童木。

以前一直钩在 Erlang 的`:error_logger`上的 Elixir 库，还是切换为`Logger`比较好吧。 因为今后的 Erlang/OTP 版本可以支持。

# 记录器编译时的清除

迄今为止`Logger`的宏[`debug/2`](https://hexdocs.pm/logger/Logger.html#debug/2)和[`info/2`](https://hexdocs.pm/logger/Logger.html#info/2)等，即使没有日志也对自变量进行了评价。 从 Elixir v1.7 开始，只有在存在消息日志时才会评估参数。

另外，在 Logger 的设定系统中，作为新的选项增加了`:compile_time_purge_matching`。 可以删除具有特定编译时元数据的日志。 例如，以下设置会导致 APP`:foo`的级别低于`:info`，或者删除来自`Bar.foo/3`的所有记录器调用。

```
config :logger,
  compile_time_purge_matching: [
    [application: :foo, level_lower_than: :info],
    [module: Bar, function: "foo/3"]
  ] 
```

Enter fullscreen mode Exit fullscreen mode

# ExUnit 的改善

[ExUnit](https://hexdocs.pm/ex_unit/) 是 Elixir 的单元测试的库。 使用 Elixir 宏查看发生失败时的详细错误报告。 例如，以下代码将失败:

```
assert "fox jumps over the lazy dog" == "brown fox jumps over the dog" 
```

Enter fullscreen mode Exit fullscreen mode

此时显示的是下一个报告。

[![exunit-diff.png](../Images/bf053111586a574b6536d8be41698417.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eSTW5x6c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://elixir-lang.org/images/contents/exunit-diff.png)

[`assert/1`宏](https://hexdocs.pm/ex_unit/ExUnit.Assertions.html#assert/1)查看代码，取出当前文件和行以及操作数。 在此基础上，`assert`失败时，与堆栈跟踪一起显示数据结构的差分。

但是，类似`assert some_function(expr1, var2)`的代码失败时，通常需要重新测试，调试或输出两个变量。 在 Elixir v1.7 中，“元素的”`assert`失败时，将输出各个参数的值(“[show arguments in ex unit reports for non-operator matches](https://github.com/elixir-lang/elixir/commit/eb5e809f10b4a24215d21cb109b506c2cf9f1761)) 例如，以下报告是简单的`assert some_vars(1 + 2, 3 + 4)`的情况。

[![exunit-bare-assertion-diff.png](../Images/65dd15f7d7ad3351e4e659eab73b72b2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4GVUmeNi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://elixir-lang.org/images/contents/exunit-bare-assertion-diff.png)

另外，[`doctest/2`](https://hexdocs.pm/ex_unit/master/ExUnit.DocTest.html#doctest/2)的失败会被彩色化并显示差异(参照“[Add diff to doctest](https://github.com/elixir-lang/elixir/pull/7585) ”)。

ExUnit 是测试框架，Mix 是构建工具。 开发者通常调用`mix test`进行测试。

在`mix test`中新增了`--failed`标志，可以只执行上次测试中所有失败的测试(参照“[add support for "-- failed " in ex unit](https://github.com/elixir-lang/elixir/pull/7373)”)。 另外，在`mix test --cover`中，以下信息将作为服务范围报告汇总显示(请参见“[display simple coverage report to console](https://github.com/elixir-lang/elixir/pull/7664)”) :

```
Generating cover results ...

Percentage | Module
-----------|--------------------------
   100.00% | Plug.Exception.Any
   100.00% | Plug.Adapters.Cowboy2.Stream
   100.00% | Collectable.Plug.Conn
   100.00% | Plug.Crypto.KeyGenerator
   100.00% | Plug.Parsers
   100.00% | Plug.Head
   100.00% | Plug.Router.Utils
   100.00% | Plug.RequestId
       ... | ...
-----------|--------------------------
    77.19% | Total 
```

Enter fullscreen mode Exit fullscreen mode

# 总结

与引人注目的新功能相比，这个版本更重视品质的改善。 Elixir 今后也会不断修改。 其他详细变更的列表请参阅[发行说明](https://github.com/elixir-lang/elixir/releases/tag/v1.7.0)。 此外，对以下 v1.8 进行的更改将记载在“[Changelog for Elixir v1.8](https://github.com/elixir-lang/elixir/blob/master/CHANGELOG.md) ”中。

在与 Elixir 代码库不直接相关的部分也有重要的改进。

*   在网站上添加了“[Development](https://elixir-lang.org/development.html) ”的部分。 说明了 Elixir 团队的构成和目标。
*   [加上了由 Honeypot](https://www.honeypot.io/) 拍摄的纪录片《[Elixir: A Mini-Documentary](http://doc.honeypot.io/elixir-documentary-2018/?utm_source=elixir_home&utm_medium=referral) 》( 12:48 )。
*   我已经介绍了 ExDoc 工具的改进。 再补充一点，即 Elixir 自身在[Makeup 库](https://github.com/tmbb/makeup)中具备语法亮点。 这增加了对语法和样式的控制，并改善了加载时间。

在 2018 年 9 月 4 日至 7 日于华盛顿州贝尔维尤召开的[ElixirConf](https://elixirconf.com/) 中，José Valim 在主题演讲中以 Elixir 的“[The Next Five Years](https://www.youtube.com/watch?v=suOzNeMJXl0) ”为题进行了演讲