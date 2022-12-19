# 在 Elixir with Ecto 中深入探讨数据库多租户

> 原文：<https://dev.to/dreamingechoes/a-dive-into-database-multi-tenancy-in-elixir-with-ecto-171h>

前一段时间，我不得不处理将一个应用程序从常规数据库迁移到多租户数据库的问题，我想在这里分享一下我的经验。

尽管存在像 [apartmentex](https://github.com/Dania02525/apartmentex) 、 [triplex](https://github.com/ateliware/triplex) 或 [tenantex](https://github.com/jeffdeville/tenantex) 这样的库，我还是想尽可能地使迁移不依赖于其他库，所以我为多租户做了自己的管理。

## 了解 Ecto 前缀选项

与数据库直接交互的 [Ecto](https://hexdocs.pm/ecto/Ecto.html) 提供的方法大多有一系列选项，其中对于多租户最重要的是**前缀**。根据文档，对于 **Postgres** 用户，**前缀**将指定**表所在的模式**，而对于 **MySQL** 用户将指定**表所在的数据库**。当没有设置**前缀**时， **Postgres** 查询被假定在公共模式中执行，而 **MySQL** 查询被假定在 repo 的配置中设置的数据库中执行。

例如，如果我们想用[Repo.insert/2](https://hexdocs.pm/ecto/Ecto.Repo.html#c:insert/2)在我们的数据库中插入一个新元素，我们执行类似这样的代码:

```
case MyAppRepo.insert(%User{email: "user@example.com"}) do
  {:ok, struct}       -> # Inserted with success
  {:error, changeset} -> # Something went wrong
end 
```

要在多租户应用程序中做到这一点，我们需要为插入指定**前缀**选项，就像这样:

```
case MyAppRepo.insert(%User{email: "user@example.com"}, prefix: "some_tenant") do
  {:ok, struct}       -> # Inserted with success
  {:error, changeset} -> # Something went wrong
end 
```

## 我们应用的主要变化

为了让我们的应用程序能够处理多租户数据库，我们必须对代码进行一些修改。让我们用例子来看看主要的变化。

### 默认凤凰脚手架

当我们使用`mix phx.gen.html`任务时，会生成一系列文件，其中包括与 repo 交互的所有函数的上下文。例如，负责创建新元素的函数看起来像:

```
def create_user(attrs \\ %{}) do
  %User{}
  |> User.changeset(attrs)
  |> Repo.insert()
end 
```

为了支持多租户功能，我们必须在所有与回购进行交互的功能中包含(`create`、`update`、`delete`、`list`)...)的`tenant`参数来指定**前缀的**选项:

```
def create_user(attrs \\ %{}, tenant) do
  %User{}
  |> User.changeset(attrs)
  |> Repo.insert(prefix: tenant)
end 
```

### 创建新租户

如果我们想要创建一个新的租户，我们需要根据我们使用的数据库执行适当的查询(因为 **Postgres** 将是`CREATE SCHEMA`)。所以我们会有一个类似于
的函数

```
def create_schema(repo, tenant) do
  SQL.query(repo, "CREATE SCHEMA \"#{tenant}\"", [])
end 
```

### 列出可用的租户

列出所有可用的租户将允许我们轻松地在它们之间切换。为了获得这些信息，我们必须对`information_schema`模式进行查询，并选择`schemata`表的`schema_name`字段。

为了能够识别我的应用程序的模式，我喜欢使用一些 ***前缀*** 来区分我的模式和其他模式(比如`tenant_`)，这就是我在查询中使用`schema_prefix`参数的原因。

```
def list_tenants(repo, schema_prefix) do
  query =
    from(
      schemata in "schemata",
      select: schemata.schema_name,
      where: like(schemata.schema_name, ^"#{schema_prefix}%")
    )

  repo.all(query, prefix: "information_schema")
end 
```

如果您想检查这个函数是否返回正确的信息，您可以进入 **Postgres** 控制台并执行下面的查询:

```
database_dev=# SELECT schema_name FROM information_schema.schemata WHERE schema_name LIKE 'tenant_%'; 
```

### 删除特定租户

与创建一样，如果我们想要删除一个特定的租户，我们需要根据我们使用的数据库执行适当的查询(因为 **Postgres** 将是`DROP SCHEMA`)。所以我们会有一个类似于
的函数

```
def drop_schema(repo, tenant) do
  SQL.query(repo, "DROP SCHEMA \"#{tenant}\" CASCADE", [])
end 
```

### 在特定租户上创建和运行迁移

`mix ecto.gen.migration`任务创建一个新文件，我们希望将它迁移到应用程序的`priv/repo/migrations`文件夹中。如果我们需要能够在公共模式和定制生成的模式上运行迁移，我们需要创建一个新的`priv/repo/tenant_migrations`文件夹，并在该文件夹中存储只在定制生成的模式上运行的迁移。

我们可以创建一个自定义任务，以便直接在该文件夹中生成和存储新的迁移。为了避免在这篇文章中放太多代码，[这里有一个任务的例子，所以我们可以在控制台上运行类似这样的东西:](https://github.com/dreamingechoes/multi_tenancex/blob/master/lib/multi_tenancex/mix/tasks/multi_tenancex.gen.tenant_migration.ex) 

```
user@computer:~$ mix app_name.gen.tenant_migration add_users_table 
```

一旦我们在`priv/repo/tenant_migrations`文件夹中有了新的迁移，我们也可以创建一个定制任务来为所有定制生成的模式运行迁移。同样，为了避免在这篇文章中写太多代码，[这里有一个任务的例子](https://github.com/dreamingechoes/multi_tenancex/blob/master/lib/multi_tenancex/mix/tasks/multi_tenancex.ecto.migrate_tenants.ex)，所以我们可以在控制台上运行类似这样的东西:

```
user@computer:~$ mix app_name.ecto.migrate_tenants 
```

最后，我们可以创建一个定制任务，为所有定制生成的模式回滚迁移。这里你有一个任务的例子，所以我们可以在控制台上运行类似这样的东西:

```
user@computer:~$ mix app_name.ecto.rollback_tenants 
```

在文章的最后，我会留下一个链接，链接到一个包含所有示例的示例 **Phoenix** 应用程序，所以如果我没有在这里指定代码，请不要担心。

### 在我们的应用程序中存储当前租户

我们需要有当前的租户，在这个租户中，我们必须执行应用程序中所有的回购功能。对于这一点，对我来说效果最好的是使用 [Guardian](https://github.com/ueberauth/guardian) 来存储当前租户，作为对`sign_in`的声明。因此，举例来说，如果我们有一个控制器来管理会话，`create`函数将如下所示:

```
def create(conn, %{
      "session" => %{
        "tenant" => tenant,
        "email" => email,
        "password" => password
      }
    }) do
  case Guardian.authenticate_user(email, password) do
    {:ok, user} ->
      conn
      |> Plug.sign_in(user, %{current_tenant: tenant})
      |> put_flash(:success, gettext("Welcome to AppName!"))
      |> redirect(to: page_path(conn, :index))

    {:error, message} ->
      conn
      |> put_flash(:error, message)
      |> redirect(to: session_path(conn, :new))
  end
end 
```

一旦用户成功登录，我们将能够在`conn`的`assigns`上访问该声明，因此，使用之前看到的关于创建用户的示例，我们可以做如下事情:

```
Accounts.create_user(user_attrs, conn.assigns.current_tenant) 
```

## 记在心里的事情

一旦您完成了我们应用程序代码的迁移，我们就必须注意与 repo 直接交互的依赖关系。我看到的大多数包都不允许用户为回购传播`opts`，所以我们不能在其中使用多租户，**所以要小心**。如果你要创建一个新的包，**请允许用户传播 repo** `opts`。

## 多租户应用示例

我在 **Phoenix** 中开发了一个小应用程序，能够检查这里看到的所有概念的真实行为。你可以查看这个回购中的代码[。](https://github.com/dreamingechoes/multi_tenancex)

> 本帖原载[此处](http://dreamingecho.es/blog/a-dive-into-database-multi-tenancy-in-elixir-with-ecto)。