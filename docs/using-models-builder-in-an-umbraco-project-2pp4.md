# 在 Umbraco 项目中使用模型生成器

> 原文：<https://dev.to/mozzydev/using-models-builder-in-an-umbraco-project-2pp4>

不久前，我向[概述了模型构建器为您提供的不同选项](https://mozzy.dev/posts/getting-started-with-models-builder/)。我没有涉及太多的细节，但是我确实解释了为什么您可能想要使用一个选项而不是另一个。

### 结构

快进一点，我认为我已经达到了一个我满意的方法，允许我以我想要的方式构建东西，而不增加额外的复杂性。我更熟悉 MVC 类型的场景，所以这是我通常为 Umbraco 项目使用的解决方案。

*   项目。核心类库
*   项目。Web.UI -网站项目(参考上文)

可能还会有一些其他的项目，比如测试。

我通常会将我的模型放在核心项目中，然后在 UI 项目的视图中使用它们。为此，有一些配置选项可以指导 Models Builder 如何创建模型。

```
<add key="Umbraco.ModelsBuilder.Enable" value="true" />
<add key="Umbraco.ModelsBuilder.ModelsMode" value="AppData" />
<add key="Umbraco.ModelsBuilder.ModelsNamespace" value="Project.Web.Core.Models.Content" />
<add key="Umbraco.ModelsBuilder.ModelsDirectory" value="~/../Project.Web.Core/Models/Content/" />
<add key="Umbraco.ModelsBuilder.AcceptUnsafeModelsDirectory" value="true" /> 
```

Enter fullscreen mode Exit fullscreen mode

以上操作将把由 Models Builder 生成的模型放到我的项目中。这样我就可以随心所欲地使用它们了。将它们包含在项目中并进行编译，它们将包含在项目 dll 中。

这样做的好处是，如果你已经有了一个使用`Model.Content.GetPropertyValue("propertyAlias")`的项目，将它们转换成强类型模型并不太困难。Models Builder 本身基本上为您完成了所有这些工作。如果你刚开始一个项目，那么有一些其他的选项可以满足你的模型/贴图需求。以下都不会生成模型，因此在使用模型生成器时有很大不同。它们确实在映射和粒度视图模型方面提供了更复杂的场景，可能是您更喜欢的。

*   [Umbraco 映射器](https://github.com/AndyButland/UmbracoMapper)
*   [UmbMapper](https://github.com/JimBobSquarePants/UmbMapper)
*   [同上](https://github.com/umco/umbraco-ditto)

### 使用模型

我现在已经有了一堆文件，这些文件为我提供了从 Umbraco 生成的模型。这些是内容模型，它们都继承自`PublishedContentModel`。它们是分部类。当谈到使用这些模型时，它可能会通过控制器实例化，或者它可能只是通过 Umbraco 的路由为我而存在。

在看来，这是我们可以做的:

```
@inherits UmbracoViewPage<MyModel> 
```

Enter fullscreen mode Exit fullscreen mode

然后，视图中会有 intellisense，我会收到编译错误的通知。使用强类型模型的一个好处是。由于我们使用了`UmbracoViewPage`,我们也可以访问所有的 Umbraco helper 方法。

好的，听起来很棒。但是，使用 Umbraco 为我生成的模型有点不确定，它与 Umbraco 中的内容紧密相关。如果我想扩展这个或者拥有我自己的财产呢？嗯，有一个解决办法。官方文档建议我们创建另一个分部类，并在那里添加我们需要的东西，但我认为我更喜欢这种方法。我们可以继承模型生成器给我们的模型。

```
public class FridayBeersViewModel : FridayBeers
{ 
  public FridayBeersViewModel(IPublishedContent content) 
    : base(content) { } 

  // custom properties 
  public bool ShowBanner => BannerImage != null;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们仍然可以从生成的模型中获得所有的属性，并且我们还可以构建一个定制的视图模型，它与我们的视图中的输出更加匹配。顺便提一下，如果您的视图模型最终与生成的模型没有什么关系，那么您可能不应该从它继承，而只能滚动您自己的视图模型。

在控制器中，你会有这样的东西。

```
public class FridayBeersController : BasePageController
{ 
  public ActionResult FridayBeers() 
  { 
    var model = new FridayBeersViewModel(CurrentPage); 
    // apply any other updates on the model 
    return CurrentTemplate(model); 
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我劫持了路线，这样我就可以修改 Umbraco 的默认行为。我还利用了 Umbraco 中基于模板的路由，其中模板名称与我的操作相匹配。`BasePageController`只是从`SurfaceController`和`IRenderMvcController`继承而来，有一个名为`CurrentTemplate`的方法解析模板并将模型传递给视图。下面是 Umbraco 核心中的例子:[在 github](https://github.com/umbraco/Umbraco-CMS/blob/7ee510ed386495120666a78c61497f58ff05de8f/src/Umbraco.Web/Mvc/RenderMvcController.cs#L96) 上的视图。

### 作文

上面的例子对于一个页面或者一个内容块来说是很好的，但是我们可能想把事情分解得更细一些，更好地利用共享代码。当我们使用组合时，我们从生成的感觉中得到的是一个接口，然后我们的内容模型实现那些属性。他们也可以利用许多组成部分。这很棒，因为这意味着如果我们愿意，我们可以将界面定义为视图中的模型。例如，如果我们有一个像 IMetaData 这样的接口，我们可以创建一个相关的部分来处理我们站点上的 meta 标签。

```
@inherits UmbracoViewPage<IMetaData> 
<meta name="description" content="@Model.MetaDescription" />
// etc. 
```

Enter fullscreen mode Exit fullscreen mode

或者，我们可能有几页有横幅，有几页没有。我们只是让我们的文档类型使用合成，他们知道如何呈现横幅。

### ModelTypeAlias

我相信在开发一个 Umbraco 网站的过程中，你会创建一个包含一些常量的类，这些常量与文档类型的别名有关。根据文档类型改变代码的简单方法。嗯，Models Builder 以一种直接的方式提供了这一点。

```
public partial class ExamplePage : PublishedContentModel
{ 
  public new const string ModelTypeAlias = "ExamplePage"; 
  public new const PublishedItemType ModelItemType = PublishedItemType.Content; 

  public BasePage(IPublishedContent content) 
    : base(content) { } 

  // other properties
} 
```

Enter fullscreen mode Exit fullscreen mode

要使用这个，需要的时候做`ExamplePage.ModelTypeAlias`就可以了。

### 总结

我已经概述了一些在项目中使用 Models Builder 的方法，这些方法可能超出了开箱即用的范围，因此您可以以一种简单的方式充分利用它。作为一种模式，这应该考虑到容易的可扩展性和消费来自 Umbraco 的内容的快速方式。

#### 进一步阅读

*   [构建器模式](https://our.umbraco.org/documentation/Reference/Templating/Modelsbuilder/Builder-Modes)
*   [理解并扩展](https://our.umbraco.org/documentation/Reference/Templating/Modelsbuilder/Understand-And-Extend)
*   [路由/定制控制器](https://our.umbraco.org/documentation/Reference/Routing/custom-controllers)