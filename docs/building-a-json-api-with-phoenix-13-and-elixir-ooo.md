# 用 Phoenix 1.3 和 Elixir 构建 JSON API

> 原文：<https://dev.to/lobo_tuerto/building-a-json-api-with-phoenix-13-and-elixir-ooo>

* * *

你可以在 [lobotuerto 的笔记查看这篇文章的最新更新版本——用 Phoenix 1.3 和 Elixir 构建 JSON API。](https://lobotuerto.com/blog/building-a-json-api-with-phoenix-and-elixir/)

它还包括一个内容表，便于在那里导航。；)

* * *

你有 **Rails** 背景吗？

关于如何使用**elixin**和 **Phoenix 构建 **JSON API** 的*过时*或*不完整*教程，你看腻了吗？**

然后，**看在我朋友的份上！**

# 简介

我发现主要应该写两种类型的教程:

*   重点突出、范围明确的教程。
*   完整的分步教程。

有范围、有重点的教程应该用来解释技术，比如这个:[Fluid SVG with vue . js .](https://lobotuerto.com/blog/fluid-svgs-with-vuejs/)

但是，完全集成的教程应该用来教授新的技术堆栈。

从*零*到*完全工作的原型*没有跳过步骤。

内置最佳实践，为给定任务提供最佳库。

我非常喜欢采用这种*整体*方法的教程。

所以，这不仅仅是关于如何生成一个新的凤凰 **API only** app。这很简单，你只需要将`--no-brunch --no-html`传递给`mix phx.new`。

本教程是关于为 web 应用程序创建一个小的但是完全可操作的 JSON API。

* * *

为了补充你的 **API，**我在前端推荐**Vue.js**:
[一个新 vue . js 项目的快速入门指南。](https://lobotuerto.com/blog/quickstart-guide-for-a-new-vuejs-project/)

* * *

**我们要做的:**

*   创建一个新的纯 API 的 Phoenix 应用程序——跳过 HTML 和 JS 的东西。
*   创建一个用户模式模块(模型)并散列它的密码——因为在数据库中存储纯文本密码是错误的。
*   创建一个用户端点——这样您就可以获得一个列表，创建或删除用户！
*   CORS 配置—因此您可以使用在另一个端口/域上运行的前端。
*   使用基于会话的身份验证创建登录端点。如果有足够多的人感兴趣，我可以在晚些时候做一个 JWT。

* * *

**让我澄清一件事……**

我刚刚开始使用**仙丹/凤凰，**如果有任何遗漏或不良做法，请原谅我，通知我，我会尽快修复它们。

当我试图学习如何用 Elixir / Phoenix 实现一个 **JSON API** 时，我希望我有这个教程，但是我跑题了。

* * *

# 先决条件

## 安装仙丹

我们将从使用 [asdf](https://github.com/asdf-vm/asdf) 版本管理器安装 **Erlang** 和 **Elixir** 开始——在开发环境中使用版本管理器是*最佳实践*。

*   [如何在 Manjaro Linux 中安装 Elixir？](https://lobotuerto.com/blog/how-to-install-elixir-in-manjaro-linux/)

## 安装六角形

现在让我们来挑战一下 **Hex** 软件包管理员:

```
mix local.hex 
```

Enter fullscreen mode Exit fullscreen mode

你现在可以用
打印一些关于你的**药剂**堆栈的信息

```
mix hex.info 
```

Enter fullscreen mode Exit fullscreen mode

您将看到类似于:
的内容

```
Hex: 0.17.3
Elixir: 1.6.4
OTP: 20.3.2

Built with: Elixir 1.5.3 and OTP 18.3 
```

Enter fullscreen mode Exit fullscreen mode

## 安装凤凰

现在让我们安装框架:

```
mix archive.install https://github.com/phoenixframework/archives/raw/master/phx_new.ez 
```

Enter fullscreen mode Exit fullscreen mode

## 安装 PostgreSQL

PostgreSQL 是新 Phoenix 应用程序的默认数据库，这是有充分理由的:它是一个可靠的、可靠的、设计良好的关系数据库。

*   [如何在 Manjaro Linux 中安装 PostgreSQL？](https://lobotuerto.com/blog/how-to-install-postgresql-in-manjaro-linux/)

## 关于休息客户端

您可能需要获得一个 **REST 客户端**，这样您就可以尝试您的 **API** 端点。

最受欢迎的两个似乎是 **Postman** 和 **Advanced Rest Client** 我测试了这两个应用，我可以说我都不喜欢——至少在它们的 Chrome 应用上——因为一个不显示 cookie 信息，另一个不在 POST 请求上发送声明的变量。

无论如何，如果你想尝试一下:

*   你可以在这里找到邮递员。
*   这里可以得到圆弧[。](https://chrome.google.com/webstore/detail/advanced-rest-client/hgmloofddffdnphfgcellkdfbfbjeloo)

如果你使用的是像 [Axios](https://github.com/axios/axios) 这样的 web 前端库，那么你的浏览器开发者工具应该足够了:

*   [Chrome DevTools。](https://developer.chrome.com/devtools)
*   火狐开发者工具。

如果你使用 **Axios** ，不要忘记传递配置选项`withCredentials: true`，这将允许客户端在进行 CORS 请求时发送 cookies。

或者你可以用 good ol' `curl`它真的很好用！

我将向您展示一些如何从 CLI 测试您的端点的示例。

* * *

# 创建一个新的仅支持 API 的 Phoenix 应用程序

## 生成 app 文件

在您的终端:

```
mix phx.new my-app --app my_app --module MyApp --no-brunch --no-html 
```

Enter fullscreen mode Exit fullscreen mode

从上面的命令中:

*   您将看到`my-app`是为这个应用程序创建的目录的名称。
*   你会看到在`my-app/lib`中的文件和目录中使用了`my_app`，例如`my_app.ex`。
*   你会发现`MyApp`无处不在，因为它是你的应用程序的主要模块。

例如在`my-app/lib/my_app.ex` :

```
defmodule MyApp do
  @moduledoc """
  MyApp keeps the contexts that define your domain
  and business logic.

  Contexts are also responsible for managing your data, regardless
  if it comes from the database, an external API or others.
  """
end 
```

Enter fullscreen mode Exit fullscreen mode

## 创建开发数据库

如果您在安装 **PostgreSQL** 时创建了一个新的 DB 用户，将其凭证添加到`config/dev.exs`和`config/test.exs`。然后执行:

```
cd my-app
mix ecto.create 
```

Enter fullscreen mode Exit fullscreen mode

**注:**

您可以用
删除 *dev* 环境的数据库

```
mix ecto.drop 
```

Enter fullscreen mode Exit fullscreen mode

如果您想为*测试*环境删除数据库，您需要:

```
MIX_ENV=test mix ecto.drop 
```

Enter fullscreen mode Exit fullscreen mode

## 启动开发服务器

从您的终端:

```
mix phx.server 
```

Enter fullscreen mode Exit fullscreen mode

访问`http://localhost:4000`,享受格式化精美的错误页面带来的荣耀。:)

不过不用担心，我们很快就会添加一个 JSON 端点。

[![Router Error](img/1911de90cce3f755c0f9b8669b2427a6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cCQKgSFP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lobotuerto.com/blog/building-a-json-api-with-phoenix-and-elixir/phoenix-router-error.png)

## 404s 和 500s 的 JSON 错误

如果您不喜欢在出现错误时看到 HTML 页面，而是希望接收 JSONs，请在您的`config/dev.ex`中将`debug_errors`设置为`false`，并重启您的服务器:

```
config :my_app, MyAppWeb.Endpoint,
  # ...
  debug_errors: false,
  # ... 
```

Enter fullscreen mode Exit fullscreen mode

现在，访问`http://localhost:4000`会产生:

```
{  "errors":  {  "detail":  "Not Found"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

* * *

# 用户模式(模型)

我们将在一个 **Auth** 上下文中生成一个新的**用户**模式(模型)。

Phoenix 中的上下文很酷，它们充当 API 边界，让你以更好的方式组织你的应用程序代码。

## 生成授权上下文和用户模式模块

```
mix phx.gen.context Auth User users email:string:unique password:string is_active:boolean 
```

Enter fullscreen mode Exit fullscreen mode

从上面看:

*   Auth 是上下文的模块名。
*   **用户**是模式的模块名。
*   **users** 是 DB 表的名称。
*   之后是一些字段定义。

上面命令生成的迁移如下:

```
defmodule MyApp.Repo.Migrations.CreateUsers do
  use Ecto.Migration

  def change do
    create table(:users) do
      add :email, :string#, null: false
      add :password, :string
      add :is_active, :boolean, default: false, null: false

      timestamps()
    end

    create unique_index(:users, [:email])
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

根据您的喜好进行调整，即不允许电子邮件使用`null`，然后运行新的迁移:

```
mix ecto.migrate 
```

Enter fullscreen mode Exit fullscreen mode

如果您想阅读一些关于这个生成器的信息，请执行:

```
mix help phx.gen.context 
```

Enter fullscreen mode Exit fullscreen mode

## 保存时哈希用户密码

向`mix.exs` :
添加新的依赖关系

```
defp deps do
    [
      # ...
      {:bcrypt_elixir, "~> 1.0"}
    ]
  end 
```

Enter fullscreen mode Exit fullscreen mode

这是 [Bcrypt](https://github.com/riverrun/bcrypt_elixir) ，我们在保存之前会用它来哈希用户的密码；所以我们不把它作为纯文本存储在数据库中。

使用
获取新的应用程序依赖项

```
mix deps.get 
```

Enter fullscreen mode Exit fullscreen mode

将`lib/my_app/auth/user.ex`改成这样:

```
defmodule MyApp.Auth.User do
  # ...
  def changeset(user, attrs) do
    user
    |> cast(attrs, [:email, :password, :is_active])
    |> validate_required([:email, :password, :is_active])
    |> unique_constraint(:email)
    |> hash_user_password()
  end

  defp hash_user_password(%Ecto.Changeset{valid?: true, changes: %{password: password}} = changeset) do
    change(changeset, password: Bcrypt.hash_pwd_salt(password))
  end

  defp hash_user_password(changeset) do
    changeset
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

注意`hash_user_password/1`的调用和定义。

这是通过那个函数运行*变更集*，如果变更集碰巧有一个`password`键，它将使用 **Bcrypt** 散列它。

* * *

运行`Bcrypt.hash_pwd_salt("hola")`会产生类似于`"$2b$12$sI3PE3UsOE0BPrUv7TwUt.i4BQ32kxgK.REDv.IHC8HlEVAkqmHky"`的结果。

这个奇怪的字符串最终被保存在数据库中，而不是纯文本版本。

* * *

### 修复测试

使用
为您的项目运行测试

```
mix test 
```

Enter fullscreen mode Exit fullscreen mode

现在他们将失败:

```
1) test users create_user/1 with valid data creates a user (MyApp.AuthTest)
     test/my_app/auth/auth_test.exs:32
     Assertion with == failed
     code: assert user.password() == "some password"
     left: "$2b$12$PUK73EqrvBTuOi2RiVrkOexqiVS.wIwbOtyR0EtzQLpbX6gaka8T2"
     right: "some password"
     stacktrace:
       test/my_app/auth/auth_test.exs:36: (test)

  2) test users update_user/2 with valid data updates the user (MyApp.AuthTest)
     test/my_app/auth/auth_test.exs:43
     Assertion with == failed
     code: assert user.password() == "some updated password"
     left: "$2b$12$cccPJfQD3seaBc8pHX8cJO/549lojlAjNNi/qo9QY0K7a7Zm5CaNG"
     right: "some updated password"
     stacktrace:
       test/my_app/auth/auth_test.exs:49: (test) 
```

Enter fullscreen mode Exit fullscreen mode

这是因为我们刚刚做的更改，即哈希密码。

但是这很容易通过将这些断言改为使用`Bcrypt.verify_pass/2`来解决。

打开文件`test/my_app/auth/auth_test.exs`并更改这些行:

```
# ...
test "create_user/1 with valid data creates a user" do
  # ...
  assert user.password == "some password"
end
# ...
test "update_user/2 with valid data updates the user" do
  #...
  assert user.password == "some updated password"
end
# ... 
```

Enter fullscreen mode Exit fullscreen mode

致:

```
# ...
test "create_user/1 with valid data creates a user" do
  # ...
  assert Bcrypt.verify_pass("some password", user.password)
end
# ...
test "update_user/2 with valid data updates the user" do
  # ...
  assert Bcrypt.verify_pass("some updated password", user.password)
end
# ... 
```

Enter fullscreen mode Exit fullscreen mode

现在`mix test`应该不会产生错误。

* * *

# 用户端点

## 生成一个新的 JSON 端点

让我们生成用户 JSON 端点，因为我们已经有了可用的`Auth`上下文和`User`模式，我们将传递`--no-schema`和`--no-context`选项。

```
mix phx.gen.json Auth User users email:string password:string is_active:boolean --no-schema --no-context 
```

Enter fullscreen mode Exit fullscreen mode

### 修复测试

现在，如果您尝试运行您的测试，您将会看到这个错误:

```
== Compilation error in file lib/my_app_web/controllers/user_controller.ex ==
** (CompileError) lib/my_app_web/controllers/user_controller.ex:18: undefined function user_path/3 
```

Enter fullscreen mode Exit fullscreen mode

它在抱怨一个缺失的`user_path/3`功能。

你需要给`lib/my_app_web/router.ex`加一个`resources`行。

在路由器中声明一个资源会为它的控制器提供一些助手——即`user_path/3`。

```
defmodule MyAppWeb.Router do
  # ...
  scope "/api", MyAppWeb do
    pipe_through :api

    resources "/users", UserController, except: [:new, :edit]
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

然而，测试仍然会抱怨。为了解决这些问题，我们需要做两个改变:

1.  注释掉`lib/my_app_web/views/user_view.ex`中的`password`行:

```
defmodule MyAppWeb.UserView do
  # ...
  def render("user.json", %{user: user}) do
    %{id: user.id,
      email: user.email,
      # password: user.password,
      is_active: user.is_active}
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我们不需要——也不应该——在我们的响应中发送散列密码。

1.  因为我们不会在响应中收到哈希密码，所以在`test/my_app_web/controllers/user_controller_test.exs`中注释掉这些`password`行:

```
# ...
assert json_response(conn, 200)["data"] == %{
  "id" => id,
  "email" => "some email",
  "is_active" => true
  # "password" => "some password"
}
# ...
assert json_response(conn, 200)["data"] == %{
  "id" => id,
  "email" => "some updated email",
  "is_active" => false
  # "password" => "some updated password"
}
# ... 
```

Enter fullscreen mode Exit fullscreen mode

测试应该没问题了。

## 创建几个用户

### 使用 IEx

你可以在 IEx (Interactive Elixir)中运行你的应用程序——这类似于`rails console`——使用:

```
iex -S mix phx.server 
```

Enter fullscreen mode Exit fullscreen mode

然后使用
创建一个新用户

```
MyApp.Auth.create_user(%{email: "asd@asd.com", password: "qwerty"}) 
```

Enter fullscreen mode Exit fullscreen mode

### 利用卷曲

如果您的终端中有可用的`curl`，您可以使用类似于
的东西通过您的端点创建一个新用户

```
curl -H "Content-Type: application/json" -X POST \
-d '{"user":{"email":"some@email.com","password":"some password"}}' \
http://localhost:4000/api/users 
```

Enter fullscreen mode Exit fullscreen mode

* * *

# CORS 配置

如果您计划让您的 API 和前端在不同的域上，您将需要对此进行配置。

如果你不知道 **CORS** 是什么，看看这个:[跨产地资源共享(CORS)。](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)

也就是说，我们有两个选择:

*   [CorsPlug](https://github.com/mschae/cors_plug) 。
*   [科西嘉岛](https://github.com/whatyouhide/corsica)。

在本教程中，我将使用 **CorsPlug** ,但是如果你需要更多的特性来配置你的 CORS 请求——或者想要一个更严格的库，试试**科西嘉**。

将此依赖关系添加到`mix.exs` :

```
defp deps do
    [
      # ...
      {:cors_plug, "~> 1.5"}
    ]
  end 
```

Enter fullscreen mode Exit fullscreen mode

使用
获取新的依赖关系

```
mix deps.get 
```

Enter fullscreen mode Exit fullscreen mode

将`plug CORSPlug`添加到`lib/my_app_web/endpoint.ex` :

```
defmodule MyAppWeb.Endpoint do
  # ...
  plug CORSPlug, origin: "http://localhost:8080"

  plug MyAppWeb.Router
  # ...
end 
```

Enter fullscreen mode Exit fullscreen mode

您可以向`origin`传递一个列表，以及一个正则表达式，或者只向`plug CORSPlug`传递一个列表来接受所有来源——因为默认情况下是`origin: "*"`。

在我的例子中，上面的规则将接受来自 **Vue.js** 前端的 CORS 请求——默认情况下，Vue.js 开发服务器在端口 8080 上运行。

* * *

# 简单认证

## 验证用户的密码

让我们向`lib/my_app/auth/auth.ex`文件添加一些函数来验证用户的密码:

```
defmodule MyApp.Auth do
  # ...
  def authenticate_user(email, password) do
    query = from u in User, where: u.email == ^email
    query |> Repo.one() |> verify_password(password)
  end

  defp verify_password(nil, _) do
    {:error, "Wrong email or password"}
  end

  defp verify_password(user, password) do
    case Bcrypt.verify_pass(password, user.password) do
      true -> {:ok, user}
      false -> {:error, "Wrong email or password"}
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

## 签到端点

然后给`lib/my_app_web/router.ex` :
添加一个新的`sign_in`端点

```
defmodule MyAppWeb.Router do
  # ...
  scope "/api", MyAppWeb do
    # ...
    resources "/users", UserController, except: [:new, :edit]
    post "/users/sign_in", UserController, :sign_in
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

## 签到控制器功能

最后将`sign_in`函数添加到`lib/my_app_web/controllers/user_controller.ex` :

```
defmodule MyAppWeb.UserController do
  # ...
  def sign_in(conn, %{"email" => email, "password" => password}) do
    case MyApp.Auth.authenticate_user(email, password) do
      {:ok, user} ->
        conn
        |> put_status(:ok)
        |> render(MyAppWeb.UserView, "sign_in.json", user: user)

      {:error, message} ->
        conn
        |> put_status(:unauthorized)
        |> render(MyAppWeb.ErrorView, "401.json", message: message)
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

### 定义 sing_in.json 和 401.json 视图

在`lib/my_app_web/user_view.ex`中添加以下内容:

```
defmodule MyAppWeb.UserView do
  # ...
  def render("sign_in.json", %{user: user}) do
    %{data:
      %{user:
        %{id: user.id, email: user.email}}}
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

在`lib/my_app_web/error_view.ex`中添加以下内容:

```
defmodule MyAppWeb.ErrorView do
  # ...
  def render("401.json", %{message: message}) do
    %{errors: %{detail: message}}
  end
  # ...
end 
```

Enter fullscreen mode Exit fullscreen mode

您现在可以尝试使用`sign_in`端点。

### 用 curl 试试你的新端点

让我们向`http://localhost:4000/api/users/sign_in`发送一些帖子请求。

#### 良好的凭证

```
curl -H "Content-Type: application/json" -X POST \
-d '{"email":"asd@asd.com","password":"qwerty"}' \
http://localhost:4000/api/users/sign_in -i 
```

Enter fullscreen mode Exit fullscreen mode

您将获得一张 **200** 的奖金，奖金为:

```
{  "data":  {  "user":  {  "id":  1,  "email":  "asd@asd.com"  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

#### 不良凭证

```
curl -H "Content-Type: application/json" -X POST \
-d '{"email":"asd@asd.com","password":"not the right password"}' \
http://localhost:4000/api/users/sign_in -i 
```

Enter fullscreen mode Exit fullscreen mode

你会得到一个 **401** 和:

```
{  "errors":  {  "detail":  "Wrong email or password"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 会话

将`plug :fetch_session`添加到`lib/my_app_web/router.ex` :
中的`:api`管道中

```
defmodule MyAppWeb.Router do
  # ...
  pipeline :api do
    plug :accepts, ["json"]
    plug :fetch_session
  end
  # ...
end 
```

Enter fullscreen mode Exit fullscreen mode

### 保存认证状态

现在让我们修改掉`lib/my_app_web/controllers/user_controller.ex` :
中的`sign_in`功能

```
defmodule MyAppWeb.UserController do
  # ...
  def sign_in(conn, %{"email" => email, "password" => password}) do
    case MyApp.Auth.authenticate_user(email, password) do
      {:ok, user} ->
        conn
        |> put_session(:current_user_id, user.id)
        |> put_status(:ok)
        |> render(MyAppWeb.UserView, "sign_in.json", user: user)

      {:error, message} ->
        conn
        |> delete_session(:current_user_id)
        |> put_status(:unauthorized)
        |> render(MyAppWeb.ErrorView, "401.json", message: message)
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

### 通过认证保护资源

将您的`lib/my_app_web/router.ex`修改成这样:

```
defmodule MyAppWeb.Router do
  use MyAppWeb, :router

  pipeline :api do
    plug :accepts, ["json"]
    plug :fetch_session
  end

  pipeline :api_auth do
    plug :ensure_authenticated
  end

  scope "/api", MyAppWeb do
    pipe_through :api
    post "/users/sign_in", UserController, :sign_in
  end

  scope "/api", MyAppWeb do
    pipe_through [:api, :api_auth]
    resources "/users", UserController, except: [:new, :edit]
  end

  # Plug function
  defp ensure_authenticated(conn, _opts) do
    current_user_id = get_session(conn, :current_user_id)

    if current_user_id do
      conn
    else
      conn
      |> put_status(:unauthorized)
      |> render(MyAppWeb.ErrorView, "401.json", message: "Unauthenticated user")
      |> halt()
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，我们添加了一个名为`:api_auth`的新管道，它将通过一个新的`:ensure_authenticated`插件函数来运行请求。

我们还创建了一个新的`scope "/api"`块，它通过`:api`然后通过`:api_auth`发送请求，并将`resources "/users"`移入其中。

* * *

在凤凰城你能定义这些东西的方式是不是很神奇？！

可组合性 **FTW！**

* * *

# 用 curl 进行端点测试

尝试请求一个受保护的资源，比如用
请求`/api/users`

```
curl -H "Content-Type: application/json" -X GET \
http://localhost:4000/api/users \
-c cookies.txt -b cookies.txt -i 
```

Enter fullscreen mode Exit fullscreen mode

你会得到:

```
{  "errors":  {  "detail":  "Unauthenticated user"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

让我们使用
登录

```
curl -H "Content-Type: application/json" -X POST \
-d '{"email":"asd@asd.com","password":"qwerty"}' \
http://localhost:4000/api/users/sign_in \
-c cookies.txt -b cookies.txt -i 
```

Enter fullscreen mode Exit fullscreen mode

你会得到:

```
{  "data":  {  "user":  {  "id":  1,  "email":  "asd@asd.com"  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

现在，再次尝试请求受保护的资源:

```
curl -H "Content-Type: application/json" -X GET \
http://localhost:4000/api/users \
-c cookies.txt -b cookies.txt -i 
```

Enter fullscreen mode Exit fullscreen mode

你会看到:

```
{  "data":  [  {  "is_active":  false,  "id":  1,  "email":  "asd@asd.com"  },  {  "is_active":  false,  "id":  2,  "email":  "some@email.com"  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

**成功！**

* * *

# 奖金部分

## 定制您的 404s 和 500s JSON 响应

在`lib/my_app_web/views/error_view.ex` :

```
defmodule MyAppWeb.ErrorView do
  # ...
  def render("404.json", _assigns) do
    %{errors: %{detail: "Endpoint not found!"}}
  end

  def render("500.json", _assigns) do
    %{errors: %{detail: "Internal server error :("}}
  end
  # ...
end 
```

Enter fullscreen mode Exit fullscreen mode

# 链接

*   [在 Elixir 和 Phoenix 中从头开始用户验证](http://nithinbekal.com/posts/phoenix-authentication/)
*   【Phoenix 1.3 回退控制器错误提示
*   curl:如何通过命令行发送 cookies？
*   [使用 curl 显示 HTTP 响应头](http://www.linuxask.com/questions/show-http-response-header-using-curl)

* * *

这是一个漫长的，这是现在的乡亲！