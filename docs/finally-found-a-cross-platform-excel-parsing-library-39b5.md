# 终于找到了跨平台的 Excel 解析库

> 原文：<https://dev.to/joaofbantunes/finally-found-a-cross-platform-excel-parsing-library-39b5>

## [T1】简介](#intro)

我知道，我知道，我一直生活在岩石下！ExcelDataReader 图书馆一直都在那里，但只是最近我才发现它。我发现它与其他产品的不同之处在于它是跨平台的，并且不需要安装一些奇怪的外部依赖。

## 入门

要开始使用这个库，我们只需要添加所需的 NuGet 包。

*   `dotnet add package ExcelDataReader`对于具有较低级别 API 的基础包
*   `dotnet add package ExcelDataReader.DataSet`对于一些使用起来更简单的扩展
*   `dotnet add package System.Text.Encoding.CodePages`，磨合时需要。NET Core only，由于(摘自项目 GitHub 页面)“这是解析用 DOS 时代代码页编码的二进制 BIFF2-5 Excel 文档中的字符串所必需的。默认情况下，这些编码是完整注册的。NET Framework，但不在。网芯。”

## 代码

代码非常简单，至少对我的用例来说是这样，因为我不需要太花哨的东西。在我最初使用它的项目中，我们只需要从 Excel 文件中读取一些表格，这些表格提供给我们一些业务逻辑的预期结果，并在测试中断言我们的实现的正确性。这个示例来自我们的简单需求。