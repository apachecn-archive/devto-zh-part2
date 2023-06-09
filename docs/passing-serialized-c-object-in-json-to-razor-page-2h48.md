# 将 JSON 中序列化的 C#对象传递给 Razor 页面

> 原文：<https://dev.to/alexruzenhack/passing-serialized-c-object-in-json-to-razor-page-2h48>

我正在使用 C#和. net 将来自数据库的数据集传送到 Google Chart JavaScript 框架。NET 核心 MVC 框架下的 Razor 页面。

第一步很简单，对数据库进行简单的查询以获得数据，但是 Google Chart 有他们特殊的方法来处理数据集，并且实现从 POCO 的简单转换是不够的，然后我写了一个转换器:

```
private IEnumerable<Array> poco2GoogleChartDataset(IEnumerable<MyData> dataset)
{
    List<Array> googleDataset = new List<Array>();
    foreach (MyData data in dataset) {
        Array arr = new object[] { data.Label, data.Value };
        googleDataset.Add(arr);
    }
    return googleDataset.ToList();
} 
```

Enter fullscreen mode Exit fullscreen mode

使用`Newtonsoft.Json`程序集:
将转换的结果序列化为 JSON

```
// Top of the class
using Newtonsoft.Json;

// Hidden for brevity
ReportJson = JsonConvert.SerializeObject(poco2GoogleChartDataset(myDataset)); 
```

Enter fullscreen mode Exit fullscreen mode

另一方面，在 Razor Page 中，我需要将这个 JSON 对象传递给一个 JavaScript 变量。如果您只是使用简单的 Razor 语法传递模型值，它将不起作用，因为在 Razor 渲染过程中将通过 HTML 编码传递值。像这样:

```
// Razor Page
...
@section Scripts {
    function drawChart() {
        var jsonDataset = @Model.ReportJson;
        ...
    }
...
} 
```

Enter fullscreen mode Exit fullscreen mode

```
jsonDataset result:
[[\&#x22;Label\&#x22;, value]] 
```

Enter fullscreen mode Exit fullscreen mode

为了绕过 HTML 编码，将模型变量用它们的`Raw`方法包装到 HtmlHelper 指令中。这边:

```
// Razor Page
...
@section Scripts {
    function drawChart() {
        var jsonDataset = @Html.Raw(Model.ReportJson);
        ...
    }
...
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，值结果将是一个 JSON 对象，准备在 Google Chart 框架中使用。

我花了几个小时才获得这种理解，部分原因是我不掌握 Razor 语法，也不了解 ASP.NET 核心 MVC，也不了解 C#😂

就是这样！