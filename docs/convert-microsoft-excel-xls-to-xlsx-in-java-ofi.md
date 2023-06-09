# 用 Java 将 Microsoft Excel XLS 转换为 XLSX

> 原文：<https://dev.to/asposecells/convert-microsoft-excel-xls-to-xlsx-in-java-ofi>

XLS 是一种古老但广泛使用的 Microsoft Excel 格式。这是一种二进制文件格式，称为*二进制交换文件格式(BIFF)* 。为了向后兼容，Microsoft Excel 仍然支持 XLS 格式，但由于这是一种旧格式，它不支持许多新功能。因此，XLS 格式经常被转换为 XLSX 格式，这是一种较新的 Open Office 基于 XML 的格式，支持 Microsoft Excel 的所有新功能。

您可以使用 Microsoft Excel 2007 或更高版本(如 Microsoft Excel 2016 等)手动将 XLS 转换为 XLSX。或者以编程方式使用 [Aspose。Cells for Java](https://products.aspose.com/cells/java) API，只有几行代码。为了完成它的操作，一只蜘蛛。Cells 不依赖于 Microsoft Excel 或任何种类的 Microsoft Office Automation、VBA(*Visual Basic for Applications*)、VSTO(*Visual Studio Tools for Office*)等。

**文章描述**

> 本文的目的是解释开发人员如何用 Java 将 Microsoft Excel XLS 转换成 XLSX 格式。

**支持的平台**

> [Aspose。Cells](https://products.aspose.com/cells/) API 支持包括 Java、.NET，C++，Android，JavaScript，PHP 等等。此外，[阿斯波斯。Cells 在云中作为 REST 或 RESTful APIs 可用](https://products.aspose.cloud/cells)。

# XLS 和 XLSX 中的最大列数和行数

XLS 格式支持

*   65，536 行。
*   256 列。

XLSX 格式支持

*   1048576 行。
*   16384 列。

# 样本输入微软 Excel XLS 文档

您可以使用 Aspose 将任何 XLS 文档转换为 XLSX。细胞 API。为了说明，我们将使用下面的[样本输入 Microsoft Excel XLS 文档](https://github.com/AsposeCells/AsposeCells-Screenshots-and-Sample-Files/blob/master/Convert-Microsoft-Excel-XLS-to-XLSX/SampleConvertMicrosoftExcelXLSToXLSX.xls)，其中包含一些公司的文本和数字格式的数据。无论何时，你打开微软 Excel 中的 XLS 文档，都会显示出*兼容模式*，如下图*红箭*所示。

[![Sample Microsoft Excel XLS document to be converted to XLSX format using Aspose.Cells API.](img/fadcbcd7bb3b8717547f805002e6ea6f.png "Sample Microsoft Excel XLS document to be converted to XLSX format using Aspose.Cells API.")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Icr5Rl4P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/AsposeCells/AsposeCells-Screenshots-and-Sample-Files/master/Convert-Microsoft-Excel-XLS-to-XLSX/Input-Convert-XLS-to-XLSX-using-Aspose.Cells-API.png)

# 样本代码

以下示例代码通过执行以下步骤将 XLS 转换为 XLSX。

*   将输入的 Microsoft Excel XLS 文档加载到*com . aspose . cells . workbook*对象中。
*   以 XLSX 格式保存*com . aspose . cells . workbook*对象。