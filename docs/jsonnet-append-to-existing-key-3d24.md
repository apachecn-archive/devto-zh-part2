# JSON.NET 追加到现有键

> 原文：<https://dev.to/coolgoose/jsonnet-append-to-existing-key-3d24>

这是一个在 JSON.NET 对象中添加现有键的简单例子，因为对我来说，至少在文档中没有明确说明。

```
JObject myJson = new JObject(new JProperty("errors", new JObject()));

/// we can now reference it as follows just match the type

myJson["errors"].Value<JObject>().Add(new JProperty("title", title)); 
```

Enter fullscreen mode Exit fullscreen mode

小更新:

你甚至可以用同样的方法给一个 JArray 添加多个条目

```
/// assuming errors is a JArray, this will automatically append new entries
ErrorReponse["errors"].Value<JArray>().Add(new JObject(new JProperty("title", title))); 
```

Enter fullscreen mode Exit fullscreen mode