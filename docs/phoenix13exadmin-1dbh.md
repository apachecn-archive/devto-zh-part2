# Phoenix1.3でex_adminを使う

> 原文：<https://dev.to/dala00/phoenix13exadmin-1dbh>

在 Phoenix 中，通过导入 ex_admin 管理画面制作软件包，可以简单地制作管理画面。

[smpallen99/ex_admin: ExAdmin 是 Elixir 和 Phoenix Framework 的自动管理包](https://github.com/smpallen99/ex_admin)

但是由于依存关系和文件夹构成的差异问题，在最新的 Phoenix 上无法顺利运行。 只是，仔细调查后发现，使用下述 phx-1.3 分支似乎就能完成，所以试了一下。

[https://github.com/smpallen99/ex_admin/tree/phx-1.3](https://github.com/smpallen99/ex_admin/tree/phx-1.3)

### Import method

指定 phx-1.3 分支放入。 因为构建失败，所以 gettext 的版本也会提高。

mix.exs

```
defp deps do
  ...
  {:gettext, "~> 0.13.1"},
  {:ex_admin, github: "smpallen99/ex_admin", branch: "phx-1.3"},
  ...
end 
```

Enter fullscreen mode Exit fullscreen mode

添加配置。

exs 配置〔t0〕

```
config :ex_admin,
  repo: MyProject.Repo,
  module: MyProjectWeb,
  modules: [
    MyProject.ExAdmin.Dashboard,
  ] 
```

Enter fullscreen mode Exit fullscreen mode

之后和往常的安装一样。

### brunch-configの修正

就这样的话，ex_admin 的 css 之类的会混合在一起。 brunch-config.js 中记载了修正方法，请按照该方法进行修正。

另外，此时，如果正在导入 node_modules 内的 css，则只需要对 css 的设定进行以下修改。

```
 "css/app.css": /^(css|node_modules)/, 
```

Enter fullscreen mode Exit fullscreen mode

### 追加模型

```
mix admin.gen.resource User 
```

Enter fullscreen mode Exit fullscreen mode

这个没有什么变化。 但是，如果是 1.3 的话，在使用`phx.gen.html`的情况下，我认为在 context module 以下加入了模型。 例如`Accounts.User`等。

所以只修正生成的 admin/user.ex 内的模型的指定。

admin/user.ex(之前)

```
 register_resource MyProject.User do 
```

Enter fullscreen mode Exit fullscreen mode

admin/user.ex(之后)

```
 register_resource MyProject.Accounts.User do 
```

Enter fullscreen mode Exit fullscreen mode

这样就运作了。