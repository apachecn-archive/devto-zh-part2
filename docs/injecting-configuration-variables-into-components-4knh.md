# 使用连接字符串的依赖注入

> 原文：<https://dev.to/justinjstark/injecting-configuration-variables-into-components-4knh>

依赖注入的一个问题是非类依赖，比如连接字符串不能被 IoC 容器自动解决。如果你使用 Dapper 或直 ADO.NET，你可能会遇到这种情况。幸运的是，有一种模式可以使用，这种模式很简单，适用于所有容器。

假设我们正在构建一个学生成绩簿，它连接到一个数据库来存储成绩。它还通过 HTTP 连接到学生注册系统。我们已经定义了几个组件。

```
public class GradeRepository : IGradeRepository
{
    private readonly string _gradebookConnectionString;
    private readonly ICacheService _cacheService;

    public GradeRepository(string gradebookConnectionString, ICacheService cacheService)
    {
        _gradebookConnectionString = gradebookConnectionString;
        _cacheService = cacheService;
    }
}

public class RegistrationRepository : IRegistrationRepository
{
    private readonly string _registrationApiBaseUrl;
    private readonly string _registrationClientId;
    private readonly ICacheService _cacheService;

    public RegistrationRepository(string registrationApiBaseUrl, string registrationClientId, ICacheService cacheService)
    {
        _registrationApiBaseUrl= registrationConnectionString;
        _registrationClientId = registrationClientId;
        _cacheService = cacheService;
    }
} 
```

我们使用构造函数注入将相关的配置和实用程序传递给每个组件。每个组件都有一个 ICacheService 来处理响应的缓存。

现在，让我们用一个 IoC 容器连接我们的两个存储库。在本演示中，我们将使用 Autofac，但这种模式适用于任何容器。

```
var builder = new ContainerBuilder();

builder.RegisterType<CacheService>().As<ICacheService>();

builder.Register(ctx => new GradeRepository
    (
        gradebookConnectionString: ConfigurationManager.ConnectionStrings["GradebookConnectionString"].ConnectionString,
        cacheService: ctx.Resolve<ICacheService>()
    ))
    .As<IGradeRepository>();

builder.Register(ctx => new RegistrationRepository
    (
        registrationApiBaseUrl: ConfigurationManager.AppSettings["RegistrationApiBaseUrl"],
        registrationClientId: ConfigurationManager.AppSettings["RegistrationClientId"],
        cacheService: ctx.Resolve<ICacheService>()
    ))
    .As<IRegistrationRepository>();

var container = builder.Build(); 
```

这个可以用，但是有点难看。随着我们添加越来越多带有字符串参数的组件，事情变得越来越糟糕。每个注册都需要一个工厂函数，我们可以在其中查找配置变量。

问题是 IoC 容器不知道如何解析字符串。有几种方法可以改善这种情况。首先，我们可以尝试使用一些奇特的容器功能来解析字符串参数，这些容器功能根据参数名称和类型注入一个值。但这很挑剔，支持因容器而异。

相反，让我们彻底摆脱字符串依赖。我们将把所有的配置转移到一个配置类中，然后把这个类传递给每个需要它的组件。

```
public class ConfigurationProvider : IConfigurationProvider
{
    private readonly string _gradebookConnectionString;
    private readonly string _registrationApiBaseUrl;
    private readonly string _registrationClientId;

    public ConfigurationProvider(string gradebookConnectionString, string registrationApiBaseUrl, string registrationClientId)
    {
        _gradebookConnectionString = gradebookConnectionString;
        _registrationApiBaseUrl= registrationApiBaseUrl;
        _registrationClientId = registrationClientId;
    }
} 
```

这非常简洁，为我们提供了一个存储应用级配置变量的单一位置。它也有望防止我们的同事和未来的代码维护人员试图以不同的、不一致的方式在应用程序的不同部分设置配置变量。

既然我们的配置类已经定义好了，让我们把它传递到组件中。

```
public class GradeRepository : IGradeRepository
{
    private readonly IConfigurationProvider _configurationProvider;
    private readonly ICacheService _cacheService;

    public GradeRepository(IConfigurationProvider configurationProvider, ICacheService cacheService)
    {
        _configurationProvider = configurationProvider;
        _cacheService = cacheService;
    }
}

public class RegistrationRepository : IRegistrationRepository
{
    private readonly IConfigurationProvider _configurationProvider;
    private readonly ICacheService _cacheService;

    public RegistrationRepository(IConfigurationProvider configurationProvider, ICacheService cacheService)
    {
        _configurationProvider = configurationProvider;
        _cacheService = cacheService;
    }
} 
```

请注意，我们的组件所依赖的数量已经减少。如果我们的组件需要 10 个不同的配置变量，那么它的签名是相同的。

现在让我们更新集装箱注册。我们不再需要为需要配置变量的组件指定工厂函数。

```
builder.Register(ctx => new ConfigurationProvider(
        gradebookConnectionString: ConfigurationManager.ConnectionStrings["GradebookConnectionString"].ConnectionString,
        registrationApiBaseUrl: ConfigurationManager.AppSettings["RegistrationApiBaseUrl"],
        registrationClientId: ConfigurationManager.AppSettings["RegistrationClientId"]
    ))
    .As<IConfigurationProvider>()
    .SingleInstance();

builder.RegisterType<CacheService>().As<ICacheService>();
builder.RegisterType<GradeRepository>().As<IGradeRepository>();
builder.RegisterType<RegistrationRepository>().As<IRegistrationRepository>(); 
```

就是这样。我们可以不断注册越来越多的组件，而不会有杂乱的工厂功能。此外，当我们的应用程序启动时，所有的应用程序配置都从配置文件中取出。

我已经尝试了很多不同的方法来将配置变量注入到我的组件中。这是最简单的方法，也是最容易用任何 IoC 容器进行配置的方法。