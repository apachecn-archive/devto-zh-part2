# 仙丹:儿童 specの機能

> 原文：<https://dev.to/gumi/elixir-child-spec-33i6>

本文以“[Elixir 1.5 的合理化的 child spec](https://qiita.com/melpon/items/bae4105c68f8da50e3c7) ”为基础进行了修改和整理。

从 Elixir v1.5 开始，对指定要从管理员启动的子规范( child spec )的方式进行了修改。 介绍 child spec 的功能和如何使用。

# Elixir v1.5 以后的 child spec 的规定方法

在 Elixir v1.5 之前，启动管理员时的 child spec 写道: 管理员用`Supervisor.Spec.supervisr/2`，工作人员用`Supervisor.Spec.worker/2`分别规定了孩子的规格。

```
children = [
  Supervisor.Spec.supervisor(MyApp.Repo, []),
  Supervisor.Spec.worker(MyApp.MyServer, [:foo]),
]

Supervisor.start_link(children, strategy: :one_for_one) 
```

Enter fullscreen mode Exit fullscreen mode

这样的话可能会写错。 容易发生在管理员的`MyApp.Repo`中呼叫`Supervisor.Spec.worker/2`，或者用`Supervisor.Spec.supervisor/2`启动本应是工作人员的`MyApp.MyServer`的错误。

一般情况下，每个模块都预先决定了使用哪一个。 不是启动子代时想的东西。

不仅是指定子类型的`:type`选项，`:start`和`:restart`也大致相同。 大多数情况下，写模块时应该规定，而不是在启动子代的阶段决定。 这意味着 child spec 包含在模块的配置中。

从 Elixir v1.5 开始就采用了这个想法。 在模块中用`child_spec/1`这个函数决定孩子的规格。 因此，孩子的启动只需写如下。

```
children = [
  MyApp.Repo,
  {MyApp.MyServer, [:foo]},
]

Supervisor.start_link(children, strategy: :one_for_one) 
```

Enter fullscreen mode Exit fullscreen mode

管理员启动后，将对列表中包含的所有子项调用每个模块的`child_spec/1`函数。 在本例中，根据`MyApp.Repo.child_spec([])`和`MyApp.MyServer.child_spec([:foo])`得到 child spec，各自的进程被启动。 指定的模块没有安装`child_spec/1`时发生错误。

由于这样精简了 child spec，[`Supervisor.Spec`](https://hexdocs.pm/elixir/Supervisor.Spec.html)已不再推荐。 如果想在孩子启动时定制`child spec`，可以使用[`Supervisor.child_spec/2`](https://hexdocs.pm/elixir/Supervisor.html#child_spec/2)。

```
Supervisor.child_spec({MyApp.MyServer, [:foo]}, shutdown: 10_000) 
```

Enter fullscreen mode Exit fullscreen mode

模块未安装`child_spec/1`时，以映射形式确定`child spec`。 因为比元组形式可以省略，所以会变得轻松吧。

```
# MyApp.MyWorker.start_link([:foo]) でプロセスを起動する
%{
  id:       MyApp.MyWorker,                          # 必須
  start:    {MyApp.MyWorker, :start_link, [[:foo]]}, # 必須
  restart:  :permanent,       # 省略可
  shutdown: 5_000,            # 省略可
  type:     :worker,          # 省略可
  modules:  [MyApp.MyWorker], # 省略可
} 
```

Enter fullscreen mode Exit fullscreen mode

# `use GenServer`实现`child_spec/1`

不过，每次定义`child_spec/1`很麻烦。 因此，从 Elixir v1.5 开始，用`use GenServer`自动安装`child_spec/1`。

```
defmodule MyServer do
  use GenServer
end

IO.inspect MyServer.child_spec([:foo])
# %{
#   id: MyServer,
#   restart: :permanent,
#   shutdown: 5000,
#   start: {MyServer, :start_link, [[:foo]]},
#   type: :worker,
# } 
```

Enter fullscreen mode Exit fullscreen mode

`use GenServer`一定要定为`type: :worker`。 想定制`child_spec/1`的定义时，只需在`use GenServer`中追加自变量即可。

```
defmodule MyServer do
  use GenServer, restart: :temporary, shutdown: 10_000
end

IO.inspect MyServer.child_spec([:foo])
# %{
#   id: MyServer,
#   restart: :temporary,
#   shutdown: 10_000,
#   start: {MyServer, :start_link, [[:foo]]},
#   type: :worker,
# } 
```

Enter fullscreen mode Exit fullscreen mode

`child_spec/1`函数不仅在 GenServer 上，在使用`Supervisor`时也会自动确定。 `use Supervisor`的情况下一定为`type: :supervisor`。 在定义基于模块的管理员时很有用吧。

```
defmodule MySupervisor do
  use Supervisor
end

IO.inspect MySupervisor.child_spec([:foo])
# %{
#   id: MySupervisor,
#   restart: :permanent,
#   start: {MySupervisor, :start_link, [[:foo]]},
#   type: :supervisor,
# } 
```

Enter fullscreen mode Exit fullscreen mode

# 现有的模块也安装`child_spec/1`

现有的`Agent`、`Registry`或`Task`等库也安装`child_spec/1`。 也就是说，可以写如下。 不用特意叫`Supervisor.Spec.worker/2`。

```
children = [
  {Registry, keys: :unique, name: MyApp.Registry},
  {Agent, fn -> :ok end},
]
Supervisor.start_link(children, strategy: :one_for_one) 
```

Enter fullscreen mode Exit fullscreen mode

# 总结

Elixir v1.5 简化了 child spec，在模块侧用`child_spec/1`规定了规格。

在必要的情况下，在模块中定义`child_spec/1`比较好吧。 `use GenServer`等`child_spec/1`被自动定义时，只需适当地传递自变量即可。

这样，在适当的位置定义 child spec 就可以避免指定错误。 有效地活用吧。