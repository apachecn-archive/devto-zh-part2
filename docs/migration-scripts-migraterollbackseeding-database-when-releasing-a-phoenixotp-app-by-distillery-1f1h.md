# Distillery 发布 Phoenix/OTP 应用程序时的迁移脚本(迁移/回滚/播种)数据库

> 原文：<https://dev.to/vinhnglx/migration-scripts-migraterollbackseeding-database-when-releasing-a-phoenixotp-app-by-distillery-1f1h>

大家好！

在这篇文章中，我想分享发布 Phoenix/OTP 应用程序时的迁移脚本(迁移、逐步回滚和播种数据)。这个脚本是从 Distillery 的创建者、我和我的同事的最初的[运行迁移文档](https://hexdocs.pm/distillery/running-migrations.html)扩展而来的——我们编写了更多的函数来支持数据库回滚和播种数据。

### 第一步:

*   初始化一个新模块

```
# apps/myapp/lib/migration.ex

defmodule MyApp.ReleaseTasks do
  @start_apps [
    :postgrex,
    :ecto
  ]
  @app :myapp

  def repos, do: Application.get_env(@app, :ecto_repos, [])

  def seed do
    prepare()
    # Run seed script
    Enum.each(repos(), &run_seeds_for/1)

    # Signal shutdown
    IO.puts("Success!")
  end

  defp run_seeds_for(repo) do
    # Run the seed script if it exists
    seed_script = seeds_path(repo)

    if File.exists?(seed_script) do
      IO.puts("Running seed script..")
      Code.eval_file(seed_script)
    end
  end

  def migrate do
    prepare()
    Enum.each(repos(), &run_migrations_for/1)
    IO.puts("Migrations successful!")
  end

  defp run_migrations_for(repo) do
    app = Keyword.get(repo.config, :otp_app)
    IO.puts("Running migrations for #{app}")
    Ecto.Migrator.run(repo, migrations_path(repo), :up, all: true)
  end

  def rollback do
    prepare()

    get_step =
      IO.gets("Enter the number of steps: ")
      |> String.trim()
      |> Integer.parse()

    case get_step do
      {int, _trailing} ->
        Enum.each(repos(), fn repo -> run_rollbacks_for(repo, int) end)
        IO.puts("Rollback successful!")

      :error ->
        IO.puts("Invalid integer")
    end
  end

  defp run_rollbacks_for(repo, step) do
    app = Keyword.get(repo.config, :otp_app)
    IO.puts("Running rollbacks for #{app} (STEP=#{step})")
    Ecto.Migrator.run(repo, migrations_path(repo), :down, all: false, step: step)
  end

  defp prepare do
    IO.puts("Loading #{@app}..")
    # Load the code for myapp, but don't start it
    :ok = Application.load(@app)

    IO.puts("Starting dependencies..")
    # Start apps necessary for executing migrations
    Enum.each(@start_apps, &Application.ensure_all_started/1)

    # Start the Repo(s) for myapp
    IO.puts("Starting repos..")
    Enum.each(repos(), & &1.start_link(pool_size: 1))
  end

  defp seeds_path(repo), do: priv_path_for(repo, "seeds.exs")

  defp priv_path_for(repo, filename) do
    app = Keyword.get(repo.config, :otp_app)
    IO.puts("App: #{app}")
    repo_underscore = repo |> Module.split() |> List.last() |> Macro.underscore()
    Path.join([priv_dir(app), repo_underscore, filename])
  end

  defp priv_dir(app), do: "#{:code.priv_dir(app)}"
end 
```

Enter fullscreen mode Exit fullscreen mode

### 第二步:

创建 shell 脚本文件来调用函数:

*   在`rel/commands/migrate.sh`迁移脚本

```
#!/bin/sh

$RELEASE_ROOT_DIR/bin/myapp command Elixir.MyApp.ReleaseTasks migrate 
```

Enter fullscreen mode Exit fullscreen mode

*   在`rel/commands/seed.sh`的种子脚本

```
#!/bin/sh

$RELEASE_ROOT_DIR/bin/myapp command Elixir.MyApp.ReleaseTasks seed 
```

Enter fullscreen mode Exit fullscreen mode

*   在`rel/commands/rollback.sh`回滚脚本

```
#!/bin/sh

$RELEASE_ROOT_DIR/bin/myapp command Elixir.MyApp.ReleaseTasks rollback 
```

Enter fullscreen mode Exit fullscreen mode

### 第三步:

从`rel/config.exs`到
的设置命令

```
release :myapp do
  set version: current_version(:myapp)
  set applications: [
    :runtime_tools,
    :misc_random,
    admin: :permanent, graphql: :permanent,
    redis: :permanent,
    session: :permanent,
    myapp: :permanent
  ]

  set commands: [
    "seed": "rel/commands/seed.sh",
    "migrate": "rel/commands/migrate.sh",
    "rollback": "rel/commands/rollback.sh",
  ]
end 
```

Enter fullscreen mode Exit fullscreen mode

### 第四步:

部署应用程序后，您可以运行:

*   移动

```
bin/myapp migrate 
```

Enter fullscreen mode Exit fullscreen mode

*   种子

```
bin/myapp seed 
```

Enter fullscreen mode Exit fullscreen mode

*   回滚。您可以指定想要回滚的步数

```
bin/myapp rollback 
```

Enter fullscreen mode Exit fullscreen mode

你们可以从我的[要点](https://gist.github.com/vinhnglx/8fa626b71b0dd22063f87de54e935316)下载代码。谢谢！