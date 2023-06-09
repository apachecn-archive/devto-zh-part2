# 实体框架核心 2.1 功能

> 原文：<https://dev.to/faizanhussainrabbani/entity-framework-core-21-features-20j3>

## 什么是实体框架核心？

实体框架核心(EF Core)是一个轻量级的、可扩展的、跨平台的实体框架版本。

与 EF6 相比，EF Core 引入了许多改进和新功能。

EF Core 保留了来自 EF6 的开发者体验，并且大多数顶级 API 也保持不变，所以 EF Core 对于使用过 EF6 的人来说会感觉非常熟悉。

同时，EF 核心是一个新的代码库，不像 EF6 那样成熟。

## EF Core 2.1 中引入的新功能:

#### 1。带有构造函数的实体类型

现在可以用参数定义一个构造函数，并让 EF Core 在创建实体的实例时调用这个构造函数。

构造函数参数可以绑定到映射的属性，或者绑定到各种类型的服务，以便于像延迟加载这样的行为，并且可以用于注入属性值、延迟加载委托和服务。

示例:

```
public class Employee
{
    public Employee(string name, string designation)
    {
        Name = name;
        Designation = designation;
    }
    [Key]
    public int EmpId { get; set; }
    public string Name { get; set; }
    public string Designation { get; set; }
} 
```

Enter fullscreen mode Exit fullscreen mode

需要考虑的几点:

*   并非所有属性都需要构造函数参数。如果一个属性没有被任何构造函数参数设置，EF Core 会在以正常方式调用构造函数后设置它。
*   参数类型和名称必须与属性类型和名称匹配，只是属性可以是 Pascal 大小写，而参数是 camel 大小写。
*   EF 核心无法使用构造函数设置导航属性。
*   构造函数可以是公共的、私有的或具有任何其他可访问性。

#### 2。价值转换

值转换器允许在读取或写入数据库时转换属性值。

这种转换可以是从一个值到另一个相同类型的值(例如，加密字符串)或者

从一种类型的值转换为另一种类型的值(例如，在数据库中的字符串和枚举值之间进行转换。)

```
public class Rider
{
    public int Id { get; set; }
    public EquineBeast Mount { get; set; }
}

public enum EquineBeast
{
    Donkey,
    Mule,
    Horse,
    Unicorn
} 
```

Enter fullscreen mode Exit fullscreen mode

然后可以在 OnModelCreating 中定义转换，将枚举值存储为字符串(例如，“驴子”，“骡子”，...)在数据库中:

```
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Rider>()
        .Property(e => e.Mount)
        .HasConversion(
            v => v.ToString(),
            v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));
} 
```

Enter fullscreen mode Exit fullscreen mode

局限性:

价值转换系统目前有一些已知的限制:

1.  如上所述，null 不能被转换。
2.  目前没有办法将一个属性的转换扩展到多个列，反之亦然。
3.  使用值转换可能会影响 EF Core 将表达式转换为 SQL 的能力。对于这种情况，将记录一个警告。正在考虑在未来的版本中消除这些限制。

#### 3。查询类型

EF 核心模型现在可以包含查询类型。与实体类型不同，查询类型没有定义键，不能插入、删除或更新(即它们是只读的)，但可以由查询直接返回。查询类型的一些使用场景包括:

*   映射到没有主键的视图
*   映射到没有主键的表
*   映射到模型中定义的查询
*   充当 FromSql()查询的返回类型

```
db.Database.ExecuteSqlCommand(
    @"CREATE VIEW View_BlogPostCounts AS 
        SELECT Name, Count(p.PostId) as PostCount from Blogs b
        JOIN Posts p on p.BlogId = b.BlogId
        GROUP BY b.Name");

#region Query Type
public class BlogPostsCount
{
    public string BlogName { get; set; }
    public int PostCount { get; set; }
}
#endregion 
```

Enter fullscreen mode Exit fullscreen mode

#### 4。急切装载

使用 Include 方法指定要包含在查询结果中的相关数据。在下面的示例中，结果中返回的博客将使用相关的帖子填充其 Posts 属性。

```
using (var context = new BloggingContext())
{
    var blogs = context.Blogs
        .Include(blog => blog.Posts)
        .ToList();
} 
```

Enter fullscreen mode Exit fullscreen mode

您可以在单个查询中包含来自多个关系的相关数据。

```
using (var context = new BloggingContext())
{
    var blogs = context.Blogs
        .Include(blog => blog.Posts)
        .Include(blog => blog.Owner)
        .ToList();
} 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用 include 方法钻取关系，以包含多级相关数据。以下示例加载所有博客、相关帖子以及每篇帖子的作者。

```
using (var context = new BloggingContext())

{

    var blogs = context.Blogs

        .Include(blog => blog.Posts)

            .ThenInclude(post => post.Author)

        .ToList();

} 
```

Enter fullscreen mode Exit fullscreen mode

### 
  
谢谢！