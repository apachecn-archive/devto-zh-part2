# 使用 Aspose.PDF 将数据导出为 PDF。网络核心 2.0

> 原文：<https://dev.to/asposepdf/export-data-to-pdf-using-asposepdf-for-net-core-20--1cc8>

由于某种原因，有些任务不使用最近流行的 HTML 到 PDF 转换方案，而将数据从数据库导出到 PDF 文档会更方便。本文将向您展示如何使用 Aspose.PDF 生成 PDF 文档。NET Core 2.0 库。

使用这个库有一些优点:

*   这是一个单一的跨平台 API。NET/。NET Standard 2.0、Java、Android 平台自带原生核心，不需要额外依赖；

*   从/到 PDF 的大量数据转换器；

*   编辑 PDF 的广泛功能和附加功能，如签名、加密、文本提取等；

*   为了演示该解决方案，标准模板“ASP。NET 核心 Web 应用/ Web 应用(模型-视图-控制器)”被使用。

## 用 Aspose.PDF 生成 PDF 的基础知识

在 Aspose.PDF 最重要的类之一是文档类。这个类是一个 PDF 渲染引擎。为了呈现 PDF 结构，Aspose.PDF 图书馆使用了文档页面模型，其中:

*   文档-包含 PDF 文档的属性，包括页面集合；
*   Page -包含特定页面的属性以及与该页面相关的各种元素集合。

因此，要使用 Aspose.PDF 创建 PDF 文档，您应该按照以下步骤操作:

1.  创建文档对象；
2.  为文档对象添加页面(页面对象)；
3.  创建放置在页面上的对象(例如，文本片段、表格等。)
4.  将创建的项目添加到页面上的相应集合中(在我们的例子中，它将是一个段落集合)；
5.  将文档保存为 PDF 文件。