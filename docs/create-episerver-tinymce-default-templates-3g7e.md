# 创建 Episerver TinyMCE 默认模板

> 原文：<https://dev.to/debpu06/create-episerver-tinymce-default-templates-3g7e>

## 快速汇总

在这篇文章中，我将描述如何给你的 Episerver TinyMCE 添加定制模板。使用模板可以最大限度地减少需要进行的编辑工作，从而改善编辑人员的内容输入体验。这在处理大量以相似方式格式化的内容时特别有用。

## 【episerver】&tinymce

如果您不熟悉 TinyMCE，您可能通过 WYSIWYG(所见即所得)或富文本编辑器了解它。这是一个允许你添加自定义字体、样式甚至图像的属性。您甚至可以编辑内容的标记，并查看它在您的站点上会如何呈现。

[![TinyMCE Example Image](../Images/f17e64db4ea89946bca7f613d87d2b5c.png)T2】](//images.ctfassets.net/9usgdrjsks5w/pPgAp2lZ96Sk8wiEMu4OE/38324ca92847289e16164942a2cab2af/TinyMCE_Example_Image.PNG)

从 Episerver 11 开始，TinyMCE 的功能被放到了它自己的 NuGet 包 [Episerver 中。CMS.TinyMCE](https://nuget.episerver.com/package?id=EPiServer.CMS.TinyMce) 。当我在浏览 [TinyMCE 文档](https://www.tiny.cloud/docs/)寻找可以配置的不同插件和特性时，我发现了这个模板特性。

虽然 TinyMCE 可以做很多配置，但在这篇文章中，我将只关注模板特性。虽然我计划在未来进行更多的扩展，但我鼓励您自己查看一下 [Episerver 文档](https://world.episerver.com/documentation/developer-guides/CMS/add-ons/customizing-the-tinymce-editor-v2/)，了解如何进行配置。我已经提到的 [TinyMCE 文档](https://www.tiny.cloud/docs/)包含了关于各个插件的信息。

## 实现

我将详细介绍设置模板所需的各个步骤。我在 Episerver Alloy 站点上使用两个基本模板创建了一个简单的例子。可以在我的 [Github 资源库](https://github.com/debpu06/Alloy-Demo-Project/tree/feature-tinymce-templates)上查看，以供参考。

#### 构建模板

模板本身只是普通的 HTML。您可以将它们添加到您的解决方案中，并使用您希望的任何自定义样式对它们进行自定义。我构建的[示例](https://github.com/debpu06/Alloy-Demo-Project/tree/feature-tinymce-templates/Alloy%20Demo%20Site/Static/html/templates)，其中一个显示在下面，只是由我从 Alloy 站点的另一个部分提取的一些标记组成。

```
<div class="mceTmpl">
    <div class="span8">
        <h1>Title</h1>
        <p class="introduction">This is some introduction text</p>
        <div class="row">
            <div class="span8 clearfix">
            </div>
        </div>
        <div class="row">
            <div class="block articlepage teaserblock span4">
                <div class="border">
                    <h2>Sub Item Title</h2>
                    <p>Subtitle.</p>
                </div>
            </div>
        </div>
    </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

TinyMCE 要求您在自定义模板中拥有的唯一东西是一个用 **mceTmpl** 类包围您的模板的 *div* 。

#### TinyMCE 初始化

现在您已经构建了模板，您需要在初始化时将它们添加到 TinyMCE 设置中。

正如我提到的，我不会通过所有不同的方式来配置 TinyMCE。我建议阅读我上面提供的链接。你能配置的一些东西是自定义样式格式，块格式，什么插件能被使用和在工具栏中，甚至是 TinyMCE 渲染时的高度和宽度。Alloy 项目中的[ExtendedTinyMceInitialization](https://github.com/debpu06/Alloy-Demo-Project/blob/feature-tinymce-templates/Alloy%20Demo%20Site/Business/Initialization/ExtendedTinyMceInitialization.cs)有几个将这些特性添加到特定内容类型的例子。我为我们的模板添加了以下内容:

```
 public void ConfigureContainer(ServiceConfigurationContext context){ context.Services.Configure<tinymceconfiguration>(config =&gt;
    {
        // Add content CSS to the default settings.
        config.Default()
            .ContentCss("/static/css/editor.css")
            .AddSetting("templates", new[]
            {
                new
                {
                    title = "Article Template 1",
                    url = "../../Static/html/templates/article_template_1.html",
                    description = "Template w/title and text"
                },
                new
                {
                    title = "Article Template 2",
                    url = "../../Static/html/templates/article_template_2.html",
                    description = "Template with title and image"
                }
            })
            .AddPlugin($"{DefaultValues.EpiserverPlugins} template")
            .Toolbar($"{DefaultValues.Toolbar} | template");
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

正如您在上面看到的，我们为 TinyMCE 配置添加了一个新的“模板”设置。该设置采用一个对象列表，每个对象指定模板的标题、模板文件的路径和模板的说明。

```
 new
    {
        title = "Article Template 2",
        url = "../../Static/html/templates/article_template_2.html",
        description = "Template with title and image"
    }, 
```

Enter fullscreen mode Exit fullscreen mode

添加设置后，您需要指定您正在使用模板插件的事实。我将它与 Episerver 指定的默认插件一起添加。

```
 .AddPlugin($"{DefaultValues.EpiserverPlugins} template") 
```

Enter fullscreen mode Exit fullscreen mode

最后，您需要指定您希望模板图标出现在工具栏中。同样，我将它添加到 Episerver 指定的默认工具栏项目旁边。请注意，与添加插件时不同，您必须用垂直管道分隔不同的项目。

```
 .Toolbar($"{DefaultValues.Toolbar} | template"); 
```

Enter fullscreen mode Exit fullscreen mode

这就是让模板工作所需的全部内容。

[![TinyMCE Template Selection](../Images/be767b2eccb479cbef265092064aa938.png)T2】](//images.ctfassets.net/9usgdrjsks5w/1eEnnZuVBWEQmaUKMeYo4U/9557255ea37de559228dffebeb1a9d00/TinyMCE_Template_Selection.gif)

## 最后的想法

总的来说，我认为这对你的编辑来说是一个非常强大的功能。尤其是当您想要开始基于内容类型指定模板时。我可以看到这一点被利用的几个地方是，如果有一个通过小调整一次又一次更新的活动。或者是否会有大量内容从非 Episerver 站点迁移过来，比如博客文章。这无疑可以帮助编辑快速创建页面结构，从而获得更好的复制内容的体验。

如果您有任何想法、建议或能想到这些模板的其他用例，请告诉我。一如既往，您可以随意创建[拉取请求](https://github.com/debpu06/Alloy-Demo-Project)或在 [twitter 上给我发消息](https://twitter.com/debpu06)