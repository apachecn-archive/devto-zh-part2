# 在 ASP.NET web forms 中实现 PageSession

> 原文：<https://dev.to/hardkoded/implementing-pagesession-in-aspnet-webforms--5958>

维护一个大页面的状态可能是一个相当大的挑战。假设您被要求实现一个包含四个选项卡的页面，每个选项卡包含两个 GridViews。每个 Gridview 都是可编辑的，你将有一个保存按钮来执行所有的数据库操作。

最常见的解决方案是:

*   将状态保持在视图状态。用户支付费用，得到一个更大的页面。
*   在服务器上保存状态。服务器支付费用，将数据保存在内存中。

虽然不建议公共网站使用会话(因为并发用户的数量未知)，但对于用户数量相对较少的内部网站点来说，这是一个很好的解决方案。

但是，如果您选择会话，如果用户在不同的选项卡上打开同一个页面，您就会遇到问题。这就是 PageSessions 发挥作用的时候了。

> 按页面而不是按页面收集会话不是很好吗...会话？

食谱很简单。我们将需要:

*   每个页面调用的唯一标识符。
*   使用唯一标识符作为在该页面上创建的所有会话密钥的前缀。
*   清除特定页面会话的方法。
*   使用所有这些元素的一种简单方法，就像在一个普通会话中一样。

## 先决条件

正如我在我的[上一篇文章](http://www.hardkoded.com/blogs/implementing-base-page-aspnet-project)中提到的，有一个`BasePage`是建立这种解决方案的好地方。

## 第一步:创建您的唯一标识符

唯一的页面标识符将帮助我们识别与全局会话集合中的特定页面实例相对应的元素。实现这一点的一个简单方法是使用 GUID。

我发现 [PreLoad](https://msdn.microsoft.com/en-us/library/system.web.ui.page.preload(v=vs.110).aspx) 事件是初始化这个 GUID 的一个很好的地方，因为我们可以确定当开发者需要它的时候 PageSession 会准备好(很可能是在 Page_Load，Control Events 和 Page_PreRender 上)

```
public class BasePage : Page
{
    public string PageKey
    {
        get => ViewState["PageInstanceUID"]?.ToString();
        internal set => ViewState["PageInstanceUID"] = value;
    }

    public BasePage()
    {
        PreLoad += BasePage_PreLoad;
    }

    private void BasePage_PreLoad(object sender, EventArgs e)
    {
        if (!IsPostBack && string.IsNullOrEmpty(PageKey))
        {
            PageKey = Guid.NewGuid().ToString();
        }
        else
        {
            PageKey = (string)(ViewState["PageInstanceUID"]);
        }
    }
} 
```

## 第二步:PageSession 类

现在是实现真正的 PageSession 类的时候了。正如我之前提到的，关键是使用之前创建的带有`PageKey`属性的普通会话作为前缀。

我们还想创建一个我们创建的所有键的列表，这样我们就可以从会话中删除这些项。

```
public class PageSession
{
    private readonly BasePage _page;

    public PageSession(BasePage page)
    {
        _page = page;
    }

    public object this[string name]
    {
        get => _page.Session[GetFullKey(name)];
        set
        {
            //We create the PageSession list
            if (_page.Session[_page.PageKey + "_SessionList"] == null)
            {
                _page.Session[_page.PageKey + "_SessionList"] = new List<string>();
            }

            //We add the item to the list
            if (!((List<string>)_page.Session[_page.PageKey + "_SessionList"]).Contains(GetFullKey(name)))
            {
                ((List<string>)_page.Session[_page.PageKey + "_SessionList"]).Add(GetFullKey(name));
            }

            //We add the item to the Session collection using the preffix
            _page.Session[GetFullKey(name)] = value;
        }
    }

    public void Clear()
    {
        Clear(_page.PageKey);
    }

    public void Clear(string pageKey)
    {
        if (HttpContext.Current.Session[pageKey + "_SessionList"] != null)
        {
            foreach (string item in HttpContext.Current.Session[pageKey + "_SessionList"] as List<string>)

            {
                if (HttpContext.Current.Session[item] != null)
                {
                    if (HttpContext.Current.Session[item] is IDisposable)
                    {
                        ((IDisposable)HttpContext.Current.Session[item]).Dispose();
                    }
                    HttpContext.Current.Session[item] = null;
                    HttpContext.Current.Session.Remove(item);
                }
            }

            HttpContext.Current.Session[pageKey + "_SessionList"] = null;
            HttpContext.Current.Session.Remove(pageKey + "_SessionList");
        }
    }

    public string GetFullKey(string name) => _page.PageKey + name;
} 
```

然后，我们在 BagePage 上创建一个 PageSession，并将其作为属性公开。

```
public class BasePage : Page
{
    public string PageKey
    {
        get => ViewState["PageInstanceUID"]?.ToString();
        internal set => ViewState["PageInstanceUID"] = value;
    }

    public PageSession PageSession { get; internal set; }

    public BasePage()
    {
        PreLoad += BasePage_PreLoad;
    }

    private void BasePage_PreLoad(object sender, EventArgs e)
    {
        if (!IsTruePostBack && string.IsNullOrEmpty(PageKey))
        {
            PageKey = Guid.NewGuid().ToString();
        }
        else
        {
            PageKey = (string)(ViewState["PageInstanceUID"]);
        }

        PageSession = new PageSession(this);
    }
} 
```

## 第三步:页面会话到期

这就是事情变得有点混乱的地方。PageSession expiration 背后的思想是试图识别用户何时离开页面。所以我们将使用 javascript [卸载事件](https://developer.mozilla.org/en-US/docs/Web/Events/unload)。

首先，我们需要创建一个 [WebMethod](https://msdn.microsoft.com/en-us/library/system.web.services.webmethodattribute(v=vs.110).aspx) ，这样我们就可以从 javascript 中清除 PageSession。

```
[WebMethod(true)]
public static void CleanUpPageSession(string pageKey)
{
   new PageSession(null).Clear(pageKey);
} 
```

然后，我们需要区分回发和真正的 Javascript 卸载。

```
ClientScript.RegisterHiddenField("IsBasePagePostBack", "");
ClientScript.RegisterHiddenField("BasePagePageKey", PageKey); 
```

现在，当我们确定页面正在进行回发时，我们将 IsBasePagePostBack 设置为“1”。

```
ClientScript.RegisterStartupScript(
    GetType(), 
    "OnUnloadMethods",
    "addEvent(window, 'unload', pageSessionCleanUp); function setBasePagePostBack(){{document.getElementById('IsBasePagePostBack').value = '1'; return true;}};", true);

ClientScript.RegisterOnSubmitStatement(
    GetType(), 
    "SafeUnload", 
    "setBasePagePostBack();"); 
```

最后但同样重要的是，我们在 javascript 中需要一个清理函数，所以我们可以调用我们的清理方法来执行 ajax 调用。

```
function pageSessionCleanUp() {
    if (document.getElementById('IsBasPagePostBack').value !== '1') {
        PageMethods.CleanUpPageSession(document.getElementById('BasePagePageKey').value);
    }
} 
```

# 最后的话

你可以在这里找到源代码[，随便叉，随便玩。](https://github.com/kblok/AspNetExamples)

不要停止编码！

最初发布于[harkoded.com](https://www.hardkoded.com/blogs/implementing-base-page-aspnet-project)