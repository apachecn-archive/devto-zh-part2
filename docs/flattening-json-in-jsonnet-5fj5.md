# 在 JSON.NET 扁平化 JSON

> 原文：<https://dev.to/anotherdevblog/flattening-json-in-jsonnet-5fj5>

*最初发表于[另一个开发博客](http://anotherdevblog.net/posts/flattening-json-in-json-net)。*

## 用例

我在工作中遇到了一个有趣的问题，我需要获取任意的 JSON blob(对象或数组)并将内存中的叶节点表示为键/值对的集合。比如给定这个 JSON:

```
[  {  "Name":  "Fish",  "Color":  "Silver",  "Attributes":  [  {  "Name":  "Environment",  "Value":  "Aquatic"  },  {  "Name":  "Parts",  "Value":  [  {  "Type":  "fin",  "Length":  3  }  ]  }  ]  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

我希望看到类似这样的输出:

```
[0].Name = Fish
[0].Color = Silver
[0].Attributes[0].Name = Environment
[0].Attributes[0].Value = Aquatic
[0].Attributes[1].Name = Parts
[0].Attributes[1].Value[0].Type = fin
[0].Attributes[1].Value[0].Length = 3 
```

Enter fullscreen mode Exit fullscreen mode

以下是我所做的，以及我学到的教训

## 第一课:并非所有东西都在堆栈上

“这听起来像是一个非常常见的用例，”我对自己说，“在文档或 StackOverflow 上肯定有一些东西。”

没有。我在 StackOverflow 上搜索了很长时间，虽然我找到了一些关于 Java 库的答案，但是我没有找到我们在这里使用的 Json.NET 库的答案。互联网上最受欢迎的 NuGet 包，以前没有人面对过这个问题？真的吗？！

这花了几个小时和大量的调试，但我最终编写了一个扩展方法，允许我获取任意 JSON:
的叶节点值

```
public static class JExtensions
{
    public static IEnumerable<JValue> GetLeafValues(this JToken jToken)
    {
        if (jToken is JValue jValue)
        {
            yield return jValue;
        }
        else if (jToken is JArray jArray)
        {
            foreach (var result in GetLeafValuesFromJArray(jArray))
            {
                yield return result;
            }
        }
        else if (jToken is JProperty jProperty)
        {
            foreach (var result in GetLeafValuesFromJProperty(jProperty))
            {
                yield return result;
            }
        }
        else if (jToken is JObject jObject)
        {
            foreach (var result in GetLeafValuesFromJObject(jObject))
            {
                yield return result;
            }
        }
    }

    #region Private helpers

    static IEnumerable<JValue> GetLeafValuesFromJArray(JArray jArray)
    {
        for (var i = 0; i < jArray.Count; i++)
        {
            foreach (var result in GetLeafValues(jArray[i]))
            {
                yield return result;
            }
        }
    }

    static IEnumerable<JValue> GetLeafValuesFromJProperty(JProperty jProperty)
    {
        foreach (var result in GetLeafValues(jProperty.Value))
        {
            yield return result;
        }
    }

    static IEnumerable<JValue> GetLeafValuesFromJObject(JObject jObject)
    {
        foreach (var jToken in jObject.Children())
        {
            foreach (var result in GetLeafValues(jToken))
            {
                yield return result;
            }
        }
    }

    #endregion
} 
```

Enter fullscreen mode Exit fullscreen mode

然后在我的调用代码中，我只从返回的`JValue`对象中提取了`Path`和`Value`属性:

```
var jToken = JToken.parse("blah blah json here");
foreach (var jValue in jToken.GetLeafValues()
{
    Console.WriteLine("{jValue.Path} = {jValue.Value}");
} 
```

Enter fullscreen mode Exit fullscreen mode

厉害！

## 第二课:但是在 StackOverflow 上*总是*

所以结果是*是*对这个用例([链接](https://stackoverflow.com/questions/7394551/c-sharp-flattening-json-structure))的 StackOverflow 的一个回答。我在搜索类似“获取所有叶节点”或“获取所有路径值”的术语，但让答案出现的神奇关键词是“扁平化”以下是发布的答案代码:

```
JObject jsonObject=JObject.Parse(theJsonString);
IEnumerable<JToken> jTokens = jsonObject.Descendants().Where(p => p.Count() == 0);
Dictionary<string, string> results = jTokens.Aggregate(new Dictionary<string, string>(), (properties, jToken) =>
{
    properties.Add(jToken.Path, jToken.ToString());
    return properties;
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 第三课:但是你不能总是照搬 StackOverflow 上的东西

哇，这个代码片段比我的解决方案要短得多，所以我试了一下。但最终还是回到了我自己的世界。原因如下:

1.  这个解决方案不管用，至少对我的情况来说不行。我需要它来处理任意的 JSON blob。我不能保证它会是一个`JObect`——它可能是一个数组或者其他什么东西，所以不幸的是，这个解决方案在我的第一个测试用例(数组)中就失败了。而且`JToken`没有一个方便的小`Descendants()`方法，我可以像`JObject`那样调用它，所以我必须做一些类型检查。呸。
2.  另一个问题是:这个解决方案在内存中构建了一个字典来表示扁平化的结构。我正在处理一些相当大的对象，加载初始的`JToken`已经够痛苦的了。我真的不想在此基础上增加字典的内存压力。
3.  说到内存，我希望(最终)只返回不为 null 或不为值类型默认值的 JValue。
4.  那个`.Count()`看起来真的很昂贵，因为它是一个在每一个后代上被调用的方法，不管你最终是否使用这个后代。可能更安全的做法是只选择你知道是`JValue`对象的后代:`.Descendants().OfType<JValue>()`。然后就可以叫`.Value`了。当你有一个`JValue`对象时，你可以调用`.Value`并获得底层原语(或伪原语字符串)值，而无需调用`.ToString()`。