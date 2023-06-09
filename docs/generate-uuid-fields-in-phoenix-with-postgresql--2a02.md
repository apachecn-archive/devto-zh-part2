# 使用 Postgresql 在 Phoenix 中生成 UUID 字段

> 原文：<https://dev.to/dreamingechoes/generate-uuid-fields-in-phoenix-with-postgresql--2a02>

如果出于某种原因，您需要为您的模式创建一个`UUID`字段(一个非主键字段)，并且您不想管理存储在新数据库记录上的值的创建，那么您可以在迁移中指定使用`uuid_generate_v4()` **Postgresql** 方法作为默认值，这样它将在插入时自动生成:

```
defmodule App.Repo.Migrations.AddUuidFieldToUser do
  use Ecto.Migration

  def change do
    create table(:users) do
      add(:some_new_field, :uuid, default: fragment("uuid_generate_v4()"))
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

根据您的数据库配置，您可能会在运行迁移时看到以下错误:

```
** (Postgrex.Error) ERROR 42883 (undefined_function): function uuid_generate_v4() does not exist 
```

Enter fullscreen mode Exit fullscreen mode

这是因为您的数据库没有激活`uuid-ossp`模块。您可以通过登录到您的数据库并执行以下查询来激活:

```
CREATE EXTENSION IF NOT EXISTS "uuid-ossp"; 
```

Enter fullscreen mode Exit fullscreen mode

或者，如果您没有访问数据库的权限，您可以创建一个迁移来执行查询，例如:

```
defmodule App.Repo.Migrations.AddUuidGenerateV4ExtensionToDatabase do
  use Ecto.Migration

  def change do
    execute("CREATE EXTENSION IF NOT EXISTS \"uuid-ossp\";")
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

你可以在这里看到更多关于`uuid-ossp`模块[的深度信息。因此，当正确执行迁移时，向您的模式添加新的字段，您就有了它！您新的闪亮的自动生成的`UUID`字段。](https://www.postgresql.org/docs/9.4/static/uuid-ossp.html) 

```
defmodule App.User do
  use Ecto.Schema

  import Ecto.Changeset

  schema "users" do
    ...

    field(:some_new_field, :binary_id)

    timestamps()
  end

  ...
end 
```

Enter fullscreen mode Exit fullscreen mode

> 本帖原载[此处](http://http://dreamingecho.es/til/generate-uuid-fields-in-phoenix-with-postgresql)。