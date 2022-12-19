# 从单元测试转移到集成/系统测试

> 原文：<https://dev.to/rembou1/moving-from-unit-test-to-integrationsystem-test--47ja>

# 从单元测试转向集成/系统测试

*这篇文章不是直接关于 Blazor 的，但是我想在博客上写一下这个技术上的变化。*

在我的 Toss 项目中，我决定从单元测试方法转移到集成测试方法。在这篇博文中，我将试着解释原因和使用的技术。

## 我如何构建我的单元测试

我是这样构建我的单元测试的:一个方法(单元)有输入(参数和依赖)和输出(返回、依赖和异常)。

单元测试将“安排”输入:

*   参数是强制的
*   依赖关系是通过一个模仿框架(Moq 中的 setup()设置的),并“断言”输出:
*   检查预期回报和例外情况
*   通过 mocking 框架(Moq 中的 Verify()来验证依赖关系

理论上这是完美的:

*   测试断言时只执行生产代码，我不依赖于其他系统。
*   如果一个测试失败了，原因就很容易找到了。
*   每段代码都将按照红/绿流程编写(创建测试并添加代码，直到测试通过)。

## 单元测试的问题

这种方法有一些缺点，导致我放弃了它:

*   由于依赖关系的强制模仿/伪造，您不是在测试一个接口，而是在测试一个实现。方法是实现的底层模块。每当我在我的实现中做一个小的改变时(例如，我在“SaveOne”上改变一个循环来调用“SaveAll”)，我将需要改变我的测试。
*   设置一切需要大量代码，看看这个文件，我必须创建它来模拟 ASPNET 核心身份依赖。如果我有一个简单的公式“1 LoC = X bugs”，我们可以说我将花费比实际代码更多的时间来调试我的测试(这就是所发生的情况)!
*   因为你没有测试所有的东西，所以你在运行时会有问题:你没有以正确的方式设置 DI，因为你的类没有实现好的接口，你的配置没有被设置...

## 技术方案

解决方案是系统测试，我们可以称之为集成测试，但对于其中一些测试来说，可能不涉及依赖。在这里，我想对我的系统进行整体测试。

### 使用 ASPNET 核心 DI 设置

这是我的类设置的:

```
public class TestFixture
{
    public const string DataBaseName = "Tests";
    public const string UserName = "username";
    private static ServiceProvider _provider;
    //only mock we need :)
    private static Mock<IHttpContextAccessor> _httpContextAccessor;

    public static ClaimsPrincipal ClaimPrincipal { get; set; }

    static TestFixture()
    {

        var dict = new Dictionary<string, string>
        {
             { "GoogleClientId", ""},
             { "GoogleClientSecret", ""},
             { "MailJetApiKey", ""},
             { "MailJetApiSecret", ""},
             { "MailJetSender", ""},
             { "CosmosDBEndpoint", "https://localhost:8081"},
             { "CosmosDBKey", "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw=="},
             { "StripeSecretKey", ""},
             {"test","true" },
             {"dataBaseName",DataBaseName }
        };

        var config = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();
        var startup = new Startup(config);
        var services = new ServiceCollection();
        startup.ConfigureServices(services);
        _httpContextAccessor = new Mock<IHttpContextAccessor>();

        services.AddSingleton(_httpContextAccessor.Object);
        services.AddScoped(typeof(ILoggerFactory), typeof(LoggerFactory));
        services.AddScoped(typeof(ILogger<>), typeof(Logger<>));

        _provider = services.BuildServiceProvider();

    }

    public async static Task CreateTestUser()
    {
        var userManager =  _provider.GetService<UserManager<ApplicationUser>>();
        ApplicationUser user = new ApplicationUser()
        {
            UserName = UserName,
            Email = "test@yopmail.com",
            EmailConfirmed = true
        };
        await userManager.CreateAsync(user);
        ClaimPrincipal = new ClaimsPrincipal(
                  new ClaimsIdentity(new Claim[]
                     {
                                new Claim(ClaimTypes.Name, UserName)
                     },
                  "Basic"));
        (ClaimPrincipal.Identity as ClaimsIdentity).AddClaim(new Claim(ClaimTypes.NameIdentifier, user.Id));
        _httpContextAccessor
          .SetupGet(h => h.HttpContext)
          .Returns(() =>
          new DefaultHttpContext()
          {
              User = ClaimPrincipal

          });
    }

    public static void SetControllerContext(Controller controller)
    {
        controller.ControllerContext = new ControllerContext
        {
            HttpContext = _httpContextAccessor.Object.HttpContext
        };
    }

    public static void SetControllerContext(ControllerBase controller)
    {
        controller.ControllerContext = new ControllerContext
        {
            HttpContext  = _httpContextAccessor.Object.HttpContext
        };
    }

    public static T GetInstance<T>()
    {
        T result = _provider.GetRequiredService<T>();
        ControllerBase controllerBase = result as ControllerBase;
        if (controllerBase != null)
        {
            SetControllerContext(controllerBase);
        }
        Controller controller = result as Controller;
        if (controller != null)
        {
            SetControllerContext(controller);
        }
        return result;

    }
} 
```

Enter fullscreen mode Exit fullscreen mode

*   我必须模拟 HttpContextAccessor，因为没有 Http 查询，我需要它来知道谁是连接的用户
*   我将“test”“true”传递给 config，这样我就可以在 Configure()中设置我的 fake/mock
*   我不得不强制记录器 DI 设置，我猜它是由 ConfigureService 中的某个东西设置的

### 选择被测层

我选择在中介层测试(来自 mediator)，我的控制器层非常薄，所以我更喜欢在这里测试我的应用程序。

我的测试设置非常简单，基本上是这样的:

```
 var mediator = TestFixture.GetInstance<IMediator>();

 mediator.Send(new MyCommand());

 var res = mediator.Send(new MyQuery());

 Assert.Single(res); 
```

Enter fullscreen mode Exit fullscreen mode

*   这个测试将使用命令和查询，所以用这个 4 LoC 我测试了很多代码:DI 设置，接口声明，接口实现...

### 外部依赖

我仍然需要模拟一些我没有管理的依赖项:Stripe、MailJet 甚至 Random。下面是我如何设置假的

```
// Add application services.
if (Configuration.GetValue<string>("test") == null)
{
    services.AddTransient<IEmailSender, EmailSender>();
    services.AddSingleton<IStripeClient, StripeClient>(s => new StripeClient(Configuration.GetValue<string>("StripeSecretKey")));
}
else
{
    //We had it as singleton so we can get the content later during the asset phase
    services.AddSingleton<IEmailSender, FakeEmailSender>();
    services.AddSingleton<IStripeClient, FakeStripeClient>();
} 
```

Enter fullscreen mode Exit fullscreen mode

*   伪代码非常简单(你可以在我的 Toss repo 中找到它),它们只是记录收到的参数，并有一个静态属性来给出下一个预期的结果

### 内部依赖关系

我称之为内部依赖，我完全像 CosmosDB 一样管理的依赖。CosmosDB 不支持像 SQL Server 那样的多客户端请求的事务(你必须创建一个服务器端 sp 来使用事务)，所以我必须在每次测试后清理数据库。下面是我做这件事的基类:

```
public class BaseCosmosTest : IAsyncLifetime
{
    public BaseCosmosTest()
    {
    }

    public async Task InitializeAsync()
    {
    }

    public async Task DisposeAsync()
    {
        var _client = TestFixture.GetInstance<DocumentClient>();
        var collections = _client.CreateDocumentCollectionQuery(UriFactory.CreateDatabaseUri(TestFixture.DataBaseName)).ToList();
        foreach (var item in collections)
        {
            var docs = _client.CreateDocumentQuery(item.SelfLink);
            foreach (var doc in docs)
            {
                await _client.DeleteDocumentAsync(doc.SelfLink);
            }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

*   我只删除文档而不删除集合，所以我的测试会运行得更快
*   IAsyncLifetime 在这里是为了拥有一个异步 Dispose 方法
*   我不能在测试前清理数据库，因为数据库在启动前不会被创建。调用 Configure 方法
*   我需要强制测试以非并行方式运行(不知道这个术语在英语中是否正确，但你明白我的意思)，因为它们都使用相同的数据库/集合，这里需要 xunit.runner.json(你需要将其复制到输出目录中) :

```
{  "parallelizeTestCollections":  false  } 
```

Enter fullscreen mode Exit fullscreen mode

## 新问题

这种测试方式当然有缺点:

*   测试失败的原因有很多，所以从长期来看，调试失败的测试可能比单元测试方法更困难。
*   您必须能够清除每个测试之间的所有依赖，如果没有，它们将属于外部类别。这意味着编写“仅测试”代码。
*   测试将需要更长的时间。
*   我没有尽可能多地测试:路由、控制器...我只能做 E2E 测试，但是它们太难做了，所以我只有几个。

## 引用

*   [http://David . heinemeierhansson . com/2014/TDD-is-dead-long-live-testing . html](http://david.heinemeierhansson.com/2014/tdd-is-dead-long-live-testing.html)
*   [https://lostechies . com/jimmybogard/2015/02/19/reliable-database-tests-with-respawn/](https://lostechies.com/jimmybogard/2015/02/19/reliable-database-tests-with-respawn/)