# 具有 DbUp 的可复查存储过程和视图

> 原文：<https://dev.to/davidwengier/reviewable-stored-procedures-and-views-with-dbup-295k>

我们在工作中使用 [DbUp](https://dbup.github.io/) 来管理数据库变更和迁移，在大多数情况下，只要您有一个已知的模式，它就能很好地工作。当前实现的缺点是对存储过程定义的更改不容易在源代码控制中被审查。幸运的是，使用 DbUp 启用这个工作流相对简单。

## 项目格式

我们的 DbUp 项目看起来相当标准:

[![DbUp Project](../Images/d64f7e5185627452a9099f94d632da3e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HhvfvxyJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uxtz7n0o27vpbipnr2gr.png)

DbUp 负责运行脚本，并通过其内置日志系统确保没有脚本运行超过一次，日志记录也存储在数据库中。问题是，这些“Alter Procedure”脚本中只包含存储过程的完整副本，即使这六个文件都在更改同一个存储过程。

启用可复查的存储过程和视图的第一步是为将要取消记录的脚本创建一个新文件夹，这样每当 DbUp 运行时，它们总是会运行。我现在就把它叫做 StoredProcs，因为这是我要移动的第一个对象。

基本思想是，您将该文件夹用于包含简单拖放 SQL 脚本，并为每个存储过程创建脚本。DbUp 每次都运行这些脚本，本质上是确保数据库定义总是正确的，并允许开发人员对源存储库中的现有脚本进行更改，而不是像普通迁移脚本那样必须一直创建新的脚本。

[![DROP and CREATE Script](../Images/2b455a5443e51a5dfcb873e47614f337.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZxSdaXDs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/44izwnvjh4yk0chszznz.png)

## DbUp 脚本运行程序

现有的 DbUp 脚本运行器看起来相当基础，就像这样:

```
var upgrader = DeployChanges.To
               .SqlDatabase(connectionString)
               .WithScriptsEmbeddedInAssembly(Assembly.GetExecutingAssembly())
               .LogToConsole()
               .JournalToSqlTable("dbo", "SchemaVersions")
               .WithTransaction()
               .Build(); 
```

Enter fullscreen mode Exit fullscreen mode

我们需要向该脚本添加一个新的升级程序，我们将使用内置于 DbUp
中的`NullJournal`而不是将日志存储在表中

```
var storedProcUpgrader = DeployChanges.To
               .SqlDatabase(connectionString)
               .WithScriptsEmbeddedInAssembly(Assembly.GetExecutingAssembly())
               .LogToConsole()
               .JournalTo(new NullJournal())
               .WithTransaction()
               .Build(); 
```

Enter fullscreen mode Exit fullscreen mode

最后一个难题是在每个升级程序上安装一个过滤器，这样每个升级程序只加载我们想要的脚本。最终的代码是这样的:

```
public static int Main()
{
    var connectionString = ConfigurationManager.ConnectionStrings["ConnectionString"].ConnectionString;

    var upgrader = DeployChanges.To
        .SqlDatabase(connectionString)
        .WithScriptsEmbeddedInAssembly(Assembly.GetExecutingAssembly(), s => !IsStoredProc(s))
        .LogToConsole()
        .JournalToSqlTable("dbo", "SchemaVersions")
        .WithTransaction()
        .Build();

    var storedProcUpgrader = DeployChanges.To
        .SqlDatabase(connectionString)
        .WithScriptsEmbeddedInAssembly(Assembly.GetExecutingAssembly(), s => IsStoredProc(s))
        .LogToConsole()
        .JournalTo(new NullJournal())
        .WithTransaction()
        .Build();

    // migrate the database data, and table schema changes first
    if (!UpgradeAndLog(upgrader))
    {
        return 1;
    }
    // now we can change stored procs that rely on the adjusted schema
    if (!UpgradeAndLog(storedProcUpgrader))
    {
        return 1;
    }

    Console.ForegroundColor = ConsoleColor.Green;
    Console.WriteLine("Success!");
    Console.ResetColor();
    return 0;
}

private static bool UpgradeAndLog(DbUp.Engine.UpgradeEngine upgrader)
{
    var result = upgrader.PerformUpgrade();
    if (!result.Successful)
    {
        Console.ForegroundColor = ConsoleColor.Red;
        Console.WriteLine(result.Error);
        Console.ResetColor();
        return false;
    }
    return true;
}

private bool IsStoredProc(string scriptName)
{
    return (scriptName.StartsWith("My.NameSpace.StoredProcs.", StringComparison.OrdinalIgnoreCase));
} 
```

Enter fullscreen mode Exit fullscreen mode

## 得到审核

对视图定义脚本的存储过程的每一个更改都只是一个更改，所以无论您使用什么源存储库比较过程，都将只显示已经完成的内容。此外，您的源存储库中始终有您的脚本的当前最新定义，因此您离不必担心数据库有一个已知的良好起点又近了一步，至少从模式的角度来看是这样。

到目前为止，我们是在一个变更一个变更的基础上推出的，但没有理由数据库的所有相关部分不能编写脚本来播种这项工作，给你一个已知的基线。

同样的理论也适用于视图或函数，或者迁移脚本需要包含整个定义的任何东西，并且删除对象不是破坏性的操作。