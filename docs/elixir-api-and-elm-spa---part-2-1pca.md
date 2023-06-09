# Elixir API 和 Elm SPA -第 2 部分

> 原文：<https://dev.to/miguelcoba/elixir-api-and-elm-spa---part-2-1pca>

# 第二部分:添加监护人认证

现在，我们将为我们的应用程序添加身份验证。因为这将是一个 JSON REST API，所以我们将使用一个令牌，该令牌需要在对 API 的每个请求中作为头传递。我们将使用 [Guardian](https://github.com/ueberauth/guardian) 来进行身份验证。
这不是完整的解决方案，因为它没有将令牌保存到数据库，也没有检查它们是否仍然有效。但是它正在检查安全令牌是否有效，这就足够了。

## 系列

1.  [第 1 部分- Elixir 应用程序创建](https://dev.to/miguelcoba/elixir-api-and-elm-spa-4hpf)
2.  第 2 部分-添加监护人身份验证
3.  [第 3 部分:Elm 应用程序创建和路由设置](https://dev.to/miguelcoba/elixir-api-and-elm-spa---part-3-3n0h)
4.  [第 4 部分:添加登录和注册页面](https://dev.to/miguelcoba/elixir-api-and-elm-spa---part-4-4il0)
5.  [第 5 部分:将会话数据保存到本地存储器](https://dev.to/miguelcoba/elixir-api-and-elm-spa---part-5-4j1k)

## 添加监护人依赖关系

首先让我们将 guardian 依赖项添加到 mix.exs 文件

```
 defp deps do
    [
      {:phoenix, "~> 1.3.0"},
      {:phoenix_pubsub, "~> 1.0"},
      {:phoenix_ecto, "~> 3.2"},
      {:postgrex, ">= 0.0.0"},
      {:gettext, "~> 0.11"},
      {:cowboy, "~> 1.0"},
      {:comeonin, "~> 4.0"},
      {:argon2_elixir, "~> 1.2"},
      {:guardian, "~> 1.0"}
    ]
  end 
```

Enter fullscreen mode Exit fullscreen mode

并检索依赖关系

```
mix deps.get 
```

Enter fullscreen mode Exit fullscreen mode

让我们配置监护人。在使用 Guardian 为您生成令牌之前，您需要做几件事情。第一件事是创建一个模块，它将被 Guardian 用作它不知道如何做并且完全依赖于你的应用程序的事情的一组回调。

在新文件夹 lib/toltec/auth/
中创建一个名为 guardian.ex 的新文件

```
# /lib/toltec/auth/guardian.ex

defmodule Toltec.Auth.Guardian do
  use Guardian, otp_app: :toltec

  def subject_for_token(resource, _claims) do
    sub = to_string(resource.id)
    {:ok, sub}
  end

  def resource_from_claims(claims) do
    case Toltec.Accounts.get_user(claims["sub"]) do
      nil -> {:error, "User not found"}
      user -> {:ok, user}
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这个文件的职责是在令牌和资源(用户)之间进行转换，以及在资源(我们的用户)和要在令牌中编码的惟一标识符之间进行转换，令牌将被到处传递。

想法很简单，生成一个唯一的、随机的、不可预测的字符串，它将代表我们的用户(主题)。这个字符串是令牌，将被提供给前端，以便在将来的交互(请求)中，我们知道用户就是它所说的那个人。

回到 guardian 模块，您会看到:

*   我们使用资源 id(用户 id)作为唯一标识应用程序用户的东西
*   从用户 id(编码在声明[“sub”]，主题)中，我们可以明确地知道我们的应用程序中的哪个用户对应

注意 get_user 方法。这与我们在第 1 部分中创建的不同。所以去 accounts.ex 改一下这个:

```
# lib/toltec/accounts/accounts.ex

  def get_user!(id), do: Repo.get!(User, id) 
```

Enter fullscreen mode Exit fullscreen mode

到此

```
 def get_user(id), do: Repo.get(User, id) 
```

Enter fullscreen mode Exit fullscreen mode

我们继续。现在我们需要配置库来正确地生成令牌。由于该令牌是以加密方式生成的，令牌的强度将直接取决于我们用来初始化加密算法的种子。因此，我们需要生成一个强大的种子，供 Guardian 用来生成令牌。
请不要使用这个示例键。使用您这边生成的新的。

```
# this is an example, don't use it in your app
mix guardian.gen.secret
TJD5jd2uGqrOO3zDb/pU85DhH9yzj5cy0WPjKQV6nz3d+XWS+RY5ff8hvhnfK2Dk 
```

Enter fullscreen mode Exit fullscreen mode

现在使用这个键来配置监护人。将其添加到 config/dev.exs

```
 config :toltec, Toltec.Auth.Guardian,
  issuer: "toltec",
  secret_key: "TJD5jd2uGqrOO3zDb/pU85DhH9yzj5cy0WPjKQV6nz3d+XWS+RY5ff8hvhnfK2Dk" 
```

Enter fullscreen mode Exit fullscreen mode

如果您要将该文件提交给您的回购，任何有权访问该回购的人都可以生成有效的令牌。所以我建议使用环境变量来分配一个密钥，但这是你的决定。

不管怎样，现在已经配置好了，让我们使用 Guardian 在用户正确登录并启动会话时创建令牌。

## 生成 JWT 代币

添加一个名为 session_controller.ex 的新文件，它的职责是新建会话，删除会话，刷新会话。

```
# lib/toltec_web/controllers/session_controller.ex

defmodule ToltecWeb.SessionController do
  use ToltecWeb, :controller

  alias Toltec.Accounts
  alias Toltec.Auth.Guardian

  def create(conn, params) do
    case authenticate(params) do
      {:ok, user} ->
        new_conn = Guardian.Plug.sign_in(conn, user)
        jwt = Guardian.Plug.current_token(new_conn)

        new_conn
        |> put_status(:created)
        |> render("show.json", user: user, jwt: jwt)

      :error ->
        conn
        |> put_status(:unauthorized)
        |> render("error.json", error: "User or email invalid")
    end
  end

  def delete(conn, _) do
    conn
    |> Guardian.Plug.sign_out()
    |> put_status(:no_content)
    |> render("delete.json")
  end

  def refresh(conn, _params) do
    user = Guardian.Plug.current_resource(conn)
    jwt = Guardian.Plug.current_token(conn)

    case Guardian.refresh(jwt, ttl: {30, :days}) do
      {:ok, _, {new_jwt, _new_claims}} ->
        conn
        |> put_status(:ok)
        |> render("show.json", user: user, jwt: new_jwt)

      {:error, _reason} ->
        conn
        |> put_status(:unauthorized)
        |> render("error.json", error: "Not Authenticated")
    end
  end

  defp authenticate(%{"email" => email, "password" => password}) do
    Accounts.authenticate(email, password)
  end

  defp authenticate(_), do: :error
end 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，有三种方法映射会话操作:创建、终止和刷新。他们利用守护者。当用户正确给出电子邮件和密码时，生成 JWT (JSON Web Token)的插件函数。

我们有一个助手 *authenticate()* 函数，它接收电子邮件和密码，并检查数据库中是否存在符合该组合的用户。如果存在，则返回一个{:ok，user}元组。如果没有，它将返回错误。有了这个契约，我们可以理解 *create()* 函数。如果它是一个有效的电子邮件/密码，我们得到一个用户，我们可以传递给*卫报。Plug.sign_ing* 函数将用户置于连接结构中。然后，我们为该用户生成一个令牌，最后发送一个包含用户和令牌的 JSON 响应。如果电子邮件/密码组合不存在，我们会呈现一个 JSON 错误响应。

*删除/2* 功能更简单。它所做的是调用 Guardian sing_out/0 方法从连接结构中删除资源(我们的用户，记住)。

还不会使用刷新方法，但它会获取一个有效的现有令牌，获取一个有效期超过 30 天的新令牌，并发送该新令牌作为响应来替换旧令牌。

让我们看看账户中的 *authenticate/2* 方法是什么样子的:

```
 # lib/toltec/accounts/accounts.ex

  def authenticate(email, password) do
    user = Repo.get_by(User, email: String.downcase(email))

    case check_password(user, password) do
      true -> {:ok, user}
      _ -> :error
    end
  end

  defp check_password(user, password) do
    case user do
      nil -> Comeonin.Argon2.dummy_checkpw()
      _ -> Comeonin.Argon2.checkpw(password, user.password_hash)
    end
  end 
```

Enter fullscreen mode Exit fullscreen mode

这很简单，它试图通过电子邮件从数据库中检索用户。然后委托给 *check_password/2* 在用户不存在的情况下进行虚拟密码检查，或者在用户存在的情况下根据存储在 DB 中的散列检查密码。

好了，会话控制器准备好了。在转到视图之前，让我们添加一个 user_controller，它将负责为我们的应用程序创建新的用途。它将非常类似于 session _ contoller.ex。

需要注意的一点是，用户创建是即时的，也就是说，一旦用户创建请求完成，用户就在系统中存在并有效。在生产系统中，您需要添加额外的验证步骤，比如在激活系统中的用户之前，发送一封带有验证电子邮件链接的确认电子邮件。这超出了本教程的范围。

在 lib/toltec_web/controllers/
中创建一个名为 user_controller 的文件

```
# lib/toltec_web/controllers/user_controller.ex

defmodule ToltecWeb.UserController do
  use ToltecWeb, :controller

  alias Toltec.Accounts
  alias Toltec.Accounts.User
  alias Toltec.Auth.Guardian

  action_fallback(ToltecWeb.FallbackController)

  def create(conn, params) do
    with {:ok, %User{} = user} <- Accounts.create_user(params) do
      new_conn = Guardian.Plug.sign_in(conn, user)
      jwt = Guardian.Plug.current_token(new_conn)

      new_conn
      |> put_status(:created)
      |> render(ToltecWeb.SessionView, "show.json", user: user, jwt: jwt)
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

该控制器接收一组参数(名称、电子邮件、密码)并将其传递给帐户 *create_user/1* 以创建一个新用户。如果成功，将自动为用户签名并创建一个 JWT。用户和令牌将通过 show.json 视图作为 JSON 发送到前端。这里没什么复杂的，对吧？

让我们转到呈现 JSON 的视图。

首先是简单的。在 lib/toltec_web/views 中创建一个 user_view.ex 文件，如下:

```
# lib/toltec_web/views

defmodule ToltecWeb.UserView do
  use ToltecWeb, :view

  def render("user.json", %{user: user}) do
    %{
      id: user.id,
      name: user.name,
      email: user.email
    }
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

没什么意外。render 方法接收一个用户，并返回一个带有我们希望向客户端公开的属性的地图。Elixir 会帮我们把它转换成 JSON。为此，我们只公开 id、姓名和电子邮件。

现在在 lib/toltec_web/views
中创建一个名为 session_view.ex 的新文件

```
# lib/toltec_web/views/session_view.ex

defmodule ToltecWeb.SessionView do
  use ToltecWeb, :view

  def render("show.json", %{user: user, jwt: jwt}) do
    %{
      data: render_one(user, ToltecWeb.UserView, "user.json"),
      meta: %{token: jwt}
    }
  end

  def render("delete.json", _) do
    %{ok: true}
  end

  def render("error.json", %{error: error}) do
    %{errors: %{error: error}}
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这是相似的，虽然有点复杂。show.json 视图向客户机呈现用户和令牌。它通过委托给 UserView 中的 user.json 视图来实现。不错吧。

## 配置路由给我们的用户 API

我们快完成了。我们只缺少一个简单但关键的步骤，路由器。没有它，应用程序在收到请求时就不知道该做什么。让我们来配置我们的路由器。

打开 router.ex，让它看起来像这样:

```
# lib/toltec_web/router.ex

defmodule ToltecWeb.Router do
  use ToltecWeb, :router

  pipeline :api do
    plug(:accepts, ["json"])
  end

  pipeline :api_auth do
    plug(Toltec.Auth.Pipeline)
  end

  scope "/api", ToltecWeb do
    pipe_through(:api)

    post("/sessions", SessionController, :create)
    post("/users", UserController, :create)
  end

  scope "/api", ToltecWeb do
    pipe_through([:api, :api_auth])

    delete("/sessions", SessionController, :delete)
    post("/sessions/refresh", SessionController, :refresh)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这已经足够说明问题了。它在/api 范围内添加了几个新的路由。有几个比其他的更受限制。

如果请求是对/sessions 或/users 的 POST 请求，它将被允许而无需授权。但是如果请求是对/sessions 的删除或对/sessions/refresh 的发送，我们要求对它们进行身份验证。这是合乎逻辑的，因为我们希望只有经过身份验证的登录用户才能注销或刷新会话(即刷新代表会话中用户的令牌)。为了完成这个认证，我们使用了一个新的管道: *:api_auth* 。该管道将使用 Guardian 来检查连接中是否已经存在有效的令牌。

让我们添加这个守护管道助手。在 lib/toltec/auth/
中创建一个名为 pipeline.ex 的文件

```
# lib/toltec/auth/pipeline.ex

defmodule Toltec.Auth.Pipeline do
  use Guardian.Plug.Pipeline,
    otp_app: :toltec,
    module: Toltec.Auth.Guardian,
    error_handler: Toltec.Auth.ErrorHandler

  plug(Guardian.Plug.VerifyHeader)
  plug(Guardian.Plug.EnsureAuthenticated)
  plug(Guardian.Plug.LoadResource)
end 
```

Enter fullscreen mode Exit fullscreen mode

主要检查对我们的应用程序发出的请求是否具有带有令牌的标头，确保令牌有效，如果有效，则自动加载资源(即，加载与加密令牌中令牌的 claims["sub"]值对应的用户)。

监护管道将需要一个模块来处理它可能发现的错误。所以在 lib/toltec/auth 中添加一个 error_handler.ex 文件如下:

```
# lib/toltec/auth/error_handler.ex

defmodule Toltec.Auth.ErrorHandler do
  import Plug.Conn

  def auth_error(conn, {:invalid_token, _reason}, _opts),
    do: response(conn, :unauthorized, "Invalid Token")

  def auth_error(conn, {:unauthenticated, _reason}, _opts),
    do: response(conn, :unauthorized, "Not Authenticated")

  def auth_error(conn, {:no_resource_found, _reason}, _opts),
    do: response(conn, :unauthorized, "No Resource Found")

  def auth_error(conn, {type, _reason}, _opts), do: response(conn, :forbidden, to_string(type))

  defp response(conn, status, message) do
    body = Poison.encode!(%{error: message})

    conn
    |> put_resp_content_type("application/json")
    |> send_resp(status, body)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

## 消耗 API

让我们试试我们的 API。这很难，因为该应用程序根本没有网络渲染。所有的交互都是通过 REST 调用进行的，请求和响应都将在 JSON 中编码。所以我们需要一个客户端来连接和使用我们的 API。Chrome 浏览器也有扩展来使用 REST APIs，比如*高级 REST 客户端*。当然也有本地窗口应用程序来做同样的事情。对于 macOS，我使用的是令人惊叹的[失眠](https://insomnia.rest/)应用程序。在本教程中，我们将只使用 curl 来处理请求。

首先，确保应用程序正在运行

```
mix phx.server
[info] Running ToltecWeb.Endpoint with Cowboy using http://0.0.0.0:4000 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们做一个登录请求。这需要是对/api/sessions 路由的 POST 请求。我们需要将电子邮件和密码作为参数传递。我们的应用程序中已经有一个用户插入了我们的 seeds.exs。使用那个用户

```
curl --request POST \
  --url http://localhost:4000/api/sessions \
  --header 'authorization: Bearer ' \
  --header 'content-type: application/x-www-form-urlencoded' \
  --data 'email=user%40toltec&password=user%40toltec'
{
    "meta":{
        "token":"eyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJ0b2x0ZWMiLCJleHAiOjE1MzEzNTEzMzAsImlhdCI6MTUyODkzMjEzMCwiaXNzIjoidG9sdGVjIiwianRpIjoiMjQ1ZjUyMjAtZWRmMi00OWM5LThiZmMtYWJkNTU4ZTRlYjU4IiwibmJmIjoxNTI4OTMyMTI5LCJzdWIiOiIxIiwidHlwIjoiYWNjZXNzIn0.AMtC7MyvkMqXKyMyV3oBvkVBgnWNTPxDAKaFx0xgfq_ubQ9XbUJH2ZqoRKCxWE0BUUEPq_GYxNdGXPzi72W_Tg"
    },
    "data":{
        "name":"some user",
        "id":1,
        "email":"user@toltec"
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

很好，我们得到了一个包含用户和令牌值的响应。

现在让我们尝试注销请求。这是一个删除请求，只需要将令牌作为标头。我还没有提到标题的格式，但它必须是
的格式

```
authorization: Bearer <jwt_value> 
```

Enter fullscreen mode Exit fullscreen mode

所以对于这个注销请求，让我们像这样使用 curl，使用上一步的 JWT 值

```
curl --request DELETE \
  --url http://localhost:4000/api/sessions \
  --header 'authorization: Bearer eyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJ0b2x0ZWMiLCJleHAiOjE1MzEzNTEzMzAsImlhdCI6MTUyODkzMjEzMCwiaXNzIjoidG9sdGVjIiwianRpIjoiMjQ1ZjUyMjAtZWRmMi00OWM5LThiZmMtYWJkNTU4ZTRlYjU4IiwibmJmIjoxNTI4OTMyMTI5LCJzdWIiOiIxIiwidHlwIjoiYWNjZXNzIn0.AMtC7MyvkMqXKyMyV3oBvkVBgnWNTPxDAKaFx0xgfq_ubQ9XbUJH2ZqoRKCxWE0BUUEPq_GYxNdGXPzi72W_Tg' --verbose
*   Trying ::1...
* TCP_NODELAY set
* Connection failed
* connect to ::1 port 4000 failed: Connection refused
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to localhost (127.0.0.1) port 4000 (#0)
> DELETE /api/sessions HTTP/1.1
> Host: localhost:4000
> User-Agent: curl/7.54.0
> Accept: */*
> authorization: Bearer eyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJ0b2x0ZWMiLCJleHAiOjE1MzEzNTEzMzAsImlhdCI6MTUyODkzMjEzMCwiaXNzIjoidG9sdGVjIiwianRpIjoiMjQ1ZjUyMjAtZWRmMi00OWM5LThiZmMtYWJkNTU4ZTRlYjU4IiwibmJmIjoxNTI4OTMyMTI5LCJzdWIiOiIxIiwidHlwIjoiYWNjZXNzIn0.AMtC7MyvkMqXKyMyV3oBvkVBgnWNTPxDAKaFx0xgfq_ubQ9XbUJH2ZqoRKCxWE0BUUEPq_GYxNdGXPzi72W_Tg
>
< HTTP/1.1 204 No Content
< server: Cowboy
< date: Wed, 13 Jun 2018 23:23:27 GMT
< content-length: 11
< content-type: application/json; charset=utf-8
< cache-control: max-age=0, private, must-revalidate
< x-request-id: 2krtnfgdqhj1inb4jg0000r5
<
* Excess found in a non pipelined read: excess = 11 url = /api/sessions (zero-length body)
* Connection #0 to host localhost left intact 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，API 返回了 204 响应(无内容)，正如我们在 session_controller.ex 上指定的那样。

最后让我们试试新用户的创建。让我们向/api/users/
发出一个 POST 请求

```
curl --request POST \
  --url http://localhost:4000/api/users \
  --header 'authorization: Bearer ' \
  --header 'content-type: application/x-www-form-urlencoded' \
  --data 'email=miguel%40toltec&password=miguel%40toltec&name=Miguel%20Coba'
{
    "meta":{
        "token":"eyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJ0b2x0ZWMiLCJleHAiOjE1MzE1NjUxODYsImlhdCI6MTUyOTE0NTk4NiwiaXNzIjoidG9sdGVjIiwianRpIjoiNTYxODY1MzQtNGZhNi00YWJkLWEwOTUtZTEyZTgzN2QzMmM4IiwibmJmIjoxNTI5MTQ1OTg1LCJzdWIiOiIyIiwidHlwIjoiYWNjZXNzIn0.CzZO0LgfqxwZ7S1Qy6lgVNrrjqacdl7fdEhVOnmt6LoXEBdN1muK1xRBDQOlll8h_lWV7PIJoZMFWUTzmcPuLg"
    },
    "data":{
        "name":"Miguel Coba",
        "id":2,
        "email":"miguel@toltec"
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

你可以在分支 part-02 的 repo [这里的](https://github.com/miguelcoba/toltec-api)中找到带有测试的源代码。

克隆它之后，运行测试并验证一切正常:

```
mix test
......................................

Finished in 0.6 seconds
38 tests, 0 failures 
```

Enter fullscreen mode Exit fullscreen mode

就是这样。我们添加了一个路由器，接受创建新用户、登录和注销用户以及延长会话的请求。没有 html 回应，只有 JSON。

在第 3 部分中，我们将从前端 Elm 应用程序开始。