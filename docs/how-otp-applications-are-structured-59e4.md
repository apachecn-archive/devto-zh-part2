# OTP 应用程序是如何构造的

> 原文：<https://dev.to/appsignal/how-otp-applications-are-structured-59e4>

OTP 是一个框架，它提供了帮助构建 Erlang 应用程序的标准，使用 T2 应用程序将代码打包成单元或组件。这种约定有助于将您的代码组织成模块的逻辑组，并提供一种启动和停止应用程序监督程序的方式。您的 Phoenix 项目是一个应用程序，但是 Phoenix 框架和 Ecto 也是应用程序。

应用程序是可以自己编译的组件。它可以依赖于其他应用程序，也可以采用自己的配置。与其他语言不同，这种约定提供了一种标准化的方式来指定 VM 应该如何与之交互。

除了包含虚拟机代码的编译后的`.beam`文件，编译后的应用程序还包括一个*应用程序规范* `.app`文件，它告诉虚拟机如何处理应用程序，以及一个可选的*应用程序回调模块*，用于启动、运行和停止应用程序。

## 应用回调模块

对于有管理程序的应用程序，应用程序回调模块定义了启动和停止应用程序的函数。没有管理程序的应用程序(通常是没有内部状态也可以调用的函数库)省略了回调模块，因为应用程序不需要启动。

在 Elixir 中，应用程序回调模块使用[和`Application`行为](https://hexdocs.pm/elixir/Application.html)。该行为实现了`start/2`和`stop/1`回调。前者启动应用程序的主监控器，后者是一个可选的回调，用于在停止监控器后进行清理。

当生成一个带有`--sup`标志的新项目时，Elixir 的`mix new`任务会自动创建一个应用程序回调模块。

```
$ mix new --sup elixir_app 
```

Enter fullscreen mode Exit fullscreen mode

生成的项目在`lib/elixir_app/application.ex`中包含一个应用回调模块。

```
defmodule ElixirApp.Application do
  # See https://hexdocs.pm/elixir/Application.html
  # for more information on OTP Applications
  @moduledoc false

  use Application

  def start(_type, _args) do
    # List all child processes to be supervised
    children = [
      # Starts a worker by calling: ElixirApp.Worker.start_link(arg)
      # {ElixirApp.Worker, arg},
    ]

    # See https://hexdocs.pm/elixir/Supervisor.html
    # for other strategies and supported options
    opts = [strategy: :one_for_one, name: ElixirApp.Supervisor]
    Supervisor.start_link(children, opts)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

使用时，`Application`模块添加一个`stop/1`函数的[存根，返回一个 ok 元组。生成的`start/2`函数启动应用程序的主管理程序。](https://github.com/elixir-lang/elixir/blob/0429cdd95f10abe2f5b3d188c2c3574132cae474/lib/elixir/lib/application.ex#L328-L330)

## 应用规范

运行`mix compile.app`将应用的规范放在`ebin`目录下的`.app`文件中，是用`mix compile`编译 app 时采取的步骤之一。

该规范包含定义应用程序的 Erlang 术语。它列出了应用程序中定义的模块、版本号以及该应用程序所依赖的其他应用程序的列表。它还指定要用作启动应用程序的回调的模块。

规范文件基于应用程序的`mix.exs`文件中的设置。为了能够生成规范，每个应用程序必须在其项目函数中定义一个名称和版本号。

```
defmodule ElixirApp.MixProject do
  use Mix.Project

  def project do
    [
      app: :elixir_app,
      version: "0.1.0"
    ]
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

下面是一个简单的示例`mix.exs`文件，它只列出了项目名称和版本号，生成了一个规范，列出了 Elixir 的默认应用程序并指定了应用程序中的模块。

```
{application,elixir_app,
             [{applications,[kernel,stdlib,elixir]},
              {description,"elixir_app"},
              {modules,['Elixir.ElixirApp','Elixir.ElixirApp.Application']},
              {registered,[]},
              {vsn,"0.1.0"}]}. 
```

Enter fullscreen mode Exit fullscreen mode

### 指定和配置应用程序回调模块

除了应用程序的名称和版本号，用`--sup`选项生成的`mix.exs`文件有一个带`mod`键的`application`功能。

```
defmodule ElixirApp.MixProject do
  # ...

  def application do
    [
      mod: {ElixirApp.Application, []}
    ]
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

当生成规范时，它包括回调模块。这个键指向应用程序的回调模块，所以 VM 知道使用哪个模块来启动应用程序。

```
{application,elixir_app,
             [{applications,[kernel,stdlib,elixir,logger]},
              {description,"elixir_app"},
              {modules,['Elixir.ElixirApp','Elixir.ElixirApp.Application']},
              {registered,[]},
              {vsn,"0.1.0"},
              {mod,{'Elixir.ElixirApp.Application',[]}}]}. 
```

Enter fullscreen mode Exit fullscreen mode

*提示*:`:mod`-tuple 中的列表用于将配置选项传递给应用程序。传入的任何东西都以回调模块的`start/2`函数的参数结束。

### `:applications`和`:extra_applications`

规范中的`applications`键列出了你的 app 所依赖的所有应用。默认情况下，`mix compile.app`包括`kernel` `stdlib`和`elixir`。

```
defmodule ElixirApp.MixProject do
  use Mix.Project

  def project do
    [
      app: :elixir_app,
      version: "0.1.0",
      deps: [{:appsignal, "~> 1.0.0"}]
    ]
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

依赖项被自动添加到`applications`列表中，如果它们有应用程序模块，它们会在应用程序启动前自动启动。

```
{application,elixir_app,
             [{applications,[kernel,stdlib,elixir,appsignal]},
              {description,"elixir_app"},
              {modules,['Elixir.ElixirApp','Elixir.ElixirApp.Application']},
              {registered,[]},
              {vsn,"0.1.0"}]}. 
```

Enter fullscreen mode Exit fullscreen mode

要添加更多像 Elixir 的`logger`这样的应用程序，您可以将它们添加到`:extra_applications`键，它们将随后被添加到现有列表中。

```
defmodule ElixirApp.MixProject do

  # ...

  def application do
    [
      extra_applications: [:logger],
      mod: {ElixirApp.Application, []}
    ]
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

`:applications`键用于明确指定规范中包含的应用程序。使用时，只会添加列出的应用程序和默认值。不会自动包括任何其他依赖项。

## 一路申请下来

每个单独的库，以及你的应用程序本身，都是一个应用程序。一些应用程序有一个监督树，要求它们有一个回调模块来负责启动和停止整个应用程序。

您的应用程序可以依赖于其他应用程序，每个应用程序都可以有自己的监督树和回调模块。不管里面有什么，它总是在应用程序规范文件中指定的。把它想象成你长生不老药的配方。😉

这就结束了我们对 Elixir 中 OTP 应用程序的概述。我们试图传达 OTP 会议的一些美妙之处。这是我们喜欢长生不老药的众多原因之一。如果你也这样做，让我们知道你想看到我们写什么评论，或者订阅[炼金简报](https://appsignal.com/elixir-alchemy)。