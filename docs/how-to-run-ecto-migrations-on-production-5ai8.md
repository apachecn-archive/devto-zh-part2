# 如何在生产环境中运行 Ecto 迁移

> 原文：<https://dev.to/ktravers/how-to-run-ecto-migrations-on-production-5ai8>

你可能会认为这个问题的答案是简单的谷歌搜索。不幸的是，今天下午我的情况并非如此，我在做一个 [Phoenix](http://phoenixframework.org/) 项目，其中新增了 [Ecto](https://hexdocs.pm/ecto/getting-started.html) 后端。为了拯救其他人(老实说，未来的我)同样的沮丧，这里是我发现的最直接的解决方案。

## 什么不起作用

[`Mix`](https://elixirschool.com/lessons/basics/mix/) 。`Mix`任务没有被编译到你的部署版本中，正如[这场激动人心的讨论](https://github.com/bitwalker/exrm/issues/67)所证明的，近期内没有计划改变这一点。

因此，不要尝试在生产中使用您信任的本地任务。在这里帮不了你。

## 什么管用

### 1。[埃克托。迁移者](https://hexdocs.pm/ecto/Ecto.Migrator.html)

埃克托配有[埃克托。迁移器](https://hexdocs.pm/ecto/Ecto.Migrator.html)，是 Ecto 的迁移 API 的一级模块。

通过 ssh 连接到您的应用程序服务器，连接到您的 Phoenix 应用程序，并运行以下命令来手动运行它:

```
path = Application.app_dir(:my_app, "priv/repo/migrations")

Ecto.Migrator.run(MyApp.Repo, path, :up, all: true) 
```

Enter fullscreen mode Exit fullscreen mode

理想情况下，您应该将上述内容包装在它自己的任务中，该任务可以在您的构建和部署过程中调用。查看 Plataformatec 的博客，了解一个很好的例子。

### 2。eDeliver

我们的应用程序使用 [`edeliver`](https://github.com/edeliver/edeliver) 进行部署，并且它有一个超级方便的命令来手动运行迁移:

```
mix edeliver migrate production 
```

Enter fullscreen mode Exit fullscreen mode

如果我们看一下源代码，就会发现这个命令实际上只是为你包装了`Ecto.Migrator`，节省了一些宝贵的击键时间。

要成功运行，您需要将`ECTO_REPOSITORY="MyApp.Repo"`添加到您的`.deliver/config`文件中。

同样，Plataformatec 有一篇关于用 eDeliver 部署你的 Elixir 应用的博文。

## 总结

嗨未来的我！希望这篇文章第 n 次仍有帮助。

### 参考文献:

*   [凤凰电子集成](https://github.com/phoenixframework/phoenix_ecto)
*   [埃克托。迁移者](https://hexdocs.pm/ecto/Ecto.Migrator.html)
*   [eDeliver](https://github.com/edeliver/edeliver)