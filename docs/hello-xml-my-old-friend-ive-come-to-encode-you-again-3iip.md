# 你好 XML，我的老朋友；我又来给你编码了。

> 原文：<https://dev.to/rionmonster/hello-xml-my-old-friend-ive-come-to-encode-you-again-3iip>

*这篇文章最初发表在[我的博客](http://rion.io)上。*

根据您从事开发的时间长短，XML 可能是一个听起来完全陌生的术语。对另一些人来说，这可能会勾起他们压抑已久的对黑暗日子的回忆。

[![](img/8113d8a4ad168a23a88c3a4ca23b0fbb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QmHyRfN2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://rion.io/conteimg/2017/12/xml-conversion.PNG)

在我与各种经验的开发人员的许多接触中，我看到了从铁杆 JSON 爱好者到相信 XML 是一种真正编码的人的光谱。不管怎样，如果您在过去十年中编写过任何 web 服务或 Web APIs，那么您很可能曾经使用过其中的一个。

这篇文章是关于中间地带的:从 XML 到 JSON 的转换，以及沿着这条路走下去的许多潜在缺点。

## 你为什么会想这么做？

创建过 Word 文档吗？Excel 怎么样？消费了一个 RSS 源？设计了一个 SVG？配置了一个. NET 应用程序？几乎使用过任何数据行业标准(例如 HL7 等。)

如果您对其中任何一个问题的回答是肯定的，那么 XML 在某种程度上扮演了一个角色。它无处不在，而且非常擅长它所做的事情。见鬼。的。NET 团队试图在. NET 的初期将 T2 从 XML 中迁移出去。NET 核心，并最终爬回来，放弃了基于 JSON 的替代方案。

这有什么关系呢？嗯，假设您正在使用 Web API 构建一个全新的应用程序。你可能会一直使用 JSON，如果你需要使用一些 XML 编码的旧文件，这可能会是一个问题，谢天谢地，多亏了 JSON.NET，有一个技巧，我可以很有把握地假设它就在你当前的应用程序中:

```
public string ConvertXmlToJson(string xml)
{
    // Load your existing XML string into an XML document
    XmlDocument document = new XmlDocument();
    document.LoadXml(xml);

    // Return a JSON-encoded version of the document
    return JsonConvert.SerializeXmlNode(document);
} 
```

Enter fullscreen mode Exit fullscreen mode

同样，如果您需要将现有的 JSON 字符串转换成 XML，那么 JSON.NET 会再次支持您:

```
public string ConvertJsonToXml(string json)
{
    // Return an XML string representing the JSON data
    XNode document = JsonConvert.DeserializeXNode(json, "Root");
    return document.ToString();
} 
```

Enter fullscreen mode Exit fullscreen mode

就是这样。

现在值得注意的是，如果您计划将它直接返回给客户机作为 JSON 使用，那么您需要确保将适当的内容类型设置为“application/json”

## 路上潜在的坎坷

正如您可能想象的那样，这两种格式如此通用，在转换过程中很可能会发生一些疯狂的事情——您是对的。

来自 montewizidoh 的这个堆栈溢出响应做了一个非常全面的工作，解释了要检查的主要内容，以确保您的旅程不会太颠簸。

### XML 到 JSON

*   **所有数据都是字符串化的**-XML 对象中的所有数据都将被转换成它们的对应字符串(例如，“false”代替 false，“0”代替 0，等等)。)因为 Javascript 处理这些问题的方式非常不同，所以值得注意。
*   **子元素可以变成 nested-object []或 nested-array {}，这取决于是否只有一个或多个 XML 子元素**——嵌套对象和嵌套数组在 Javascript 中的使用方式非常不同，所以这是需要考虑的问题。JSON.NET 将识别一个`json:Array='true'`属性，在某些情况下可以使用它来解决这个问题。
*   **XML 必须是格式良好的**——虽然 XML 不必完全符合标准，但是您*必须*包含一个根元素，并且元素名称不能以数字开头。
*   **空元素不被转换**——XML 文档中的任何空元素都不会被转换成 JSON 并被忽略。

### JSON 到 XML 的转换问题

*   **需要一个顶级元素**——因为 XML 需要一个根元素，所以必须存在一个可以转换为 XML 结果的根的元素，否则解析器将失败。
*   **对象名不能以数字**开头——你的对象名不能以数字开头，因为它们不能被转换成元素。从技术上来说，XML 在这方面更加严格，但是有时您可以不遵守一些命名规则。

我敢肯定，您的里程数可能会有所不同，并且每个场景都可能需要一些数据消息会话，才能按预期转换所有内容。如果您必须在全新的 Web API 应用程序中使用任何遗留的 XML 文件，希望这能让您记住一些事情。