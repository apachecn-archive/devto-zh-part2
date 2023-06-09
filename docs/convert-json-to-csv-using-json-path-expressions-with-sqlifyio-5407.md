# 通过 Konbert 使用 JSON 路径表达式将 JSON 转换为 CSV

> 原文：<https://dev.to/moboudra/convert-json-to-csv-using-json-path-expressions-with-sqlifyio-5407>

JSON 路径是引用 JSON 文档中元素的一种非常简单和直观的方式，类似于 CSS 选择器，但是对于 JSON。

这就是它的工作方式，如果你有一个这样的文档:

```
[  {  "name":  "Christopher",  "age":  "23",  },  {  "name":  "Michael",  "age":  "45",  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

您可以用这个表达式提取所有人员记录的名字:

```
$.*.name 
```

Enter fullscreen mode Exit fullscreen mode

其中:

*   `$`引用根元素，在本例中是一个数组
*   `*`是重复表达式，所以它将匹配数组中的每个对象
*   `name`是获取对象的名称属性

因此，如果您使用这个表达式来转换为 CSV，结果将是:

```
name
Christopher
Michael 
```

Enter fullscreen mode Exit fullscreen mode

当您需要展平非常复杂的 JSON 文档，并且您只想提取几个字段时，这非常有用。

继续尝试我们的定制 [JSON 到 CSV 转换工具](https://konbert.com/convert/json/to/csv)。欢迎任何反馈！