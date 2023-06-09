# 向实体属性添加唯一的可空索引

> 原文：<https://dev.to/alexruzenhack/add-a-unique-nullable-index-to-an-entity-property-25jo>

## 问题

我正在进行一个 MVC 项目，为了简化我们的生活，团队选择使用 ASP.NET 核心身份组件来创建 web 应用程序的认证和授权逻辑。

然而，当用户在应用程序上注册自己时，会为他们生成一个新的 Id，尽管该公司可能已经有一个 Id。

然后，为了将新的 Id 与原有的 Id 链接在一起，我扩展了默认的`IdentityUser`来拥有`MyCompanyId`属性，这样:

```
public class CustomUser : IdentityUser
{
    public int MyCompanyId { get; set; }
} 
```

Enter fullscreen mode Exit fullscreen mode

为了使这个更改生效，我扩展了`IdentityDbContext`来使用我的`CustomUser`，并且将属性`MyCompanyId`注册为一个惟一的索引，就像这样:

```
public class CustomDbContext : IdentityDbContext<CustomUser>
{
    public CustomDbContext(DbContextOptions<CustomDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder builder)
    {
        base.OnModelCreating(builder);

        builder.Entity<CustomUser>(user => 
        {
            user.HasIndex(x => x.MyCompanyId).IsUnique(true);
        });
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这样，当用户自己注册时，一个默认值被分配给`MyCompanyId`属性，即`0`。当另一个用户试图注册时，相同的值将被分配给他们的`MyCompanyId`，并且它将抛出一个重复键的错误。

发生这种情况是因为当实体被转录到 fluentish API 进行迁移时，默认情况下，列被设置为不可空，并且必须分配一个默认值，正如我们看到的`0`。

```
// hidden for brevity
...
migrationBuilder.CreateTable(
    name: "AspNetUsers",
    columns: table => new
    {
        Id = table.Column<string>(nullable: false),
        ...
        MyCompanyId = table.Column<int>(nullable: false)
    },
... 
```

Enter fullscreen mode Exit fullscreen mode

并且索引为所有值分配唯一性:

```
// hidden for brevity
...
migrationBuilder.CreateIndex(
    name: "IX_AspNetUsers_MyCompanyId",
    table: "AspNetUsers",
    column: "MyCompanyId",
    unique: true);
... 
```

Enter fullscreen mode Exit fullscreen mode

## 解

如何解决这一切，并允许一个可空的唯一索引？这是**所以**简单，伙计！只需在属性的类型后添加`?`符号，这样:

```
public class CustomUser : IdentityUser
{
    public int? MyCompanyId { get; set; }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在该属性是可选的，它将一直向下传播。让我们来看看迁移 fluentish API:

```
// hidden for brevity
...
migrationBuilder.CreateTable(
    name: "AspNetUsers",
    columns: table => new
    {
        Id = table.Column<string>(nullable: false),
        ...
        MyCompanyId = table.Column<int>(nullable: true) // now it's true
    },
... 
```

Enter fullscreen mode Exit fullscreen mode

并且索引有一个新的变量`filter`,它跳过空值作为惟一值。这意味着您可以拥有任意多的空值。

```
// hidden for brevity
...
migrationBuilder.CreateIndex(
    name: "IX_AspNetUsers_MyCompanyId",
    table: "AspNetUsers",
    column: "MyCompanyId",
    unique: true,
    filter: "[MyCompanyId] IS NOT NULL");
... 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！