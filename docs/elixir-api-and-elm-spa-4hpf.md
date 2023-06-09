# Elixir API 和 Elm SPA -第 1 部分

> 原文：<https://dev.to/miguelcoba/elixir-api-and-elm-spa-4hpf>

# 第 1 部分:Elixir 应用程序创建

我将创建一个演示应用程序，后端有一个 Elixir API，前端有一个独立的 Elm SPA。该应用程序将是一个简单的 CRUD 应用程序，我发现是迄今为止最好的做法。该应用程序将是一个简单的市场应用程序，用户可以发布出售商品的公告，其他用户可以看到报价并购买它们。这款应用的名字将被命名为 Toltec。

## 系列

1.  第 1 部分- Elixir 应用程序创建
2.  [第 2 部分-增加监护人认证](https://dev.to/miguelcoba/elixir-api-and-elm-spa---part-2-1pca)
3.  [第 3 部分:Elm 应用程序创建和路由设置](https://dev.to/miguelcoba/elixir-api-and-elm-spa---part-3-3n0h)
4.  [第 4 部分:添加登录和注册页面](https://dev.to/miguelcoba/elixir-api-and-elm-spa---part-4-4il0)
5.  [第 5 部分:将会话数据保存到本地存储器](https://dev.to/miguelcoba/elixir-api-and-elm-spa---part-5-4j1k)

## 假设

我假设您正在使用:

*   酏剂
*   二郎 20
*   榆树 0.18
*   PostgreSQL 10.4

## 创建 App

让我们为 API 创建一个基本的应用程序。我们不需要 html 或 brunch，因为我们根本不渲染 html。我们将公开一个 JSON REST API:

```
mix phx.new toltec-api --app toltec --no-brunch --no-html --binary-id 
```

Enter fullscreen mode Exit fullscreen mode

## 配置自动格式化

在添加任何代码之前，让我们配置代码自动格式化。在项目的根目录下添加一个名为 *.formatter.exs* :
的文件

```
# .formatter.exs

[
  inputs: ["mix.exs", "{config,lib,test}/**/*.{ex,exs}"]
] 
```

Enter fullscreen mode Exit fullscreen mode

和运行格式化命令:

```
mix format 
```

Enter fullscreen mode Exit fullscreen mode

这将格式化项目中的所有代码。

现在，您需要配置您的编辑器，以便在保存文件时自动运行该命令。例如，对于 VS 代码，您可以使用 [vscode-elixir](https://marketplace.visualstudio.com/items?itemName=mjmcloug.vscode-elixir) 和 [vscode-elixir-formatter](https://marketplace.visualstudio.com/items?itemName=sammkj.vscode-elixir-formatter) 扩展。

## 创建账户

我们将使用[come in](https://github.com/riverrun/comeonin)包来处理我们应用程序中的密码散列。在 mix.exs
中添加这个依赖项

```
 # mix.exs

  defp deps do
    [
      {:phoenix, "~> 1.3.0"},
      {:phoenix_pubsub, "~> 1.0"},
      {:phoenix_ecto, "~> 3.2"},
      {:postgrex, ">= 0.0.0"},
      {:gettext, "~> 0.11"},
      {:cowboy, "~> 1.0"},
      {:comeonin, "~> 4.0"},
      {:argon2_elixir, "~> 1.2"}
    ]
  end 
```

Enter fullscreen mode Exit fullscreen mode

并获取依赖关系

```
mix deps.get 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们添加用户模式和帐户上下文来保存所有这些逻辑

```
mix phx.gen.context Accounts User users name:string email:string:unique password_hash:string 
```

Enter fullscreen mode Exit fullscreen mode

这将创建用户迁移、用户模式和帐户上下文。对于用户，我不会使用 UUID 主键，所以我将从用户迁移中删除 binary_id 配置，但是如果您愿意，可以保留它。
将迁移更改为:

```
# priv/repo/migrations/20180612062911_create_users.exs

  def change do
    execute("CREATE EXTENSION citext;")

    create table(:users) do
      add(:name, :string, null: false)
      add(:email, :citext, null: false)
      add(:password_hash, :string, null: false)

      timestamps()
    end

    create(unique_index(:users, [:email]))
  end 
```

Enter fullscreen mode Exit fullscreen mode

让我们向用户模式添加一些基本方法:

```
 # lib/toltec/accounts/user.ex

 defmodule Toltec.Accounts.User do
  use Ecto.Schema
  import Ecto.Changeset
  alias Toltec.Accounts.User

  schema "users" do
    field(:email, :string)
    field(:name, :string)
    field(:password, :string, virtual: true)
    field(:password_hash, :string)

    timestamps()
  end

  def changeset(%User{} = user, attrs) do
    user
    |> cast(attrs, [:name, :email])
    |> validate_required([:name, :email])
    |> validate_length(:name, min: 2, max: 255)
    |> validate_length(:email, min: 5, max: 255)
    |> unique_constraint(:email)
    |> validate_format(:email, ~r/@/)
  end

  def registration_changeset(%User{} = user, attrs) do
    user
    |> changeset(attrs)
    |> cast(attrs, [:password])
    |> validate_required([:password])
    |> validate_length(:password, min: 8, max: 100)
    |> put_password_hash()
  end

  defp put_password_hash(changeset) do
    case changeset do
      %Ecto.Changeset{valid?: true, changes: %{password: password}} ->
        put_change(changeset, :password_hash, Comeonin.Argon2.hashpwsalt(password))

      _ ->
        changeset
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我添加了一个*密码*虚拟字段，用来临时保存明文密码。这将永远不会保存到数据库中。我还添加了两个变更集，一个用于更改密码，一个用于其他字段。 *put_password_hash()* 函数使用 comeonin 库获得密码的散列，并将其放在将存储在 DB 中的 *password_hash* 字段中。

现在修改包含帐户所有逻辑的帐户上下文，如下所示:

```
# lib/toltec/accounts/accounts.ex

defmodule Toltec.Accounts do
  import Ecto.Query, warn: false
  alias Toltec.Repo

  alias Toltec.Accounts.User

  def list_users do
    Repo.all(User)
  end

  def get_user!(id), do: Repo.get!(User, id)

  def create_user(attrs \\ %{}) do
    result =
      %User{}
      |> User.registration_changeset(attrs)
      |> Repo.insert()

    case result do
      {:ok, user} -> {:ok, %User{user | password: nil}}
      _ -> result
    end
  end

  def update_user(%User{} = user, attrs) do
    user
    |> User.changeset(attrs)
    |> Repo.update()
  end

  def delete_user(%User{} = user) do
    Repo.delete(user)
  end

  def change_user(%User{} = user) do
    User.changeset(user, %{})
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

接下来，让我们向数据库添加一些种子数据，以使我们的应用程序可用。

```
# priv/repo/seeds.exs

# users
user =
  Toltec.Accounts.User.registration_changeset(%Toltec.Accounts.User{}, %{
    name: "some user",
    email: "user@toltec",
    password: "user@toltec"
  })

Toltec.Repo.insert!(user) 
```

Enter fullscreen mode Exit fullscreen mode

现在是构建数据库、创建表和插入种子数据的时候了。

```
mix ecto.reset 
```

Enter fullscreen mode Exit fullscreen mode

该命令将删除数据库(如果存在)，重新创建它，按顺序运行迁移，最后运行 seeds.exs 文件以插入初始数据。您应该在输出中看不到任何错误，应该类似于以下内容。

```
mix ecto.reset
The database for Toltec.Repo has already been dropped
The database for Toltec.Repo has been created
[info] == Running Toltec.Repo.Migrations.CreateUsers.change/0 forward
[info] execute "CREATE EXTENSION citext;"
[info] create table users
[info] create index users_email_index
[info] == Migrated in 0.0s
[debug] QUERY OK db=2.8ms
INSERT INTO "users" ("email","name","password_hash","inserted_at","updated_at") VALUES ($1,$2,$3,$4,$5) RETURNING "id" ["user@toltec", "some user", "$argon2i$v=19$m=65536,t=6,p=1$jdZHJ4HdGSF34hP6iTiPeQ$+PWBp77RP8wxMasVj0wv1wWS33pponixKSEG109V/u8", {{2018, 6, 12}, {22, 35, 55, 411334}}, {{2018, 6, 12}, {22, 35, 55, 411345}}] 
```

Enter fullscreen mode Exit fullscreen mode

你可以在分支 part-01 的 repo [这里的](https://github.com/miguelcoba/toltec-api)中找到带有测试的源代码。

克隆它之后，运行测试并验证一切正常:

```
mix test
...................

Finished in 0.3 seconds
19 tests, 0 failures 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，我们已经创建了应用程序、用户模式和一些初始用户来使用我们的应用程序。
目前就这样。