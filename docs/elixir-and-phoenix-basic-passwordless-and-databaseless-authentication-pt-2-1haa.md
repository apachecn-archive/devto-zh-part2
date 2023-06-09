# Elixir 和 Phoenix 基本无密码和无数据库认证(第二部分)

> 原文：<https://dev.to/bigardone/elixir-and-phoenix-basic-passwordless-and-databaseless-authentication-pt-2-1haa>

这个帖子属于 **Elixir 和 Phoenix basic 无密码无数据库认证**系列。

1.  [项目设置以及存储和验证认证令牌的初始功能](https://dev.to/bigardone/elixir-and-phoenix-basic-passwordless-and-databaseless-authentication-pt-1-3clj)
2.  [发送认证链接邮件和用户套接字连接](https://dev.to/bigardone/elixir-and-phoenix-basic-passwordless-and-databaseless-authentication-pt-2-1haa)
3.  [将 webpack 设置为我们的资产捆绑器和 Elm 单页应用程序](https://dev.to/bigardone/elixir-and-phoenix-basic-passwordless-and-databaseless-authentication-pt-3-4o0g)

在本系列的[前一部分](https://dev.to/bigardone/elixir-and-phoenix-basic-passwordless-and-databaseless-authentication-pt-1-3clj)中，我们为我们的新项目设置了保护伞应用程序，并为存储和生成认证令牌创建了必要的模块。完成这一步后，下一步是向有效用户发送电子邮件，其中包含登录链接，一旦点击，该链接将验证他们进入系统。

[![Final result](img/f66b2ec735530ead75baea010b91dc8f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Uq4Hr2yM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://monosnap.com/image/5VUT424b4Hu9ITi8r1SGae7HQleCPT.png)

### 发送邮件

在 Elixir 应用程序中发送电子邮件，我通常依靠来自 [thoughtbot](https://thoughtbot.com/) 的牛逼团队的 [Bamboo](https://github.com/thoughtbot/bamboo) ，它不仅简单强大，而且非常可定制。让我们继续将依赖项添加到项目中，在`PasswordlessAuthWeb`应用程序下:

```
# apps/passwordless_auth_web/mix.exs

defmodule PasswordlessAuthWeb.Mixfile do
  use Mix.Project

  # ...

  defp deps do
    [
      {:phoenix, "~> 1.3.2"},
      {:phoenix_pubsub, "~> 1.0"},
      {:phoenix_html, "~> 2.10"},
      {:phoenix_live_reload, "~> 1.0", only: :dev},
      {:gettext, "~> 0.11"},
      {:passwordless_auth, in_umbrella: true},
      {:cowboy, "~> 1.0"},
      {:bamboo, "~> 0.8"}
    ]
  end

  # ...
end 
```

Enter fullscreen mode Exit fullscreen mode

不要忘记运行必要的`deps.get` mix 任务来安装它。下一步是配置竹子:

```
# apps/passwordless_auth_web/config/config.exs

use Mix.Config

# ...

# Bamboo mailer configuration
config :passwordless_auth_web,
       PasswordlessAuthWeb.Service.Mailer,
       adapter: Bamboo.LocalAdapter

# ... 
```

Enter fullscreen mode Exit fullscreen mode

在配置中，我们指定了两件事:

*   我们将用作与`Bamboo`功能接口的模块名称。
*   我们要用来发送电子邮件的适配器。

对于我们的特殊情况，在开发项目时，我们将利用 [LocalAdapter](https://hexdocs.pm/bamboo/Bamboo.LocalAdapter.html) 在内存中存储发送的电子邮件，并为我们提供一个小的收件箱应用程序，我们可以在那里查看它们。为了访问这个收件箱应用程序，我们需要在路由器中安装一个新的 route it，这只能在`:dev`环境中访问:

```
# apps/passwordless_auth_web/lib/passwordless_auth_web/router.ex

defmodule PasswordlessAuthWeb.Router do
  use PasswordlessAuthWeb, :router

  if Mix.env() == :dev, do: forward("/sent_emails", Bamboo.EmailPreviewPlug)

  # ...
end 
```

Enter fullscreen mode Exit fullscreen mode

我们缺少的唯一部分是创建`Mailer`模块，所以让我们继续添加它:

```
# apps/passwordless_auth_web/lib/passwordless_auth_web/service/mailer.ex

defmodule PasswordlessAuthWeb.Service.Mailer do
  use Bamboo.Mailer, otp_app: :passwordless_auth_web
end 
```

Enter fullscreen mode Exit fullscreen mode

如果我们此时启动 Phoenix 服务器并访问[http://localhost:4000/sent _ emails](http://localhost:4000/sent_emails)，我们应该会看到以下消息:

[![Empty inbox](img/426595a8703de3fb04af9bc357f4990d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HqOGI9Di--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://monosnap.com/image/gB604OQdzH4yWfhptA9PyaxD16M3AD.png)

这完全没问题，因为我们还没有发送任何电子邮件，所以让我们继续创建必要的功能，使用电子邮件地址和令牌来构建包含认证链接的电子邮件:

```
# apps/passwordless_auth_web/lib/passwordless_auth_web/emails/auth_email.

defmodule PasswordlessAuthWeb.Emails.AuthEmail do
  import Bamboo.Email, only: [new_email: 1]
  import PasswordlessAuthWeb.Router.Helpers, only: [page_url: 4]

  @from "support@passwordlessauth.com"

  def build(email, token) do
    url = page_url(PasswordlessAuthWeb.Endpoint, :index, [], token: token)

    new_email(
      to: email,
      from: @from,
      subject: "Your authentication link",
      html_body: """
      <p>Here is your authentication link:</p>
      <a href="#{url}">#{url}</a>
      <p>It is valid for 5 minutes.</p>
      """,
      text_body: """
      Here is your authentication link: \n
      #{url}\n
      It is valid for 5 minutes.
      """
    )
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

借助令牌参数和 Phoenix 的路由助手，我们构建了新的 Bamboo 电子邮件，它的正文中有身份验证链接，收件人是电子邮件参数。暂且用凤凰开箱自带的默认路线，指向`/`。

让我们通过再次启动 Phoenix 服务器并发送一封新邮件来测试一下:

```
$ iex -S mix phx.server
Erlang/OTP 21 [erts-10.0] [source] [64-bit] [smp:8:8] [ds:8:8:10] [async-threads:1] [hipe]

[info] Running PasswordlessAuthWeb.Endpoint with Cowboy using http://0.0.0.0:4000
Interactive Elixir (1.6.6) - press Ctrl+C to exit (type h() ENTER for help)
iex(1)> email = PasswordlessAuthWeb.Emails.AuthEmail.build("foo@email.com", "token")
%Bamboo.Email{
  assigns: %{},
  bcc: nil,
  cc: nil,
  from: "support@passwordlessauth.com",
  headers: %{},
  html_body: "<p>Here is your authentication link:</p>\n<a href=\"http://localhost:4000/?token=token\">http://localhost:4000/?token=token</a>\n<p>It is valid for 5 minutes.</p>\n",
  private: %{},
  subject: "Your authentication link",
  text_body: "Here is your authentication link: \n\nhttp://localhost:4000/?token=token\n\nIt is valid for 5 minutes.\n",
  to: "foo@email.com"
}
iex(2)> PasswordlessAuthWeb.Service.Mailer.deliver_later email
[debug] Sending email with Bamboo.LocalAdapter:

%Bamboo.Email{assigns: %{}, bcc: [], cc: [], from: {nil, "support@passwordlessauth.com"}, headers: %{}, html_body: "<p>Here is your authentication link:</p>\n<a href=\"http://localhost:4000/?token=token\">http://localhost:4000/?token=token</a>\n<p>It is valid for 5 minutes.</p>\n", private: %{}, subject: "Your authentication link", text_body: "Here is your authentication link: \n\nhttp://localhost:4000/?token=token\n\nIt is valid for 5 minutes.\n", to: [nil: "foo@email.com"]}

iex(3)> 
```

Enter fullscreen mode Exit fullscreen mode

如果我们重新访问[http://localhost:4000/sent _ emails](http://localhost:4000/sent_emails)，我们可以看到我们刚刚发送的电子邮件:

[![Bamboo inbox](img/3518d7b2b2de65fca32d7ae9569e1018.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gIG2IGXI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://monosnap.com/image/PLsLxEljpZnc3KHuJLc7oR3Tl0CLPK.png)

但是，用户将如何请求认证电子邮件呢？

### 认证控制器

尽管管理站点是一个依赖于套接字连接的 Elm 单页应用程序，我们仍然需要提供一种机制，以便用户可以请求验证电子邮件。让我们为此使用一个控制器:

```
# apps/passwordless_auth_web/lib/passwordless_auth_web/controllers/authentication_controller.ex

defmodule PasswordlessAuthWeb.AuthenticationController do
  use PasswordlessAuthWeb, :controller

  alias PasswordlessAuthWeb.{Emails.AuthEmail, Service.Mailer}

  @email_regex ~r/^[A-Za-z0-9._%+-+']+@[A-Za-z0-9.-]+\.[A-Za-z]+$/

  def create(conn, params) do
    with %{"email" => email} <- params,
         true <- valid_email?(email),
         {:ok, token} <- PasswordlessAuth.provide_token_for(email) do
      build_and_deliver_email(email, token)
    end

    json(conn, %{message: gettext("auth.message")})
  end

  def valid_email?(email), do: Regex.match?(@email_regex, email)

  defp build_and_deliver_email(email, token) do
    email
    |> AuthEmail.build(token)
    |> Mailer.deliver_later()
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

如果收到的参数包含有效格式的`email`,它将提供一个令牌，构建并发送认证电子邮件。相反，出于安全原因，如果提供的电子邮件格式错误或不存在，我们不想给用户任何线索，所以它只是返回相同的成功消息。让我们为这个控制器和动作添加一个新的路由:

```
# apps/passwordless_auth_web/lib/passwordless_auth_web/router.ex

defmodule PasswordlessAuthWeb.Router do
  use PasswordlessAuthWeb, :router

    if Mix.env() == :dev, do: forward("/sent_emails", Bamboo.EmailPreviewPlug)

    # ...

    pipeline :api do
        plug(:accepts, ["json"])
    end

    # ...

    scope "/api", PasswordlessAuthWeb do
        pipe_through(:api)

        post("/auth", AuthenticationController, :create)
    end 
```

Enter fullscreen mode Exit fullscreen mode

最后，为了检查一切是否如我们预期的那样工作，让我们为控制器添加一个新的测试模块:

```
# apps/passwordless_auth_web/test/passwordless_auth_web/controllers/authentication_controller_test.exs

defmodule PasswordlessAuthWeb.AuthenticationControllerTest do
  use PasswordlessAuthWeb.ConnCase
  use Bamboo.Test

  import PasswordlessAuthWeb.Gettext

  alias PasswordlessAuth.Repo
  alias PasswordlessAuthWeb.Emails.AuthEmail

  describe "POST /api/auth" do
    test "always returns success message no matter what parameters receives", %{conn: conn} do
      conn = post(conn, authentication_path(conn, :create), email: "foo@test.com")
      assert %{"message" => _} = json_response(conn, 200)

      conn = post(conn, authentication_path(conn, :create), %{})
      assert assert %{"message" => _} = json_response(conn, 200)
    end

    test "delivers the email only when valid email", %{conn: conn} do
      email = "#{ __MODULE__ }@email.com"
      Repo.add_email(email)

      post(conn, authentication_path(conn, :create), email: email)

      {:ok, token} = Repo.fetch(email)

      assert_delivered_email(AuthEmail.build(email, token))
    end

    test "does not deliver the email only when invalid email format", %{conn: conn} do
      email = "#{ __MODULE__ }emailcom"
      Repo.add_email(email)

      post(conn, authentication_path(conn, :create), email: email)

      {:ok, token} = Repo.fetch(email)

      refute_delivered_email(AuthEmail.build(email, token))
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

如果我们运行它，我们可以检查它是否工作正常:

```
$ mix test test/passwordless_auth_web/controllers/authentication_controller_test.exs
==> passwordless_auth
Test patterns did not match any file: test/passwordless_auth_web/controllers/authentication_controller_test.exs
==> passwordless_auth_web
...

Finished in 0.1 seconds
3 tests, 0 failures

Randomized with seed 547795 
```

Enter fullscreen mode Exit fullscreen mode

耶！现在我们已经解决了电子邮件的生成和发送问题，让我们继续应用程序的下一个重要部分，用户套接字。

### 认证用户套接字连接

Phoenix 在引导一个新项目时创建了一个默认的`UserSocket`模块，所以让我们编辑它来添加认证逻辑:

```
# apps/passwordless_auth_web/lib/passwordless_auth_web/channels/user_socket.ex

defmodule PasswordlessAuthWeb.UserSocket do
  use Phoenix.Socket

  alias PasswordlessAuth

  ## Transports
  transport(:websocket, Phoenix.Transports.WebSocket)

  def connect(%{"token" => token}, socket) do
    case PasswordlessAuth.verify_token(token) do
      {:ok, email} ->
        {:ok, assign(socket, :user, %{email: email})}

      _ ->
        :error
    end
  end

  def connect(_, _socket), do: :error

  def id(socket), do: "user_socket:#{socket.assigns.user.email}"
end 
```

Enter fullscreen mode Exit fullscreen mode

`connect/2`回调函数接收一个`token`参数，并使用`PasswordlessAuth.verify_token/1`检查该令牌是否有效，成功时向套接字分配相应的电子邮件。另一方面，如果没有收到`token`参数或验证出错，则返回`:error`拒绝连接。让我们添加一些单元测试来确保它像我们期望的那样工作:

```
# apps/passwordless_auth_web/test/passwordless_auth_web/channels/user_socket_test.exs

defmodule PasswordlessAuthWeb.UserSocketTest do
  use PasswordlessAuthWeb.ChannelCase, async: true

  alias Phoenix.Socket
  alias PasswordlessAuth.Repo
  alias PasswordlessAuthWeb.UserSocket

  describe "connect/2" do
    test "errors when passing invalid params or token" do
      assert :error = connect(UserSocket, %{})
      assert :error = connect(UserSocket, %{"token" => "invalid-token"})
    end

    test "joins when passing valid token" do
      email = "foo@#{ __MODULE__ }.com"
      :ok = Repo.add_email(email)
      {:ok, token} = PasswordlessAuth.provide_token_for(email)

      assert {:ok, %Socket{assigns: %{user: %{email: ^email}}}} =
               connect(UserSocket, %{"token" => token})
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

并运行它们来检查结果:

```
$ mix test test/passwordless_auth_web/channels/user_socket_test.exs
==> passwordless_auth
Test patterns did not match any file: test/passwordless_auth_web/channels/user_socket_test.exs
==> passwordless_auth_web
..

Finished in 0.06 seconds
2 tests, 0 failures

Randomized with seed 589379 
```

Enter fullscreen mode Exit fullscreen mode

酷，它的工作！我想今天就到这里吧。在下一部分中，我们将在前端工作，将 [webpack](https://webpack.js.org/) 配置为我们选择的资产构建工具，添加 Elm 支持以开始构建管理单页应用程序，使用我们到目前为止所做的一切来验证用户。与此同时，不要忘记查看源代码和最终结果:

[源代码](https://github.com/bigardone/passwordless-auth)

编码快乐！

[![](img/9bf2fe70792d6de7618ba520c05490bc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4NFVcqVM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://feeds.feedburner.com/%257Er/CodeLoveAndBoards/%257E4/OhBolx8u7Ys)