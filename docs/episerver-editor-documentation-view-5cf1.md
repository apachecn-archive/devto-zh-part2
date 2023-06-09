# 创建 Episerver 编辑器文档视图

> 原文：<https://dev.to/debpu06/episerver-editor-documentation-view-5cf1>

## 快速汇总

在这篇文章中，我将描述如何在 Episerver 中添加一个自定义编辑器视图，用于呈现文档。我们的例子是从 Github 获取文档，但是我将讨论如何扩展实现来从其他来源获取文档。
[![Editor View In Use](img/8d042d25000fe731202d3a0cba36a661.png)](//images.ctfassets.net/9usgdrjsks5w/3MemdUUmbCcmcAgc4mm60G/bc9d6a304ccd012216c00808bf5191d2/Editor_View_In_Use.gif) 
为什么要添加文档视图？显而易见的答案是让编辑更快地访问文档。我最初为一个有几十种页面和块类型的客户创建了这个视图，其中一些有几十个具有复杂逻辑的属性。当你开始编辑一个页面时，能够立即看到属性的描述，我们认为这是非常有价值的。

不太明显的原因是它迫使开发人员添加文档。当实现了新的页面和块类型时，编辑器可以看到的空白页面是开发人员添加文档的良好动机。

## 用法

你可以点击查看完整解决方案[。若要将它添加到您的项目中，请将以下项目放入您的解决方案中，并在您的 web 项目中引用它们:](https://github.com/debpu06/Alloy-Demo-Project/tree/feature-editor-documentation-view)

*   文档。插件
*   文档。插件.核心
*   文档。插件. Github

从那里，你需要添加视图[**~/Views/editorviewcodumentation/index . cshtml**](https://github.com/debpu06/Alloy-Demo-Project/blob/feature-editor-documentation-view/Alloy%20Demo%20Site/Views/EditorViewDocumentation/Index.cshtml)。接下来，添加必要的 ServiceConfigurationContext 调用:

```
context.InitializeDocumentationPlugin(new GithubConfigurationOptions()
{ 
    GithubApiToken = ConfigurationManager.AppSettings["GithubApiKey"], 
    GithubRepositoryName = ConfigurationManager.AppSettings["GithubRepositoryName"], 
    GithubRepositoryOwner = ConfigurationManager.AppSettings["GithubRepositoryOwner"], 
    GithubDocumentationFolder = ConfigurationManager.AppSettings["GithubDocumentationFolder"], 
    GithubBranch = ConfigurationManager.AppSettings["GithubBranch"], 
    GithubApiUrl = ConfigurationManager.AppSettings["GithubApiUrl"]
}); 
```

Enter fullscreen mode Exit fullscreen mode

以及 appSettings 中列出的设置。“GithubDocumentationFolder”是 markdown 文档文件的存储库路径。在我的例子中，我的文档文件夹在存储库根目录中。你可以在下面看到我的设置。

```
<!-- Github Api key can be generated by going to 
  Settings > Developer Settings > Personal Access Tokens in Github -->
<add key="GithubApiKey" value=" ***************************"></add>

<!-- Github repo owner. Can be the user or group owner -->
<add key="GithubRepositoryOwner" value="debpu06"></add>

<!-- Github Repository Name -->
<add key="GithubRepositoryName" value="Alloy-Demo-Project"></add>

<!-- Relative path to folder that contains documentation. This 
  example shows that the 'Documentation' folder is in the repository root -->
<add key="GithubDocumentationFolder" value="Documentation"></add>

<!-- specific branch of the documentation to use -->
<add key="GithubBranch" value="feature-editor-documentation-view"></add>

<!-- Github api url -->
<add key="GithubApiUrl" value="https://api.github.com"></add> 
```

Enter fullscreen mode Exit fullscreen mode

最后，您需要做的就是实现 [**文档。plugin . interfaces . I 文档中的**页面类型上的](https://github.com/debpu06/Alloy-Demo-Project/blob/feature-editor-documentation-view/Documentation.Plugin/Interfaces/IDocumented.cs)接口。在我的例子中，我将它添加到了起始页。它返回到“StartPage.md ”,但可以基于每个实例进行设置。

```
[ContentType( GUID = "19671657-B684-4D95-A61F-8DD4FE60D559", 
    GroupName = Global.GroupNames.Specialized)] 
[SiteImageUrl] 
public class StartPage : SitePageData, IDocumented 
{ 
    [Display( Name = "Documentation Reference", GroupName = SystemTabNames.Content, Order = 310)] 
    [CultureSpecific] 
    public virtual string DocumentationReference 
    { 
        get { return this.GetPropertyValue(page => page.DocumentationReference) ?? "StartPage.md"; } 
        set { this.SetPropertyValue(page => page.DocumentationReference, value); } 
    } 
```

Enter fullscreen mode Exit fullscreen mode

然后，您可以用 markdown 文档文件的名称填充 episerver 中的属性。[![property in epi](img/18fec39dc2df86244b3570daf4448ec3.png)T2】](//images.ctfassets.net/9usgdrjsks5w/4YHn5XCSVasswAe2aCWky0/3d80af5262b80b9a298fb7416eace5f2/property_in_epi.png)

这就是让编辑器文档视图在您的 Episerver 站点上工作所需的全部内容。

## 实现细节

如果您想自己实现它或者调整现有的特性，我将更详细地介绍我是如何实现文档视图的。

#### 自定义 Episerver 视图配置

第一步是创建 ViewConfiguration 的子类，并将其作为服务注册到 Episerver。我创建了一个名为 EditorViewConfiguration 的子类。在构造函数中，可以看到我给继承的 ViewConfiguration 属性赋了一些值。其中大部分是不言自明的，但请注意“视图类型”属性。这是将呈现文档视图的视图的名称。

```
 namespace Documentation.Plugin.Infrastructure{ [ServiceConfiguration(typeof(EPiServer.Shell.ViewConfiguration))] public class EditorViewConfiguration : ViewConfiguration<pagedata>
    {
        public EditorViewConfiguration()
        {
            Key = "EditorViewConfiguration";
            Name = "Editor Documentation";
            Description = "Page Showing Editor Documentation";
            ControllerType = "epi-cms/widget/IFrameController";
            ViewType = "/EditorViewDocumentation/";
            IconClass = "epi-iconForms";
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我创建了一个名为 IDocumented 的接口，它有一个名为 DocumentationReference 的字符串属性。

```
namespace Documentation.Plugin.Interfaces
{
    public interface IDocumented
    {
        string DocumentationReference { get; set; }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我让基本页面“SitePageData”实现了这个接口，这样它的所有子类页面类型也将实现它。

附注:我争论过这个属性是否应该被编辑。在很大程度上，单个页面类型如何工作的文档并没有改变，只是改变了内容。然而，对于我在最初实现中使用的项目，他们有一些页面，这些页面有很多逻辑，在不同的活动中表现非常不同。因此，我们最终使属性可编辑，以允许不同页面实例的文档。

下一步是创建模型、视图和控制器，用于在 CMS 中呈现编辑器文档视图。视图和视图模型相当简单。该视图只输出我们将从文档库中获取的原始数据。我们上面提到的“视图类型”将是我们添加 Index.cshtml 文件的地方。

**~/Views/editor view documentation/index . cs html**

```
 Editor Documenation
    @model Documentation.Plugin.Models.EditorViewDocumentationViewModel

    @{
        Layout = string.Empty;
    }

    @Html.Raw(Model.DocumentationContent) 
```

Enter fullscreen mode Exit fullscreen mode

ViewModel 只包含编辑器文档标记的字符串属性。

```
namespace Documentation.Plugin.Models
{
    public class EditorViewDocumentationViewModel
    {
        public string DocumentationContent { get; set; }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在[控制器](https://github.com/debpu06/Alloy-Demo-Project/blob/feature-editor-documentation-view/Documentation.Plugin/Controllers/EditorViewDocumentationController.cs)中，我们在 Index 方法中做的第一件事是从查询字符串中获取对页面的引用。然后，我们将它与 IContentRepository 的一个实例一起使用，以获得页面实例，我们将它转换为上面提到的 IDocumented 接口。

```
 //Get the episerver content id from the query string
    var epiId = System.Web.HttpContext.Current.Request.QueryString["id"];

    //Gets the current content based on the id
    IDocumented currentContent = _contentRepository.Get<contentdata>(new ContentReference(epiId)) as IDocumented; 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们可以使用 IDocumented 实例和我们的文档存储库(我们将在下面讨论)来获取要呈现的标记，并将其添加到 ViewModel，然后我们将它和视图一起返回。

```
 //Grab the documentation markup
    var result = _documentationRepository.GetDocumentationById(currentContent.DocumentationReference);
    if (!string.IsNullOrEmpty(result))
    {
        model.DocumentationContent = result;
    }
    else
    {
        model.DocumentationContent = $"<p>No documentation exists for: {currentContent.DocumentationReference}</p>";
    } 
```

Enter fullscreen mode Exit fullscreen mode

完整的文件可以在[这里](https://github.com/debpu06/Alloy-Demo-Project/blob/feature-editor-documentation-view/Documentation.Plugin/Controllers/EditorViewDocumentationController.cs)查看

#### 获取 Github 文档

为了获得 Github 文档，我们可以创建一个定制类 GithubDocumentationRepository 来处理向 Github 查询文档降价文件。

*附注:为了使事情变得简单，我对文档的结构做了一些假设。首先，文档将在 Github 中保持一致。我在项目存储库中创建了一个名为“Documentation”的文件夹，用于保存 markdown 文件。其次，每个页面都有自己的降价文件。*

GithubDocumentationRepository 实现了 [IDocumentationRepository](https://github.com/debpu06/Alloy-Demo-Project/blob/feature-editor-documentation-view/Documentation.Plugin.Models/Interfaces/IDocumentationRepository.cs) ，它定义了一个方法:

```
string GetDocumentationById(string reference); 
```

Enter fullscreen mode Exit fullscreen mode

GithubDocumentationRepository 还子类化了[base documentation repository](https://github.com/debpu06/Alloy-Demo-Project/blob/feature-editor-documentation-view/Documentation.Plugin.Models/Repository/BaseDocumentationRepository.cs)。在[base documentation repository](https://github.com/debpu06/Alloy-Demo-Project/blob/feature-editor-documentation-view/Documentation.Plugin.Models/Repository/BaseDocumentationRepository.cs)中，我们正在做两件事。首先，我们用正确的头和 Github API 的 url 初始化 HttpClient】

```
protected void BuildHttpClient(string userName, string token, string apiUrl)
{
    _client = new HttpClient();
    _client.BaseAddress = new Uri(apiUrl);
    _client.DefaultRequestHeaders.Authorization = GetBasicAuthHeader(userName, token);
    _client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
    _client.DefaultRequestHeaders.Add("User-Agent", "Anything");
}

private AuthenticationHeaderValue GetBasicAuthHeader(string userName, string token)
{
    string format = $"{userName}:{token}";
    string authString = Convert.ToBase64String(Encoding.ASCII.GetBytes(format));
    return new AuthenticationHeaderValue("Basic", authString);
} 
```

Enter fullscreen mode Exit fullscreen mode

我们还在这个基类中对 Github API 进行 Get 调用。

```
protected string GetDocumentationData(string requestUrl)
{
    using (var httpResponse = _client.GetAsync(requestUrl).Result)
    {
        if (httpResponse.StatusCode != HttpStatusCode.OK)
            return string.Empty;

        return httpResponse.Content.ReadAsStringAsync().Result;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

你可以在这里抓取整个文件[。
回到我们的](https://github.com/debpu06/Alloy-Demo-Project/blob/feature-editor-documentation-view/Documentation.Plugin.Models/Repository/BaseDocumentationRepository.cs)[GithubDocumentationRepository](https://github.com/debpu06/Alloy-Demo-Project/blob/feature-editor-documentation-view/Documentation.Plugin.Github/Repository/GithubDocumentationRepository.cs)，我们需要通过首先调用上面创建的 **BuildHttpClient** 来初始化存储库。

```
public GithubDocumentationRepository(IConfigurationOptions configurationOptions)
{
    _configurationOptions = configurationOptions as GithubConfigurationOptions;
    //if for some reason options are not set, throw exception
    if (_configurationOptions == null)
    {
        throw new ArgumentException(typeof(GithubConfigurationOptions).FullName);
    }

    BuildHttpClient(_configurationOptions.GithubRepositoryOwner, _configurationOptions.GithubApiToken, _configurationOptions.GithubApiUrl);
} 
```

Enter fullscreen mode Exit fullscreen mode

您首先会注意到的是 GithubConfigurationOptions。我们接下来将讨论这些，但目前只知道它们是添加到 appSettings 的值，允许我们连接到 Github API。

从那里我们可以实现上面提到的 GetDocumentationById 方法。我们从构建 Github API 请求 url 开始。然后，我们用我们的请求调用基本文档存储库中定义的 GetDocumentationData 方法。

```
 var requestUrl =
        $"/repos/{_configurationOptions.GithubRepositoryOwner}/" +
        $"{_configurationOptions.GithubRepositoryName}" +
        $"/contents/{_configurationOptions.GithubDocumentationFolder}/{reference}?ref=" +
        $"{_configurationOptions.GithubBranch}";

    //Getting json information about the file
    var responseData = GetDocumentationData(requestUrl); 
```

Enter fullscreen mode Exit fullscreen mode

Github 不会直接返回文件的内容。相反，它返回有关文件的信息。关于它的更多信息可以在这里找到。

然后，我们将响应反序列化为一个名为 [GithubResponse](https://github.com/debpu06/Alloy-Demo-Project/blob/feature-editor-documentation-view/Documentation.Plugin.Github/Models/GithubResponse.cs) 的自定义对象。

```
 //Converting response to object
    var deserializedObject = JsonConvert.DeserializeObject<GithubResponse>(responseData); 
```

Enter fullscreen mode Exit fullscreen mode

除了直接模仿 Github API 获取内容的响应之外，这个文件没有什么可说的。完整的实现可以在[这里](https://github.com/debpu06/Alloy-Demo-Project/blob/feature-editor-documentation-view/Documentation.Plugin.Github/Models/GithubResponse.cs)找到。其中最重要的部分是属性 **DownloadUrl** 。使用这个 url，我们可以获得文档文件的实际降价。我们可以使用与获取文件初始信息相同的 **GetDocumentationData** 方法。

```
 //Get markdown from url of file
   var documentMarkdown = GetDocumentationData(deserializedObject.DownloadUrl) ?? string.Empty; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经有了实际的降价，我们只需要把它转换成 html，这样当我们把它传递给视图时就可以呈现了。为此，我使用了一个名为 [Markdig](https://github.com/lunet-io/markdig) 的第三方插件。完成的方法看起来像这样:

```
public string GetDocumentationById(string reference)
{
    var requestUrl =
        $"/repos/{_configurationOptions.GithubRepositoryOwner}/" +
        $"{_configurationOptions.GithubRepositoryName}" +
        $"/contents/{_configurationOptions.GithubDocumentationFolder}/{reference}?ref=" +
        $"{_configurationOptions.GithubBranch}";

    //Getting json information about the file
    var responseData = GetDocumentationData(requestUrl);

    //if information on file is not found return markup message
    if (string.IsNullOrEmpty(responseData))
    {
        //handling will show no documentation message when response is empty string
        return string.Empty;
    }

    //Converting response to object
    var deserializedObject = JsonConvert.DeserializeObject<GithubResponse>(responseData);

    //Get markdown from url of file
    var documentMarkdown = GetDocumentationData(deserializedObject.DownloadUrl) ?? string.Empty;

    //return markdown as html
    return Markdig.Markdown.ToHtml(documentMarkdown);
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 汇集一切

为了注册我们的**GithubDocumentationRepository**，我为 ServiceConfigurationContext 创建了一个扩展方法，用于将**GithubDocumentationRepository**注册为**IDocumentationRepository**singleton。

```
public static class ServiceConfigurationContextExtensions
{
    public static void InitializeDocumentationPlugin(this ServiceConfigurationContext context, IConfigurationOptions options)
    {
        var githubOptions = options as GithubConfigurationOptions;
        if (githubOptions != null)
        {
            context.Services.AddSingleton<idocumentationrepository githubdocumentationrepository>();
            context.Services.AddSingleton<iconfigurationoptions githubconfigurationoptions>(locator =&gt; githubOptions);
            return;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

你可能注意到了，**GithubConfigurationOptions**再次弹出。它们被传递到**InitializeDocumentationPlugin**方法中，并在创建 **IConfigurationOptions** 类型的单例时使用。

```
namespace Documentation.Plugin.Github.Models
{
    public class GithubConfigurationOptions : IConfigurationOptions
    {
        public string GithubRepositoryOwner { get; set; }
        public string GithubRepositoryName { get; set; }
        public string GithubApiToken { get; set; }
        public string GithubApiUrl { get; set; }
        public string GithubDocumentationFolder { get; set; }
        public string GithubBranch { get; set; }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，这包含了所有关于连接到 Github API 的信息。它实现的 **IConfigurationOptions** 接口实际上不包含任何方法或属性。它的目的是允许我们创建配置选项的其他实现，并将它们注册为单件。当我们谈论这个项目的扩展时，我们将在下面对此进行更多的讨论。

最后一步是初始化一切。您可以将以下内容添加到 web 项目的初始化类中:

```
context.InitializeDocumentationPlugin(new GithubConfigurationOptions()
{
    GithubApiToken = ConfigurationManager.AppSettings["GithubApiKey"],
    GithubRepositoryName = ConfigurationManager.AppSettings["GithubRepositoryName"],
    GithubRepositoryOwner = ConfigurationManager.AppSettings["GithubRepositoryOwner"],
    GithubDocumentationFolder = ConfigurationManager.AppSettings["GithubDocumentationFolder"],
    GithubBranch = ConfigurationManager.AppSettings["GithubBranch"],
    GithubApiUrl = ConfigurationManager.AppSettings["GithubApiUrl"]
}); 
```

Enter fullscreen mode Exit fullscreen mode

它将调用 ServiceConfigurationContext 扩展，并传递它从应用程序设置中获取的所有 Github API 属性。

你可以在 Episerver Alloy 演示网站[这里](https://github.com/debpu06/Alloy-Demo-Project/tree/feature-editor-documentation-view)查看完整实现。

## 下一步

如果您想将这个实现添加到您的解决方案中，您可以遵循上面的[用法](#usage)一节中的步骤。

#### 自定义造型

对于开发人员来说，查看渲染后的降价很常见。在最初的实现过程中，我没有看到向最终的文档视图添加任何定制样式的需要。然而，如果你有专门的内容编辑人员，他们不会像你一样花太多时间去阅读 READMEs，那么定制样式可能是个好主意。

#### 自定义文档来源

如果您想要定制解决方案来为不同的文档源工作，您可以通过创建自己的**idocumentionrepository**和 **IConfigurationOptions** 的实现来实现，然后注册它们，而不是调用**InitializeDocumentationPlugin**。我将使用 Confluence 作为文档来源来跟进一个实现。

如果您有任何建议，请随时创建[拉动请求](https://github.com/debpu06/Alloy-Demo-Project)或在[推特](https://twitter.com/debpu06)上给我发消息