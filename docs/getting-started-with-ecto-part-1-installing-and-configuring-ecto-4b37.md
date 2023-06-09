# Ecto 入门第 1 部分:安装和配置 Ecto

> 原文：<https://dev.to/edgar971/getting-started-with-ecto-part-1-installing-and-configuring-ecto-4b37>

欢迎来到《Ecto 入门》的第一部分。这篇文章是教你如何使用 Ecto 的系列文章的一部分。我将介绍如何设置 Ecto、创建迁移和模式，以及简单和复杂的查询。到本系列结束时，您将能够在您的酏剂应用程序中轻松地使用 Ecto 和 Postgres。

## 什么是 Ecto？

Ecto 是一个用于 Elixir 应用程序的数据库包。它用于编写数据库查询和与数据库交互。使用 Ecto，您可以创建迁移、定义模式、创建和更新记录以及查询数据库。当前版本支持 PostgreSQL 和 MySQL。将来会增加对 MSSQL、SQLite 和 MongoDB 的支持。

## 安装和设置

让我们从在我们的 Elixir 应用程序中添加和配置 Ecto 开始。如果您已经这样做了，或者如果您正在使用 Phoenix，请随意跳过这一步。

1.  让我们将`ecto`和`postgrex`包添加到`mix.exs`文件中的`deps`函数中:

```
defp deps do
    [
      {:ecto, "~> 2.2"},
      {:postgrex, "~> 0.13.5"}
    ]
end 
```

注意:Postgrex 用于对我们的 Postgres 数据库执行 Ecto 查询。

1.  运行`mix deps.get`来安装我们添加的依赖项。

现在我们已经安装了这些包，让我们来配置 Ecto。我们可以通过运行下面的[生成器命令](https://hexdocs.pm/ecto/Mix.Tasks.Ecto.html) :
来实现

```
mix ecto.gen.repo -r GettingStartedWithEcto.Repo 
```

这将在`lib/getting_started_with_ecto/repo.ex` :
中生成我们的 Repo，用于连接和查询我们的数据库

```
defmodule GettingStartedWithEcto.Repo do
  use Ecto.Repo, otp_app: :getting_started_with_ecto
end 
```

并更新我们的配置以连接到数据库:

```
config :getting_started_with_ecto, GettingStartedWithEcto.Repo,
  adapter: Ecto.Adapters.Postgres,
  database: "getting_started_with_ecto_repo",
  username: "user",
  password: "pass",
  hostname: "localhost" 
```

生成器没有做的一件事是告诉我们的长生不老药应用程序关于我们的`GettingStartedWithEcto.Repo`。
在`config.exs`的末尾增加以下一行:

```
config :getting_started_with_ecto, ecto_repos: [GettingStartedWithEcto.Repo] 
```

注意:您的 Postgres 配置可能会有所不同。

1.  如果您在连接时遇到问题，请仔细检查您的用户名、密码和主机。
2.  使用默认端口`5432`，但是您可以通过添加到上面的配置来更改它:`port: 15432`

我们需要做的最后一件事是将`GettingStartedWithEcto.Repo`设置为`lib/getting_started_with_ecto/application.ex` :
内我们应用程序的
监督树中的监督人

```
def start(_type, _args) do
    children = [
      GettingStartedWithEcto.Repo
    ]
    opts = [strategy: :one_for_one, name: GettingStartedWithEcto.Supervisor]
    Supervisor.start_link(children, opts)
end 
```

## 创建数据库

如果安装和配置正确，最后一步应该很容易。

以下命令创建我们的数据库:

```
mix ecto.create 
```

如果一切顺利，您应该会看到以下消息:

```
The database for GettingStartedWithEcto.Repo has been created 
```

`GettingStartedWithEcto.Repo`是处理数据库查询的存储库。

## 祝贺你🎉 🎉 🎉

您已经安装并配置了 Ecto。在下一篇文章中，我们将学习如何在我们的 Elixir 应用程序中创建迁移和模式。