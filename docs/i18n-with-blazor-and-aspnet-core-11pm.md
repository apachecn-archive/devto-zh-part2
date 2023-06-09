# 带 Blazor 和 Aspnet 核心的 I18n

> 原文：<https://dev.to/rembou1/i18n-with-blazor-and-aspnet-core-11pm>

# 将 ASPNET 核心作为后端服务的 Blazor 应用程序国际化

开发人员的一个共同任务是将他们的应用程序翻译成用户语言。大多数框架都提供了工具来轻松实现这样的任务，并让开发人员专注于其他事情，如愚蠢的需求或错误的 API。Blazor 是一个新的框架，没有这样的东西。在这篇博文中，我将向你展示我的方法。

## 服务器端

翻译将存储在服务器端，客户端将要求所有的翻译在其语言。我这样做是为了避免在客户端下载所有语言的所有翻译，这对大型应用程序来说可能很沉重。我的服务器端是一个 APSNET Core 2.1 应用程序，所以我将使用现有的功能来管理它们。

### 调教

内置翻译系统易于安装。在 ConfigureService 方法中，您添加了

```
services.AddLocalization(options => options.ResourcesPath = "Resources"); 
```

Enter fullscreen mode Exit fullscreen mode

稍后将使用该文件夹名称。

在配置方法上添加

```
 var supportedCultures = new[]
  {
      new CultureInfo("en"),
      new CultureInfo("fr"),
  };
  app.UseRequestLocalization(new RequestLocalizationOptions
  {
      DefaultRequestCulture = new RequestCulture("en"),
      // Formatting numbers, dates, etc.
      SupportedCultures = supportedCultures,
      // UI strings that we have localized.
      SupportedUICultures = supportedCultures
  }); 
```

Enter fullscreen mode Exit fullscreen mode

您可以在其中设置您的应用程序可以处理的所有语言。ASPNET Core 将使用 Http 头“Accept-Language”来决定使用哪些翻译，如果不支持，它将使用默认的翻译。

### 资源文件

所有翻译后的内容都存储在资源文件中，这些资源文件将在部署后嵌入到服务器组件中。您需要创建一个 Resources 文件夹，并在其中添加一个名为“Client.fr.resx”的资源文件:

*   翻译是按类分组的，所以这里我们将使用一个名为“Client”的虚拟类来翻译客户端的所有翻译
*   “fr”是我要翻译的文化的两个字母代码，我可以使用更具体的文化，如“fr-CA”。

Visual Studio 为这类文件提供了一个表格编辑器，但是 xml 内容非常简单，你可以在这里找到我的测试数据。

### API

现在我需要一个 API 来发送给定语言的所有译文。这是我的 API

```
[ApiController, Route("api/[controller]/")]
    public class I18nController : ControllerBase
    {
        private IStringLocalizer<Client> stringLocalizer;

        public I18nController(IStringLocalizer<Client> stringLocalizer)
        {
            this.stringLocalizer = stringLocalizer;
        }

        [HttpGet]
        public ActionResult GetClientTranslations()
        {
            var res = new Dictionary<string, string>();
            return Ok(stringLocalizer.GetAllStrings().ToDictionary(s => s.Name, s => s.Value));
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

*   IStringLocalizer 是一个用于本地化内容的 ASPNET 核心接口
*   我为分组所有客户端翻译而创建的空类中的客户端
*   没有传递语言，因为框架将从 HTTP 头“Accept-Language”中获取语言，并用正确的翻译初始化 IStringLocalizer。你也可以设置 ASPNET 核心，这样它就可以从 url 参数、cookies 等中获取语言

## 客户端

### 服务

我将创建一个加载好语言翻译的服务。这是我的服务

```
public class RemoteI18nService : II18nService
    {
        private readonly IHttpApiClientRequestBuilderFactory httpApiClientRequestBuilderFactory;
        private string _lg;
        private Lazy<Task< Dictionary<string, string>>> translations;

        public RemoteI18nService(IHttpApiClientRequestBuilderFactory httpApiClientRequestBuilderFactory)
        {
            this.httpApiClientRequestBuilderFactory = httpApiClientRequestBuilderFactory;
            translations = new Lazy<Task<Dictionary<string, string>>>(() => FetchTranslations(null));
        }

        private async Task<Dictionary<string, string>> FetchTranslations(string lg)
        {
            var client = httpApiClientRequestBuilderFactory.Create("/api/i18n");
            if (lg != null)
                client.SetHeader("accept-language", _lg);
            Dictionary<string, string> res = null;
            await client.OnOK<Dictionary<string, string>>(r => res = r).Get();
            return res;
        }

        public async Task<string> Get(string key)
        {
            return !(await translations.Value).TryGetValue(key, out string value) ? key : value;
        }

        public void Init(string lg)
        {
            translations = new Lazy<Task<Dictionary<string, string>>>(() => FetchTranslations(lg));
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

*   IHttpApiClientRequestBuilderFactory 只是一个 HttpClient 的包装器，我创建它是为了方便地处理不同的响应代码
*   我使用的是延迟加载，所以我确信翻译只会被请求一次。我的服务将作为单例注入(我尽量避免在服务器端注入，但在客户端注入真的很有用)。这是 Program.cs 上的 DI 设置

```
 serviceProvider = new BrowserServiceProvider(configure =>
            {
                ...
                configure.Add(new ServiceDescriptor(
                   typeof(II18nService),
                   typeof(RemoteI18nService),
                   ServiceLifetime.Singleton));
            }); 
```

Enter fullscreen mode Exit fullscreen mode

### 组件

现在我需要一个组件来显示给定键的正确翻译。这是我的

```
@inject II18nService i18n;

@displayValue

@functions{
    [Parameter]
    string key { get; set; }

    public string displayValue { get; set; }
    protected override async Task OnInitAsync()
    {
        await i18n.Get(key).ContinueWith(t =>
        {
            displayValue = t.Result;
            this.StateHasChanged();
        });
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

*   除了使用 ContinueWith/StateHasChanged，我没有添加其他选择，因为否则字符串不会更新。
*   我不能在视图中直接使用 i18nService，因为视图呈现不是异步的(问题 [1240](https://github.com/aspnet/Blazor/issues/1240) ,而我的 Get()方法是
*   我认为这种技术可能是错误的，因为它会在一个大的应用程序上创建许多非常小的组件。但这是我目前唯一找到的方法。

### 集成显示一个翻译我是这样做的:

```
 <Trad key="Welcome" /> 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

我选择对后端和前端使用相同的翻译存储机制(资源文件),这样可以简化文件管理，并且我们可以在两个部分之间重用一些翻译。开发人员的体验并不完美，可能会对性能产生影响，但仍然有效。这里最大的挑战是找到一个好的异步方法来调用 Get 方法，就像当前版本的 Blazor (0.5.1)调用。结果“或”。等待”的任务结果。