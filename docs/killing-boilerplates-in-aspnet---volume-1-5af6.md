# 杀死 ASP.NET 的样板文件-第 1 卷

> 原文：<https://dev.to/hardkoded/killing-boilerplates-in-aspnet---volume-1-5af6>

我们都讨厌样板文件，对吧？你一遍又一遍复制的那堆代码。样板文件不是一段复杂的代码，而是简单到我们没有时间去思考的代码。

在这篇文章中，我将介绍如何减少我们使用的一些样板文件:

*   从 QueryString 或 AppSettings 获取值。
*   保持视图状态或会话数据同步。

# QueryString 和 AppSettings 样本

这两个样板文件很相似，也很简单。您需要从 QueryString 或 AppSettings 中获取一些值，将其转换为有效的数据类型，并将其设置为您的一个变量。您的代码可能如下所示:

```
public partial class Default : System.Web.UI.Page
{
   int _userId;
   bool _someFeatureToggle;
   protected void Page_Load(object sender, EventArgs e)
   {
       _userId = SomeMethodToSafelyParseAString<int>(Request.QueryString["userId"]);
       _someFeatureToggle = SomeMethodToSafelyParseAString<bool>(ConfigurationManager.AppSettings["SomeFeatureToggle"]);
   }
   public void SomeMethod()
   {
       var data = new Service().GetData(_userId, _minValidDate);
   }
} 
```

是啊，没什么大不了的，但是，最后，这是一个样板。

# 同步存储在视图状态或会话中的数据

因此，假设您有一个数据集，当用户在页面上执行不同的操作时，您需要保持它的同步。我敢打赌你的代码是这样做的:

*   如果不是回发，则初始化数据。
*   如果是回发就还原。
*   一些改变数据的动作。
*   将数据保存在存储器中。

```
public partial class Default : System.Web.UI.Page
{
   int _userId;
   bool _someFeatureToggle;
   DataSet _data;

   protected void Page_Load(object sender, EventArgs e)
   {
       _userId = SomeMethodToSafelyParseAString<int>(Request.QueryString["userId"]);
       _someFeatureToggle = SomeMethodToSafelyParseAString<bool>(ConfigurationManager.AppSettings["SomeFeatureToggle"]);
       if (!IsPostBack)
       {
           _data = new Service().GetData(_useID, _someFeatureToggle);
       }
       else
       {
           _data = (DataSet)PageSession["data"];
       }
   }
   protected void Grid_ItemCommand(object sender, RowCommandEventArgs args)
   {
       if (args.CommandName == "DeleteFirstRow")
       {
           _data.Tables[0].Rows[0].Delete();
       }
   }
   protected void Page_PreRender(object sender, EventArgs e)
   {
       PageSession["data"] = _data;
   }
} 
```

这是我们想要避免的样板文件！

# 属性为救援

如果我们可以使用属性实现所有这些样板文件，那不是很好吗？

我们的代码应该是这样的:

```
public partial class Default : System.Web.UI.Page
{
   [QueryString]
   int _userId;
   [AppSettings]
   bool _someFeatureToggle;
   [KeepInSession]
   DataSet _data;

   protected void Page_Load(object sender, EventArgs e)
   {
   }

   protected void Grid_ItemCommand(object sender, RowCommandEventArgs args)
   {
       if (args.CommandName == "DeleteFirstRow")
       {
           _data.Tables[0].Rows[0].Delete();
       }
   }
   protected void Page_PreRender(object sender, EventArgs e)
   {
   }
} 
```

# 实现属性

为了实现这个解决方案，我们需要两样东西:标记字段的属性，以及处理这些属性并执行我们需要的任务的一些代码。

编写属性代码非常容易。我们不需要太多代码，因为它们只是用来标记我们的变量。

```
[AttributeUsage(AttributeTargets.Field)]
public sealed class QueryStringAttribute : Attribute
{
    public string QueryStringKey { get; set; }

    public QueryStringAttribute()
    {
    }
    public QueryStringAttribute(string queryStringKey)
    {
        QueryStringKey = queryStringKey;
    }
} 
```

# 让我们设置一些变量

