# 用网站地图在 Umbraco 上爬行

> 原文：<https://dev.to/swimburger/crawling-through-umbraco-with-sitemaps-4ck6>

网站有各种形状和大小。有的很快，有的很美，有的完全一塌糊涂。如果人们找不到它，那么它是否是一个高质量的网站就无关紧要了，但是搜索引擎可以提供帮助。虽然登上首页的竞争很激烈，但本系列将深入探讨一些常见的做法，让你的网站更容易被抓取。

系列:

1.  用**网站地图**在 Umbraco 中爬行(这个😎)
2.  [带着**机器人**在 Umbraco 爬行](https://swimburger.net/blog/umbraco/crawling-through-umbraco-with-robots)

## 什么是网站地图？

网站地图是一种工具，方便搜索引擎抓取你的网站。从本质上来说，网站地图是一个简单的 XML 文件，列出了你希望被搜索引擎索引的所有页面。当爬虫访问你的主页时，它将索引那个页面+寻找它能跟随到其他页面的所有链接。使用这种方法，爬虫可能找不到你的所有页面。因此，网站地图将帮助搜索引擎抓取你的网站，并找到那些难以找到的网页。这是一个非常常见的工具，可以追溯到 2005 年，当时它由谷歌推出，并一直使用到今天。

示例:

```
<?xml version="1.0" encoding="UTF-8" ?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
    <url>
        <loc>https://swimburger.net/</loc>
        <lastmod>8/27/2018 4:05:17 AM</lastmod>
        <changefreq>weekly</changefreq>
        <priority>0.5</priority>
    </url>
    <url>
        <loc>https://swimburger.net/blog/</loc>
        <lastmod>9/3/2018 7:15:27 PM</lastmod>
        <changefreq>weekly</changefreq>
        <priority>0.5</priority>
    </url>
    ...
</urlset> 
```

Enter fullscreen mode Exit fullscreen mode

关于网站地图的更多信息，以下是一些很好的资源:

*   [https://en.wikipedia.org/wiki/Sitemaps](https://en.wikipedia.org/wiki/Sitemaps)
*   [https://www.sitemaps.org/protocol.html](https://www.sitemaps.org/protocol.html)
*   [https://support.google.com/webmasters/answer/156184?hl=en](https://support.google.com/webmasters/answer/156184?hl=en)

## 在 Umbraco 中实现网站地图

如上所述，站点地图最终是一个简单的 XML 文件，列出了搜索引擎要搜索的所有页面。但是我们如何在 Umbraco 中生成这个 xml 文件呢？

在开始编写代码之前，有一些事情需要考虑:

*   Umbraco 的内容树本质上是一个可嵌套的结构，而 sitemap XML 是一个不允许嵌套的平面列表。
*   网站地图至少需要知道页面的位置(URL)和最后修改日期，尽管有两个选项:更改频率和优先级。
*   并不是所有的内容都是一个页面，但是只有页面应该在站点地图中列出。
*   网站通常有一些不应该被索引的页面。

这转化为以下要求:

*   表示页面的内容项目需要与非页面内容项目相区别。
*   页面内容项嵌套结构需要展平。
*   特定的页面内容项可以选择不出现在站点地图中。
*   特定的页面内容项需要保存更改频率和优先级的数据。“位置”和“最后修改时间”是每个内容项目都具有的内置属性。

### 添加一个 BasePage 组成

我们将从全新的 Umbraco 安装(7.12 版)开始，并使用默认的 ContentModel `live`模式。

为了能够区分非页面内容项和页面内容项，我们将引入一个所有页面文档类型都必须继承的`Base Page`组合。

将以下配置添加到您的基本页面组合中:

*   爬网(选项卡)

    *   从站点地图中排除
    *   类型:复选框(真/假)
    *   别名:sitemapExclude
    *   网站地图更改频率
    *   类型:自定义编辑器下拉列表
    *   别名:sitemapChangeFrequency
    *   预告片:
        *   总是
        *   每小时地
        *   每天地；天天地
        *   一周的
        *   每月
        *   每年的
        *   从不
    *   网站地图优先级
    *   类型:自定义编辑器小数
    *   别名:站点地图优先级
    *   配置:
        *   最小值:0
        *   步长:0.1
        *   最大值:1

[![Umbraco Crawling Tab](../Images/760ac31a6de0d4a893440872bd4bb4b1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2yorskOd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.swimburger.net/media/1086/crawling-tab.png%3Fwidth%3D1098%26height%3D640)

我们已经有了自己的组合，但是还没有任何页面文档类型。让我们用模板创建两种页面文档类型:

*   主页
    *   允许主页下的通用页面
    *   允许根目录下的主页
*   通用页面
    *   允许通用页面下的通用页面

[![Umbraco Templates](../Images/a1b1d9f20af1a9674b45f8ad37f75261.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tHuuc72v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.swimburger.net/media/1087/umbraco-templates.png%3Fwidth%3D1016%26height%3D325)

让我们保持这些页面的模板非常简单，如下所示:

*GenericPage.cshtml*

```
@inherits Umbraco.Web.Mvc.UmbracoTemplatePage<ContentModels.GenericPage>
@using ContentModels = Umbraco.Web.PublishedContentModels;
@{
    Layout = null;
}
<h1>@Model.Content.Name</h1> 
```

Enter fullscreen mode Exit fullscreen mode

*HomePage.cshtml*

```
@inherits Umbraco.Web.Mvc.UmbracoTemplatePage<ContentModels.HomePage>
@using ContentModels = Umbraco.Web.PublishedContentModels;
@{
    Layout = null;
}
<h1>@Model.Content.Name</h1> 
```

Enter fullscreen mode Exit fullscreen mode

现在文档类型已经准备好了，让我们创建一些内容吧！下面是我创作的内容截图。

[![Umbraco Content Tree](../Images/18a161eeb636e6bebefdceeaa5388342.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--McRhN0l_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.swimburger.net/media/1088/content-tree.png%3Fwidth%3D353%26height%3D241)

除了我们的“秘密推广页面”之外，这些页面需要显示在站点地图中，所以一定要选中“从站点地图中排除”开关。

### 生成站点地图文件

我们已经完成了一半，我们已经准备好了数据和结构，可以放入站点地图了。为了生成站点地图，我们需要用模板再创建一个文档类型。这一次，文档类型将完全为空，因为我们不需要任何属性，但是我们需要允许在主页下创建 Sitemap 文档类型。让我们在主页下添加一个名为“网站地图”的网站地图内容项。

终于该写点代码了！将下面的 Razor 代码添加到站点地图模板中。

```
@inherits Umbraco.Web.Mvc.UmbracoTemplatePage
@{
    Layout = null;
    umbraco.library.ChangeContentType("application/xml");
    var pages = UmbracoContext.ContentCache.GetByXPath("//*[./sitemapExclude='0']");
}<?xml version="1.0" encoding="UTF-8" ?>
<urlset >
@foreach (var page in pages)
{
    <url>
        <loc>@page.UrlAbsolute()</loc>
        <lastmod>@page.UpdateDate</lastmod>
        <changefreq>@(page.GetPropertyValue<string>("sitemapChangeFrequency", "weekly"))</changefreq>
        <priority>@(page.GetPropertyValue("sitemapPriority", 0.5))</priority>
    </url>
}
</urlset> 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们将 HTTP 响应的文档类型设置为‘application/xml ’,因为我们返回的是 XML 而不是 HTML。之后我们进行 XPath 查询，查询所有属性设置为 false (0)的内容。

一旦我们有了页面，我们迭代每个页面并打印出 URL、UpdateDate、sitemapChangeFrequency 和 sitemapPriority。

当访问我们的站点地图内容项时，服务器返回我们全新的站点地图。

### 为/sitemap.xml 设置重写规则

搜索引擎不要求站点地图文件被称为“sitemap”或“sitemap.xml”，也不要求站点地图在你的域的根。虽然，最常见的是你可以通过浏览`/sitemap.xml`找到一个站点的站点地图，所以让我们遵守这个不成文的标准。

开箱即用，Umbraco 不允许您添加文件扩展名到您的内容项目。如果我们将“sitemap”内容项重命名为“sitemap.xml”，则 Umbraco 生成的 URL 是`/sitemapxml`(如果您将内容项重命名为“sitemap.xml”，则将其重命名回“sitemap”)。

正如任何事情一样，一个给定的问题有多种解决方案。对于站点地图，最简单的解决方案是使用简单的 IIS 重写。

将以下重写配置添加到 web.config 文件中:

```
<?xml version="1.0" encoding="utf-8"?>
<configuration>
   ...
   <system.webServer>
     ...
     <rewrite>
      <rules>
        <rule name="Rewrite to Sitemap.xml">
          <match url="^sitemap.xml" />
          <action type="Rewrite" url="sitemap" />
        </rule>
      </rules>
     </rewrite>
     ...
  </system.webServer>
  ...
</configuration> 
```

Enter fullscreen mode Exit fullscreen mode

`/sitemap.xml`和`/sitemap`现在都返回我们的新网站地图。

### 什么黑客，谷歌说网站地图的 URL 是重复的内容！？

当你把你的站点地图提交给谷歌时，你可能会注意到谷歌实际上“排除”了你站点地图中的许多 URL。你可以在谷歌网站管理员控制台的覆盖率报告下查看。

[![Webmaster Coverage Console](../Images/1b0b79fdbcb45b96b00f05f7114d3397.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qqd--Dge--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.swimburger.net/media/1085/webmaster-snippet.png%3Fwidth%3D827%26height%3D794)

有许多可能的原因导致某些网址被排除在外，但有一个原因我想特别提请你注意。

谷歌发现以下网址都返回相同的内容，并只选择其中一个网址显示在谷歌搜索中。

*   [https://www.contoso.com/about-us](https://www.contoso.com/about-us)
*   [https://www.contoso.com/about-us/](https://www.contoso.com/about-us/)
*   [https://www.contoso.com/ABOUT-US](https://www.contoso.com/ABOUT-US)

谷歌报告这些不同网址的原因是因为在你网站的某个地方你附加了“/”，而在其他地方你省略了“/”。这种不一致会导致两个 URL 都出现在 Google 网站管理员工具中，并被标记为重复内容。

这里有更多来自谷歌的[关于这个问题的信息](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html)以及如何解决它。

以下是解决此问题的一些建议:

*   选择强制 URL 的**附加或不附加斜线**
*   选择强制 URL 的**全部小写**
*   保持你的 URL 的一致性(网站地图，HTML，外部链接)
*   当传入的请求 URL 不遵循强制规则时，通过设置**重定向**来强制一致性

#### 要斜线还是不要斜线

在 Umbraco 中有一个设置，当使用 Umbraco 的 API 来生成 URL 时，它将总是追加，或者总是省略尾部的斜杠。

该设置称为`**addTrailingSlash**`，可设置为真或假。选择权在你，谷歌并不偏爱任何一个，但它确实关心一致性！

默认情况下，Umbraco 生成的 URL 总是小写的，但也许你有一些自定义代码来生成 URL。

确保遵循相同的时尚，除非出于某种原因需要不同的大小写。如果您确实需要在网站的某些部分使用不同的大小写，请确保 IIS 重定向不会影响这些内容。

#### 重定向不一致的 URL

您可以使用两种重定向来增强一致性:一种用于尾随斜杠，另一种用于小写 URL。

如果您想强制不使用尾随斜线，请使用以下重定向:

```
<rule name="Remove trailing slash" stopProcessing="true">
    <match url="(.*)/$" />
    <conditions>
        <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="true" />
        <add input="{REQUEST_FILENAME}" matchType="IsDirectory" negate="true" />
    </conditions>
    <action type="Redirect" redirectType="Permanent" url="{R:1}" />
</rule> 
```

Enter fullscreen mode Exit fullscreen mode

如果你想强制使用斜杠，使用这个:

```
<rule name="Add trailing slash" stopProcessing="true">
    <match url="(.*[^/])$" />
    <conditions>
        <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="true" />
        <add input="{REQUEST_FILENAME}" matchType="IsDirectory" negate="true" />
    </conditions>
    <action type="Redirect" redirectType="Permanent" url="{R:1}/" />
</rule> 
```

Enter fullscreen mode Exit fullscreen mode

如果你想强制使用小写的 URL，使用这个重定向规则:

```
<rule name="Convert to lower case" stopProcessing="true">
    <match url=".*[A-Z].*" ignoreCase="false" />
    <action type="Redirect" url="{ToLower:{R:0}}" redirectType="Permanent" />
</rule> 
```

Enter fullscreen mode Exit fullscreen mode

既然谷歌看到了这些重定向，重复内容的数量应该会减少。搜索引擎会更好地理解所有前面提到的网址是同一个页面。

### 其他搜索引擎如何找到我的网站地图？

在 Bing (+ Yahoo)和 Google 网站管理员工具中，我们都可以提交和验证我们的站点地图，但是没有网站管理员工具的搜索引擎怎么办？

我们不能指望搜索引擎神奇地知道我们站点地图的正确网址，所以在`robots.txt`文件中有一个属性，你可以把你站点地图的网址放在那里。

```
Sitemap: https://swimburger.net/sitemap.xml
User-agent: *
Disallow: /umbraco 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，这个属性不支持相对 URL，只支持绝对 URL，这意味着我们也必须动态地生成我们的`robots.txt`文件。我们可以应用与网站地图相同的技术，并使用下面的模板。

```
@inherits UmbracoTemplatePage
@{
    Layout = null;
    umbraco.library.ChangeContentType("text/plain");
    var rootUrl = Request.Url.GetLeftPart(UriPartial.Authority);
}Sitemap: @string.Format("{0}{1}", rootUrl, "/sitemap.xml")
User-agent: *
Disallow: /umbraco 
```

Enter fullscreen mode Exit fullscreen mode

关于这一点的更详细的演练，请关注第 2 部分:用机器人在 Umbraco 中爬行。

### 结论

在这篇文章中，我们看到了如何创建一个基于 Umbraco CMS 内容动态生成的站点地图文件。我们利用了一个 BasePage 组合来过滤内容，只过滤页面，它允许从站点地图中逐页排除内容。

正如我之前说过的，对于一个给定的问题，有很多解决方案。这篇文章中描述的解决方案适用于中小型网站，但可能不适用于大型网站。下面列出了可能需要改进的地方:

*   将查询逻辑移动到控制器，并将一个专用的视图模型传递给满足站点地图结构的视图。
*   缓存站点地图输出
*   定期预先生成网站地图，并将 XML 文件存储在磁盘上
*   将站点地图分割成多个站点地图以防止站点地图变得太大
*   如果您有自定义路由，并且 URL 结构与 Umbraco 内容树的 1 对 1 不匹配，则生成自定义 URL

希望这篇文章对你的项目有所帮助，欢迎在下面的评论中提出任何问题或建议！

**源代码:**你可以在 GitHub

**上找到[的 Sitemap 代码来源:](https://github.com/SNiels/Umbraco.Crawling.Sitemap)**当我为自己的工作进行这个练习时，这些源代码非常有价值。

*   [https://en.wikipedia.org/wiki/Sitemaps](https://en.wikipedia.org/wiki/Sitemaps)
*   [https://www.sitemaps.org/](https://www.sitemaps.org/)
*   [https://support.google.com/webmasters/answer/156184?hl=en](https://support.google.com/webmasters/answer/156184?hl=en)