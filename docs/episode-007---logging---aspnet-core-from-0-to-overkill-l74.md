# 第 007 集-伐木业-ASP.NET 核心:从 0 到过度杀戮

> 原文：<https://dev.to/joaofbantunes/episode-007---logging---aspnet-core-from-0-to-overkill-l74>

在这一集里，我们来看看 ASP.NET 核心的登录，内置的支持和提供者，以及使用第三方实现，即 NLog。

对于演练，您可以查看下一个视频，但如果您喜欢快速阅读，请跳到书面综合。

[https://www.youtube.com/embed/jEx2PgUkLAE](https://www.youtube.com/embed/jEx2PgUkLAE)

整个系列的播放列表是[这里是](https://www.youtube.com/playlist?list=PLN0oN9Azm_MMAjk3nhRnmHdr1l0160Dhs)。
T3】

## [T1】简介](#intro)

日志记录是创建应用程序时的基础之一，因为它允许我们对它的行为有一个粗略的了解，不仅是在它可能遇到的问题方面，而且是关于正在发生的事情的一些信息。

到目前为止，在这个项目中，我们的“日志记录”基本上是`Console.WriteLine`，这确实不是一个好主意，因为我们希望对日志记录行为有更多的控制，比如日志级别和提供者。

因此，让我们在项目中添加一些真正的日志记录。

## 使用内置的日志记录设施

### 设置记录

非常类似于我们在前一集看到的关于配置的内容，我们需要做的第一件事是配置我们想要使用的日志提供者(或者正如我们将看到的，不是真的...又来了！😛).

就像我们对配置所做的那样，我们进入`Program`类，在那里我们创建`IWebHostBuilder`，我们可以调用`ConfigureLogging`方法来设置我们想要的提供者。

回到(现在众所周知的)`WebHost`的`CreateDefaultBuilder`方法，我们可以看到它已经默认配置了一些日志提供者。

```
//...
.ConfigureLogging((hostingContext, logging) =>
{
    logging.AddConfiguration(hostingContext.Configuration.GetSection("Logging"));
    logging.AddConsole();
    logging.AddDebug();
})
//... 
```

Enter fullscreen mode Exit fullscreen mode

(完整源代码在 [GitHub](https://github.com/aspnet/MetaPackages/blob/975011071b02f6937261c604fcde48d7e43030ce/src/Microsoft.AspNetCore/WebHost.cs#L181) 上，适用于 ASP.NET 核心 2.1)

通过查看代码，我们现在知道:

*   如果存在的话，将使用日志部分中的配置(我们稍后将对此进行研究)
*   控制台和调试提供程序是预先配置的

在这篇文章中，我们将主要坚持使用控制台(以及稍后的文件)，所以默认的构建器配置已经准备好了。

现在让我们替换代码中的`Console.WriteLine`调用。

### 注入 ILogger

为了记录东西，我们必须得到一个`ILogger<T>` -或者使用一个`ILoggerFactory<T>`，但是我们现在将坚持使用`ILogger<T>`-在我们想要记录的类中。因此，例如，如果我们想在`GroupsController`中添加日志，我们可以添加一个`ILogger<GroupsController>`，并将其存储在一个私有字段中，以便在操作中使用。现在，我们已经在`GroupsServiceDecorator`中向控制台写入了一些内容，这些内容是在`AutofacModule`中创建的，所以让我们来看看。

`AutofacModule.cs`

```
public class AutofacModule : Module
{
    protected override void Load(ContainerBuilder builder)
    {
        builder.RegisterType<InMemoryGroupsService>().Named<IGroupsService>("groupsService").SingleInstance();
        builder.RegisterDecorator<IGroupsService>((context, service) => new GroupsServiceDecorator(service, context.Resolve<ILogger<GroupsServiceDecorator>>()), "groupsService");
    }

    private class GroupsServiceDecorator : IGroupsService
    {
        private readonly IGroupsService _inner;
        private readonly ILogger<GroupsServiceDecorator> _logger;

        public GroupsServiceDecorator(IGroupsService inner, ILogger<GroupsServiceDecorator> logger)
        {
            _inner = inner;
            _logger = logger;
        }

        public IReadOnlyCollection<Group> GetAll()
        {
            _logger.LogWarning($"######### Helloooooo from {nameof(GetAll)} #########", );
            return _inner.GetAll();            
        }

        public Group GetById(long id)
        {
            _logger.LogWarning($"######### Helloooooo from {nameof(GetById)} #########");
            return _inner.GetById(id);
        }

        public Group Update(Group group)
        {
            _logger.LogWarning($"######### Helloooooo from {nameof(Update)} #########");
            return _inner.Update(group);
        }

        public Group Add(Group group)
        {
            _logger.LogWarning($"######### Helloooooo from {nameof(Add)} #########");
            return _inner.Add(group);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

相当简单的东西。在`GroupsServiceDecorator`中，我们得到一个`ILogger<GroupsServiceDecorator>`，然后在方法中使用它。只需要在上面的`builder.RegisterDecorator`中添加依赖解析来传递日志记录器。

如果我们现在运行这个应用程序，我们得到的几乎和以前一样，但是增加了一些来自使用日志记录器的额外信息:

```
warn: CodingMilitia.PlayBall.GroupManagement.Web.IoC.AutofacModule.GroupsServiceDecorator[0]
      ######### Helloooooo from GetById ######### 
```

Enter fullscreen mode Exit fullscreen mode

### 结构化日志记录

虽然我们现在不会从中获得太多的好处，但我们可以做的一件事是改进日志的输出，使其对结构化日志记录友好。

结构化日志记录(或语义日志记录)意味着除了日志消息之外，每个日志还附加了一些元数据，这可能有助于充分利用日志，而不需要大量的正则表达式或类似的巫术来提取所需的信息。让我们以前面的`GroupsServiceDecorator`为例，如果我们不是像现在这样记录消息，而是使用一个提供者为每个日志存储一个 JSON，除了消息之外，它还存储修饰方法的名称及其语义，比如:

```
{  "message":  "######### Helloooooo from GetAll #########",  "data":  {  "decoratedMethod":  "GetAll"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

这将使通过修饰的方法名查找变得更容易，例如，计算它被调用的次数以及类似的事情。

为了实现这一点，我们用记录器方法提供的格式化功能替换了以前使用的字符串插值:

`AutofacModule.cs`

```
//...
private class GroupsServiceDecorator : IGroupsService
{
    //...

    public IReadOnlyCollection<Group> GetAll()
    {
        _logger.LogWarning("######### Helloooooo from {decoratedMethod} #########", nameof(GetAll));
        return _inner.GetAll();            
    }

    public Group GetById(long id)
    {
        _logger.LogWarning("######### Helloooooo from {decoratedMethod} #########", nameof(GetById));
        return _inner.GetById(id);
    }

    public Group Update(Group group)
    {
        _logger.LogWarning("######### Helloooooo from {decoratedMethod} #########", nameof(Update));
        return _inner.Update(group);
    }

    public Group Add(Group group)
    {
        _logger.LogWarning("######### Helloooooo from {decoratedMethod} #########", nameof(Add));
        return _inner.Add(group);
    }
}
//... 
```

Enter fullscreen mode Exit fullscreen mode

现在，当将来我们配置记录器输出到支持它的地方时，我们将有一些更好的结构化数据来处理。

### 改变日志级别

到目前为止，我们正在创建的日志都是警告，但是对于这种日志，它们的级别可能应该是`Trace`或类似的东西。我还没有使用它，因为默认的日志级别是`Information`，所以`Trace`不会显示。让我们改变这一点。

在`appsettings.development.json`中，我们添加一个日志部分，在这里我们可以配置

```
{  "_commentWannabe_":  "...",  "Logging":  {  "LogLevel":  {  "Default":  "Debug",  "System":  "Information",  "Microsoft":  "Warning",  "CodingMilitia.PlayBall.GroupManagement.Web.IoC":  "Trace"  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

我们可以立即看到，我们不仅定义了一个日志级别，还可以按名称空间定义它。因此，默认的日志级别将是`Debug`，但是以`System`开头的名称空间将只记录`Information`，以`Microsoft`开头的名称空间只允许`Warning`或更高的级别，最后，对于名称空间`CodingMilitia.PlayBall.GroupManagement.Web.IoC`(装饰器所在的位置)，所有内容都将被记录，最低级别为`Trace`。

现在我们可以用`LogTrace`替换我们的`LogWarning`，并看到它仍然显示在控制台上。

```
trce: CodingMilitia.PlayBall.GroupManagement.Web.IoC.AutofacModule.GroupsServiceDecorator[0]
      ######### Helloooooo from GetById ######### 
```

Enter fullscreen mode Exit fullscreen mode

### 记录范围

我们在日志记录时可以使用的另一个有趣的特性是作用域。作用域允许我们对一组日志调用进行分组，我们可以将一些数据附加到其中，这些数据总是日志的一部分，而不需要在每次日志调用中添加这些信息。

为了让我们看到作用域，我们需要在控制台提供者的配置中启用 then:
`appsettings.development.json`

```
{  "_commentWannabe_":  "...",  "Logging":  {  "LogLevel":  {  "Default":  "Debug",  "System":  "Information",  "Microsoft":  "Warning",  "CodingMilitia.PlayBall.GroupManagement.Web.IoC":  "Trace"  },  "Console":  {  "IncludeScopes":  true  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

通过这样做，我们可以在查看控制台日志时看到不同之处，因为 ASP.NET 核心使用了这个功能:

```
trce: CodingMilitia.PlayBall.GroupManagement.Web.IoC.AutofacModule.GroupsServiceDecorator[0]
      => ConnectionId:0HLIF66I6VVM5 => RequestId:0HLIF66I6VVM5:00000001 RequestPath:/groups/1 => CodingMilitia.PlayBall.GroupManagement.Web.Controllers.GroupsController.Details (CodingMilitia.PlayBall.GroupManagement.Web)
      ######### Helloooooo from GetById ######### 
```

Enter fullscreen mode Exit fullscreen mode

与以前的日志相比，我们可以看到额外的几行，其中包含关于请求和处理请求的控制器的信息。

现在让我们试着添加一个我们自己的范围。

`AutofacModule.cs`

```
public class AutofacModule : Module
{
    //...

    private class GroupsServiceDecorator : IGroupsService
    {
        //...

        public IReadOnlyCollection<Group> GetAll()
        {
            using (var scope = _logger.BeginScope("Decorator scope: {decorator}", nameof(GroupsServiceDecorator)))
            {
                _logger.LogTrace("######### Helloooooo from {decoratedMethod} #########", nameof(GetAll));
                var result = _inner.GetAll();
                _logger.LogTrace("######### Goodbyeeeee from {decoratedMethod} #########", nameof(GetAll));
                return result;
            }
        }

        //...
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

以`GetAll`方法为例，我们在`using`语句中使用`_logger.BeginScope`创建作用域，确保只有在其中创建的日志才能使用该作用域，包括不直接在`using`中，而是在那里调用的代码中的日志。

该范围可能有一条消息以及与之相关联的额外信息，这将伴随其上下文中的每个日志。

还向该方法添加了另一个`_logger.LogTrace`，因此我们可以看到两者都带走了作用域的信息。控制台输出结果如下。

```
trce: CodingMilitia.PlayBall.GroupManagement.Web.IoC.AutofacModule.GroupsServiceDecorator[0]
      => ConnectionId:0HLIF66I6VVM6 => RequestId:0HLIF66I6VVM6:00000001 RequestPath:/groups => CodingMilitia.PlayBall.GroupManagement.Web.Controllers.GroupsController.Index (CodingMilitia.PlayBall.GroupManagement.Web) => Decorator scope: GroupsServiceDecorator
      ######### Helloooooo from GetAll #########
trce: CodingMilitia.PlayBall.GroupManagement.Web.IoC.AutofacModule.GroupsServiceDecorator[0]
      => ConnectionId:0HLIF66I6VVM6 => RequestId:0HLIF66I6VVM6:00000001 RequestPath:/groups => CodingMilitia.PlayBall.GroupManagement.Web.Controllers.GroupsController.Index (CodingMilitia.PlayBall.GroupManagement.Web) => Decorator scope: GroupsServiceDecorator
      ######### Goodbyeeeee from GetAll ######### 
```

Enter fullscreen mode Exit fullscreen mode

## 使用第三方记录器

正如依赖注入的情况一样，我们可以使用第三方容器，我们也可以使用第三方日志库，它们可能具有一些我们无法开箱即用的特性。

为了了解如何使用第三方日志程序，我们将把 [NLog](https://nlog-project.org/) 添加到我们的项目中，并替换内置的日志提供程序。

### 移除内置提供者并添加 NLog

为了获得我们项目的 NLog 依赖性，`dotnet add package NLog.Web.AspNetCore`完成了这个任务。

为了用 NLog 替换默认的日志记录器，我们使用了`Program`类，并对`CreateWebHostBuilder`方法做了一些修改。

`Program.cs`

```
public class Program
{
    //...

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureLogging(builder =>
            {
                builder.ClearProviders(); //clear default providers, NLog will handle it
                builder.SetMinimumLevel(Microsoft.Extensions.Logging.LogLevel.Trace); //set minimum level to trace, NLog rules will kick in afterwards
            })
            .UseNLog()
            .UseStartup<Startup>();
    //...
} 
```

Enter fullscreen mode Exit fullscreen mode

正如我们在上面看到的，我们使用`ConfigureLogging`来清除添加的提供者`WebHost.CreateDefaultBuilder`——我们可以让它们和 NLog 一起使用，但是这可能比让 NLog 处理所有事情更令人困惑。我们还将最低日志级别设置为 trace，因此所有内容都将传递给 NLog，因此它的目标(我们稍后将看到它们是什么)将只记录应该记录的内容。

此外，我们必须调用`WebHostBuilder`上的`UseNLog`来设置 NLog。

### 配置 NLog

现在来配置 NLog！理想情况下，我们会创建一个`nlog.config`文件，但是我现在只打算以编程方式配置一切。关于配置一切的推荐方法，请查看 [NLog 的文档](https://github.com/NLog/NLog.Web/wiki/Getting-started-with-ASP.NET-Core-2)。

为此，我向在应用程序开始时调用的`Program`类添加了一个`ConfigureNLog`方法。在这种方法中，我们设置 NLog 的目标，它们类似于 ASP.NET 核心日志记录提供程序，因为我们可以设置控制台和文件、每个目标的特定日志级别以及每个目标的特定日志布局。

`Program.cs`

```
public class Program
{
    public static void Main(string[] args)
    {
        Console.WriteLine("############### Starting application ###############");
        ConfigureNLog();
        CreateWebHostBuilder(args).Build().Run();
    }

    //...

    //TODO: replace with nlog.config
    private static void ConfigureNLog()
    {
        var config = new LoggingConfiguration();

        var consoleTarget = new ColoredConsoleTarget("coloredConsole")
        {
            Layout = @"${date:format=HH\:mm\:ss} ${level} ${message} ${exception}"
        };
        config.AddTarget(consoleTarget);

        var fileTarget = new FileTarget("file")
        {
            FileName = "${basedir}/file.log",
            Layout = @"${date:format=HH\:mm\:ss} ${level} ${message} ${exception} ${ndlc}"
        };
        config.AddTarget(fileTarget);

        config.AddRule(LogLevel.Trace, LogLevel.Fatal, consoleTarget, "CodingMilitia.PlayBall.GroupManagement.Web.IoC.*");
        config.AddRule(LogLevel.Info, LogLevel.Fatal, consoleTarget);
        config.AddRule(LogLevel.Warn, LogLevel.Fatal, fileTarget);

        LogManager.Configuration = config;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我想说，只要看看上面的代码，你就可以很容易地理解发生了什么。创建了两个目标，控制台和文件，每个目标都有不同的消息布局和日志级别规则，包括(就像我们前面看到的内置提供者一样)使日志级别也依赖于记录日志的类的名称空间。

关于布局，除了`ndlc`之外，大多数选项都很容易理解。`ndlc`(意思是“嵌套诊断逻辑上下文”)是包含我们之前在消息中谈到的范围的方法。

关于`FileTarget`的一个说明——我真的没有尽我所能的配置它。例如，定义何时应该创建一个新的日志文件(每一天，当它达到一定大小时，当应用程序重新启动时，等等)或者何时删除旧的日志文件可能是一个好主意。

有了这个，我们就可以像以前一样工作了，但是让 NLog 负责日志。使用记录器的代码不需要任何修改。唯一值得注意的区别是我们看到的消息数量，因为我没有以完全相同的方式配置日志级别，消息的布局也有点不同(并且有颜色，因为我们使用了`ColoredConsoleTarget`)。

控制台日志样本

```
18:38:57 Warn ######### Helloooooo from GetById ######### 
```

Enter fullscreen mode Exit fullscreen mode

文件日志样本

```
18:38:57 Warn ######### Helloooooo from GetById #########  ConnectionId:0HLIFS2291JCR RequestId:0HLIFS2291JCR:00000003 RequestPath:/groups/1 CodingMilitia.PlayBall.GroupManagement.Web.Controllers.GroupsController.Details (CodingMilitia.PlayBall.GroupManagement.Web) 
```

Enter fullscreen mode Exit fullscreen mode

## 其他

《ASP.NET 核心:从 0 到过度杀戮》系列的另一集到此结束。像往常一样，这并不是关于这个主题的所有知识，在这个例子中是日志，但是它是我们过度工程化道路上的另一个垫脚石😆

这个帖子的源码是[这里](https://github.com/AspNetCoreFromZeroToOverkill/GroupManagement/tree/episode007)。

请发送任何反馈，以便我可以改进和调整下一集。

谢谢你的来访，西阿兹！