正如我在之前的帖子[中提到的实现 page session](http://www.hardkoded.com/blogs/implementing-pagesession-aspnet-project),[预加载](https://msdn.microsoft.com/en-us/library/system.web.ui.page.preload(v=vs.110).aspx)事件非常适合这个，因为我们需要那些变量尽快准备好被使用。

另一件很酷的事情是在我们的母版页上支持这些属性。我们可以从这样做开始:

```
void BasePage_PreLoad(object sender, EventArgs e)
{
   EvalFieldsOnPreLoad(this);
   var master = Master;
   while (master != null)
   {
       EvalFieldsOnPreLoad(master);
       master = master.Master;
   }
} 
```

我不会把整个代码放在这里，因为你可以在它的 [GitHub repo](https://github.com/kblok/AspNetExamples/tree/master/PageAttributesDemo) 上查看。但是`EvalFieldsOnPreLoad`看起来像这样:

```
private void EvalFieldsOnPreLoad(object instance)
{
   var type = instance.GetType();
   while (type.Equals(typeof(Page)) || type.Equals(typeof(UserControl)))
   {
       foreach (var field in type.GetFields(
           BindingFlags.NonPublic | BindingFlags.Public | 
           BindingFlags.Instance | BindingFlags.DeclaredOnly))
       {
           var queryStringAttribute = GetAttributeFromField<QueryStringAttribute>(field);
           if (queryStringAttribute != null)
           {
                if (string.IsNullOrEmpty(queryStringAttribute.QueryStringKey))
                {
                    field.SetValue(instance, 
                        Convert.ChangeType(
                            Request.QueryString[field.Name], 
                            field.FieldType));
                }
                else if(!string.IsNullOrEmpty(Request.QueryString[queryStringAttribute.QueryStringKey]))
                {
                    field.SetValue(instance, 
                        Convert.ChangeType(
                            Request.QueryString[queryStringAttribute.QueryStringKey], 
                            field.FieldType));
                }
           }
       type = type.BaseType;
   } 
```

我们已经走了一半了。我们的 QueryStringAttribute 和 AppSettingsAttribute 将使用这段代码。现在我们需要完成 KeepInSession 和 KeepInViewState 属性的数据同步。

由于`PreLoad`事件是尽早加载数据的最佳位置，所以 [PreRenderComplete](https://www.google.com.ar/search?q=PreRenderComplete&oq=PreRenderComplete&aqs=chrome..69i57j0l5.318j0j1&sourceid=chrome&ie=UTF-8) 事件是存储数据的最佳位置，因为它是在我们的代码执行后被调用的(通常是 Page_Load、Events 和 Page_PreRender)。所以我们可以这样做:

```
void BasePage_PreRenderComplete(object sender, EventArgs e)
{
   EvalFieldsOnPreRenderComplete(this);
   var pageMaster = Master;
   while (pageMaster != null)
   {
       EvalFieldsOnPreRenderComplete(pageMaster);
       pageMaster = pageMaster.Master;
   }
} 
```

EvalFieldsOnPreRenderComplete 应该是这样的:

```
private void EvalFieldsOnPreRenderComplete (object instance)
{
   Type type = instance.GetType();
   while (type.Equals(typeof(Page)) || type.Equals(typeof(UserControl)))
   {
       foreach (var field in type.GetFields(
           BindingFlags.NonPublic | BindingFlags.Public | 
           BindingFlags.Instance | BindingFlags.DeclaredOnly))
       {
           var keepInSessionAttribute = GetAttributeFromField<KeepInSessionAttribute>(field);
           if (keepInSessionAttribute != null)
           {
               Session[field.Name + "AutoSave"] = field.GetValue(instance);
           }
       }
       type = type.BaseType;
   }
} 
```

# 最后的话

我喜欢这个解决方案。如果你实现了这一点，你的代码将会更清晰，更具声明性。随意从 [GitHub](https://github.com/kblok/AspNetExamples/tree/master/PageAttributesDemo) 上叉。

不要停止编码！

最初发布于[harkoded.com](https://www.hardkoded.com/blogs/killing-boilerplates-in-aspnet-volume-1)