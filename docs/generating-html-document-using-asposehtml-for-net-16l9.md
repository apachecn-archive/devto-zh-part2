# 使用 Aspose.HTML 生成 HTML 文档。网

> 原文：<https://dev.to/andruhovski/generating-html-document-using-asposehtml-for-net-16l9>

有时你会遇到不得不用 C#代码生成 HTML 的情况。当然，最简单的方法是合并字符串并从方法返回结果。这不是最好的解决方案，但对于一些短字符串来说是好的。

如果你有一个更复杂的 HTML 结构，代码会因为许多字符串格式和合并而变得不受支持。

尽管这是最糟糕的方法，许多开发人员采用这种方法，但这意味着“它现在可以工作，但将来不会得到支持”。

在这篇文章中，我想展示如何使用 Aspose.HTML 为. NET 生成一个 HTML

# 简介

Aspose.HTML 为。NET 既是解析器又是页面呈现器。作为解析器，它提供对 W3C 标准文档对象模型的访问。所以如果熟悉 DOM，你可以很容易地用 Aspose.HTML 生成一个 HTML 文档

Aspose.HTML 图书馆有一个 [**HTMLDocument**](https://apireference.aspose.com/net/html/aspose.html/htmldocument) 类。这个类是 HTML 层次结构的一个根，包含了所有的内容。

该类包含几个方法和属性，可以帮助我们创建一个简单的页面:

**方法**

*   [AppendChild](https://apireference.aspose.com/net/html/aspose.html.dom/node/methods/appendchild) -将节点 newChild 添加到该节点的子节点列表的末尾。如果 newChild 已经在树中，那么它被移除；

*   创建一个指定类型的元素。注意返回的实例实现了元素接口，所以可以直接在返回的对象上指定属性；

**属性**

*   [ParentElement](https://apireference.aspose.com/net/html/aspose.html.dom/node/properties/parentelement) -获取该节点的父元素；

*   [Body](https://apireference.aspose.com/net/html/aspose.html/htmldocument/properties/body) -保存文档内容的元素；

*   [ChildNodes](https://apireference.aspose.com/net/html/aspose.html.dom/document/methods/createelement) -包含该节点所有子节点的节点列表。如果没有子节点，这是一个不包含任何节点的节点列表。

上述方法和属性使用了一个[节点列表](https://apireference.aspose.com/net/html/aspose.html.collections/nodelist)。NodeList 提供了有序节点集合的抽象，而没有定义或约束如何实现这个集合。在实际应用中，NodeList 表示 DOM 元素的列表，并且依赖于查询类型。

## 创建简单的 HTML 文档

让我们尝试创建一个简单的文档，它包含一个图像、一个有序列表和一个 3x3 的表。我们将在本演示中使用 Visual Studio 2017:

### 第一步:在 Visual Studio 中创建项目

打开 Visual Studio，选择**文件** > > **新建** > > **项目菜单。**

在**新建项目**中，您会在左侧模板列表中看到**已安装的模板**。在左侧，您可以选择一种语言- Visual C#。我选择了**Visual c#**->-**Windows 经典桌面** - > **控制台 App(。NET 框架)。**

### 第二步:添加 Aspose.Html 库

进入**工具- >获取软件包管理器- >软件包管理器控制台**。运行命令:`Install-Package Aspose.Html`

### 第三步:创建一个空的 html 文档

让我们尝试创建一个空的 HTML 文档，并将其存储到本地文件`c:\apsdemo\files\demo1.html`。您需要为演示文件创建一个文件夹。实现这一点的方法之一是在软件包管理器控制台中运行命令:`New-Item -ItemType Directory C:\asposedemo\files\`

将以下文本添加到 Main 方法中: