# 在 Phoenix 应用程序中编写药剂插件

> 原文：<https://dev.to/jackmarchant/composing-elixir-plugs-in-a-phoenix-application-d24>

Elixir 是一种函数式语言，所以请求-响应循环的主要构件之一是不起眼的插件就不足为奇了。插头将采用连接结构(见[插头。Conn](https://hexdocs.pm/plug/Plug.Conn.html) )并返回相同类型的新结构。正是这个概念允许您将多个插件连接在一起，每个插件在一个 Conn 结构上都有自己的转换。
插件可以是实现插件行为的函数或模块。

### 什么是插头？

在我们了解 Plug 之前，将它作为一个依赖项添加到您的项目的 mix.exs 文件:`{:plug, "~> 1.5"}`并运行`mix deps.get`来安装它。
插头具有以下结构:

```
defmodule MyApp.Plug.AuthenticateUserSession do
  import Plug.Conn

  def init(options), do: options

  def call(conn, _opts) do
    conn
    |> put_session(:user, %{name: "Jack"})
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

在本例中，我们将:user 映射添加到当前会话数据中。两个主要功能是`init/1`和`call/2`，它们都是插头行为的一部分，在`plug.ex`中定义。
`init/1`用于使用可在`call/2`功能中使用的选项初始化插头。`call/2`是这个插件的核心，在这里你接受一个`%Plug.Conn{}`结构并返回一个新的。

### 如何使用插头

您可以在应用程序的路由器中使用插件。它可能看起来像这样:

```
defmodule MyApp.Router do
  use Plug.Router

  alias MyApp.Plug.AuthenticateUserSession

  plug AuthenticateUserSession

  get("/", do: send_resp(conn, 200, "Welcome\n"))
end 
```

Enter fullscreen mode Exit fullscreen mode

现在，当您在应用程序中访问`/`时，它将运行`AuthenticateUserSession.call/2`,正如我们之前定义的那样，它会将一个用户映射添加到会话数据中。

### 如何组合多个插头

构建应用程序时，您可能需要做的不仅仅是将用户添加到会话中。您可以创建一个新的插件来完成您需要的特定工作，而不是在 AuthenticateUserSession 插件上进行扩展。这允许我们组成分立的插头，并确保我们遵循[单一责任原则](https://en.wikipedia.org/wiki/Single_responsibility_principle)。

有了 Elixir 插件，我们可以在一个`:pipeline`中组合多个插件，将功能组合在一起。

```
defmodule MyApp.Router do
  use WebRoot.Web, :router
  pipeline :auth do
    plug AuthenticateUserSession
    plug AuthoriseUserSession
  end
  scope "/", do
    pipe_through :app
    get "/page", MyApp.PageController, :index
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

使用管道时，顺序很重要，因为将按照定义的顺序调用插头。现在，在 MyApp 中，当请求`/page`时，`AuthenticateUserSession.call/2`将被调用，它可以返回一个`%Plug.Conn{}`并让请求继续到下一个插件，或者它可以停止请求并返回一个不同的响应。当前者发生时，`AuthoriseUserSession.call/2`功能将运行。最后，如果这个插件成功返回，`MyApp.PageController`将被调用，用户可以收到响应。

除了请求-响应循环之外，Plug 概念还有许多应用。它象征性地解释了如何用许多部分来构建 Elixir 应用程序，所有部分都执行它们的工作并移交给下一个模块。
你可以在插头包的[文档中了解更多关于插头的信息。](https://hexdocs.pm/plug)