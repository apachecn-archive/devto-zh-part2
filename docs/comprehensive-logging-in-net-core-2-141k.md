# 全面登录。网络核心 2

> 原文：<https://dev.to/nordyj/comprehensive-logging-in-net-core-2-141k>

日志记录是对应用程序的可维护性和故障排除的简易性至关重要的项目之一。这也往往是事后的想法，作为“如果你有时间的话”你会做的事情。好在，在。NET Core，有几个技巧可以用来大大简化这项工作:动作过滤器和异常过滤器。

这篇博客中提到的代码是针对。在下面的例子中，ILoggerService 和 IHostingService 是我用来包装现有的。使用扩展方法的. NET 核心接口。因为扩展方法不能被 mocking 框架包装，所以您需要围绕使用它们的接口创建一个包装器实现。我是这里的 [Moq](https://github.com/moq/moq4) 库的忠实粉丝，这就是下面的示例中使用的库。

## 记录 HTTP 请求

我们要做的第一件事是创建一个动作过滤器。这是一个实现 IActionFilter 接口的类。该接口定义了两个方法:OnActionExecuting，每当要执行控制器操作时调用，以及 OnActionExecuted，在操作执行后调用。这很有用，因为它允许我们创建一个通用的过滤器来响应所有路由的 HTTP 请求，并在运行控制器操作之前和之后对该请求做一些事情，包括日志记录。因此，一个记录所有路由的 HTTP 请求的通用过滤器可能看起来像这样:

```
public class ActionLoggingFilter : IActionFilter
{
    private readonly ILoggerService _logger;

    public ActionLoggingFilter(ILoggerService loggerService)
    {
        _logger = loggerService;
    }

    public void OnActionExecuting(ActionExecutingContext context)
    {
        var method = context.HttpContext.Request.Method;
        var controller = context.RouteData.Values["controller"];
        var action = context.RouteData.Values["action"];
        var message = $"Starting execution of {method} request on {controller}.{action}.";

        _logger.LogDebug(message);
    }

    public void OnActionExecuted(ActionExecutedContext context)
    {
        var method = context.HttpContext.Request.Method;
        var controller = context.RouteData.Values["controller"];
        var action = context.RouteData.Values["action"];
        var message = $"Completed execution of {method} request on {controller}.{action}.";

        _logger.LogDebug(message);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

ILoggerService 是由注入的。NET Core 的依赖注入框架，如果应用程序的日志记录级别设置为 Debug，则在这个上下文中使用它来写出关于请求的日志。这意味着，在正常操作下，不会生成日志。但是，如果我们需要排除故障，我们只需要更新应用程序配置，以便将日志记录级别设置为 Debug。现在，每个路由请求的前后都有一个日志条目，这使得我们可以更容易地跟踪请求的流程。

## 记录异常

接下来，如果我们想要一个全局异常处理程序，我们需要编写一个实现 IExceptionFilter 接口的类。此类公开一个名为 OnException 的方法，每当出现未处理的异常时都会调用该方法。举个例子:

```
public class ApplicationExceptionFilter : IExceptionFilter
{
    private readonly IHostingService _hostingService;
    private readonly IModelMetadataProvider _modelMetadataProvider;
    private readonly ILoggerService _logger;

    public ApplicationExceptionFilter(IHostingService hostingService, IModelMetadataProvider modelMetadataProvider, ILoggerService loggerService)
    {
        _hostingService = hostingService;
        _modelMetadataProvider = modelMetadataProvider;
        _logger = loggerService;
    }

    public void OnException(ExceptionContext context)
    {
        var method = context.HttpContext.Request.Method;
        var controller = context.RouteData.Values["controller"];
        var action = context.RouteData.Values["action"];
        var message = $"An exception was encountered during the execution of a {method} request on {controller}.{action}.";

        _logger.LogError(context.Exception, message);

        if (!_hostingService.IsDevelopment())
            return;

        var result = new ViewResult { ViewName = "CustomError" };
        result.ViewData = new ViewDataDictionary(_modelMetadataProvider, context.ModelState)
        {
            { "Exception", context.Exception }
        };
        context.Result = result;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

同样，依赖注入框架将在这里提供 3 个定义的依赖:IHostingService、IModelMetadataProvider 和 ILoggerService。OnException 方法使用 LogError 写入错误日志项。然后，它会查看自己是否运行在开发模式下。如果没有，它只是返回，异常继续冒泡，产生适当的 HTTP 响应代码。然而，如果我们处于开发模式，上下文。结果值提供了可在浏览器中呈现的有关错误的详细信息。

## 布线完毕

一旦你创建了这两个类，你需要告诉。NET 核心来使用它们。。NET Core 依赖于一个名为 Startup.cs 的文件，该文件允许您定义应用程序将需要的服务，以及在处理 HTTP 请求时将使用的处理程序管道。为了将过滤器添加到管道中，您需要调整启动。ConfigureServices 方法:

```
public void ConfigureServices(IServiceCollection services)
{
    services.AddOptions();

    services.AddMvc(options =>
    {
        options.Filters.Add<ActionLoggingFilter>();
        options.Filters.Add<ApplicationExceptionFilter>();
    });

    // Any other services...
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，当您的应用程序运行时，所有控制器操作都将被记录(如果您的配置设置为调试日志记录级别)，并且所有未处理的异常都将被记录，所有这些都无需您向控制器操作添加任何附加内容。如果有意义的话，您仍然可以向控制器操作添加额外的上下文日志记录。

## 单元测试

ActionLoggingFilter 的单元测试非常简单。传递给 IActionFilter 方法的 ActionContext 值需要用路由数据模拟，如下所示:

```
public class ActionLoggingFilterTests
{
    private readonly Mock<ILoggerService> _loggerService;
    private readonly ActionContext _actionContext;

    public ActionLoggingFilterTests()
    {
        _loggerService = new Mock<ILoggerService>();

        var httpContext = new DefaultHttpContext();
        httpContext.Request.Method = "GET";
        var routeData = new RouteData();
        routeData.Values.Add("controller", "Controller");
        routeData.Values.Add("action", "Action");
        _actionContext = new ActionContext(httpContext, routeData, new ActionDescriptor());
    }

    [Fact]
    public void OnActionExecutingLogsDebugMessage()
    {
        var actionExecutingContext = new ActionExecutingContext(
            _actionContext,
            new List<IFilterMetadata>(),
            new Dictionary<string, object>(),
            null);
        var message = "Starting execution of GET request on Controller.Action.";
        _loggerService.Setup(x => x.LogDebug(message));
        var filter = new ActionLoggingFilter(_loggerService.Object);

        filter.OnActionExecuting(actionExecutingContext);

        _loggerService.Verify(x => x.LogDebug(message), Times.Once);
    }

    [Fact]
    public void OnActionExecutedLogsDebugMessage()
    {
        var actionExecutedContext = new ActionExecutedContext(
            _actionContext,
            new List<IFilterMetadata>(),
            null);

        var message = "Completed execution of GET request on Controller.Action.";
        _loggerService.Setup(x => x.LogDebug(message));
        var filter = new ActionLoggingFilter(_loggerService.Object);

        filter.OnActionExecuted(actionExecutedContext);

        _loggerService.Verify(x => x.LogDebug(message), Times.Once);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

每个测试都创建适当的上下文，然后验证是否调用了 LogDebug 方法，以及调用该方法时是否使用了正确的消息。

ApplicationExceptionFilter 的单元测试稍微复杂一些:

```
public class ApplicationExceptionFilterTests
{
    private readonly Mock<IHostingService> _hostingService;
    private readonly Mock<IModelMetadataProvider> _modelMetadataProvider;
    private readonly Mock<ILoggerService> _loggerService;
    private readonly ActionContext _actionContext;

    public ApplicationExceptionFilterTests()
    {
        _hostingService = new Mock<IHostingService>();
        _modelMetadataProvider = new Mock<IModelMetadataProvider>();
        _loggerService = new Mock<ILoggerService>();

        var httpContext = new DefaultHttpContext();
        httpContext.Request.Method = "GET";
        var routeData = new RouteData();
        routeData.Values.Add("controller", "Controller");
        routeData.Values.Add("action", "Action");
        _actionContext = new ActionContext(httpContext, routeData, new ActionDescriptor());
    }

    [Fact]
    public void OnExceptionLogsErrorMessage()
    {
        var exception = new ApplicationException("Test");
        var exceptionContext = new ExceptionContext(_actionContext, new List<IFilterMetadata>())
        {
            Exception = exception
        };
        var message = "An exception was encountered during the execution of a GET request on Controller.Action.";
        _loggerService.Setup(x => x.LogError(exception, message));
        var filter = new ApplicationExceptionFilter(_hostingService.Object, _modelMetadataProvider.Object, _loggerService.Object);

        filter.OnException(exceptionContext);

        _loggerService.Verify(x => x.LogError(exception, message), Times.Once);
    }

    [Fact]
    public void OnExceptionReturnsViewResultWhenInDevelopmentMode()
    {
        _hostingService.Setup(x => x.IsDevelopment()).Returns(true);
        var identity = ModelMetadataIdentity.ForType(typeof(object));
        var metadata = new Mock<ModelMetadata>(identity) { CallBase = true };
        _modelMetadataProvider.Setup(x => x.GetMetadataForType(typeof(object))).Returns(metadata.Object);

        var exception = new ApplicationException("Test");
        var exceptionContext = new ExceptionContext(_actionContext, new List<IFilterMetadata>())
        {
            Exception = exception
        };
        var filter = new ApplicationExceptionFilter(_hostingService.Object, _modelMetadataProvider.Object, _loggerService.Object);

        filter.OnException(exceptionContext);

        var result = exceptionContext.Result as ViewResult;

        Assert.NotNull(result);
        Assert.Equal("CustomError", result.ViewName);
        Assert.NotNull(result.ViewData);
        Assert.True(result.ViewData.Keys.Contains("Exception"));
        Assert.Equal(exception, result.ViewData["Exception"]);
    }

    [Fact]
    public void OnExceptionDoesNotModifyResultWhenNotInDevelopmentMode()
    {
        _hostingService.Setup(x => x.IsDevelopment()).Returns(false);
        var exception = new ApplicationException("Test");
        var exceptionContext = new ExceptionContext(_actionContext, new List<IFilterMetadata>())
        {
            Exception = exception
        };
        var filter = new ApplicationExceptionFilter(_hostingService.Object, _modelMetadataProvider.Object, _loggerService.Object);

        filter.OnException(exceptionContext);

        var result = exceptionContext.Result;

        Assert.Null(result);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

同样，我必须模拟 ActionContext 来提供路由数据。我还必须模拟 IModelMetadataProvider 接口，该接口用于定义 IExceptionFilter 接口预期使用的异常值。

演示这些技术的示例应用程序可以在这里找到:[https://github.com/StaticSphere/LoggingSample](https://github.com/StaticSphere/LoggingSample)