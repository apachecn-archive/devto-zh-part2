# 用 XSLT 格式化 XML

> 原文：<https://dev.to/lefebvre/format-xml-with-xslt-2011>

您知道可以使用 XSLT 来格式化您的 XML，使它更具可读性吗？XSLT 代表可扩展样式表语言。这个 XSLT 可用于格式化 XML:

```
<?xml version="1.0" encoding="UTF-8"?>
<xsl:transform version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
<xsl:output method="xml" indent="yes" />
<xsl:template match="/">
<xsl:copy-of select="/" />
</xsl:template>
</xsl:transform> 
```

Enter fullscreen mode Exit fullscreen mode

使用任何可用的 XSLT 转换命令将这个 XSLT 应用于 XML。

要在 Xojo 中使用这个 XSLT，需要在项目中添加一个模块(命名为 XMLExtensions)，在模块中添加一个字符串常量(命名为 kXSLTFormat)，并将上面的 XSLT 复制到常量中。

现在将一个全局函数添加到模块中，该模块使用一个格式函数扩展 XMLDocument 类:

```
Function Format(Extends xml As XMLDocument) As String
  Return xml.Transform(kXSLTFormat)
End Function 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以调用它以字符串形式获取格式化的 XML。例如，此代码从一个文本字段中获取未格式化的 XML，并在另一个文本字段中显示格式化的 XML:

```
Dim xml As New XMLDocument
xml.LoadXml(TextArea1.Text)
TextArea2.Text = xml.Format 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/9afb3a9af53c881509b2f37d2898d125.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--waj1FsV4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.xojo.com/wp-content/uploads/2018/08/2018-08-17_15-45-28.png)