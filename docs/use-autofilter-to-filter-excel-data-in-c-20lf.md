# 在 C#中使用自动筛选来筛选 Excel 数据。网

> 原文：<https://dev.to/asposecells/use-autofilter-to-filter-excel-data-in-c-20lf>

根据标准过滤数据是一个非常重要的特性。它有助于用户轻松理解和分析数据。您可以使用 Microsoft Excel 的自动筛选功能，根据从列表中选择的选项来查找、显示或隐藏一列或多列中的值。应用过滤器后，所有不符合条件的行将完全隐藏。

你可以用[来表示。的单元格。NET](https://products.aspose.com/cells/net) 用几行代码就可以用 C#轻松地对 Excel 数据进行过滤。它还可以用于对 Excel 文档执行各种功能，例如，您可以在任何平台上创建、编辑和操作 Excel 电子表格，而无需安装 Microsoft Excel 或使用任何类型的 Microsoft Office automation。

**文章描述**

> 本文的目的是解释开发人员如何在 C#或任何。NET 框架支持的语言，如 VB.NET 等。

**支持的平台**

> [Aspose。Cells](https://products.aspose.com/cells/) API 支持所有。NET 框架，例如。NET 2.0，。NET 3.5，。NET 4.0，。NET 7.0，。网芯，。NET Standard 2.0、Xamarin 等。它也可用于其他平台，如 Java、C++、Android、JavaScript、PHP 等。此外，[阿斯波斯。Cells 在云中作为 REST 或 RESTful APIs 提供。](https://products.aspose.cloud/cells)

# 在 Microsoft Excel 中使用自动筛选功能筛选数据

请按照下列步骤在 Microsoft Excel 中使用“自动筛选”功能筛选数据。

*   选择列，点击*排序&筛选*部分内的*数据>筛选*按钮。
*   单击自动筛选下拉列表，从列表中选择您的选项，然后按确定。
*   所有不符合条件的行将被过滤掉。详情请看这张快照。

[![Apply AutoFilter in Microsoft Excel which can also be done with Aspose.Cells API programmatically.](../Images/d5a5f38b2f98fa4cac02c35a042aaa5c.png "Apply AutoFilter in Microsoft Excel which can also be done with Aspose.Cells API programmatically.")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GOWeGObS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/AsposeCells/AsposeCells-Screenshots-and-Sample-Files/master/Use-AutoFilter-to-Filter-Excel-Data/Apply-AutoFilter-Microsoft-Excel-Aspose.Cells-API.png)

# 在 Aspose 中使用自动筛选功能筛选数据。细胞

本节说明如何使用带有 Aspose 的自动筛选来筛选 Excel 数据。细胞 API。

# 样本输入微软 Excel 文档

为了演示，我们将使用下面的[样本输入 Microsoft Excel 文档](https://github.com/AsposeCells/AsposeCells-Screenshots-and-Sample-Files/blob/master/Use-AutoFilter-to-Filter-Excel-Data/sampleUseAutoFilterToFilterExcelData.xlsx)，它包含四列中的一些数据。我们将对*车辆*和*颜色*列应用自动筛选。一旦行被过滤掉，其中一些行将被隐藏，红线内显示的 *Qty1* 和 *Qty2* 列的**总计**将被相应修改。

[![Sample Input Microsoft Excel Document containing Data for applying AutoFilter using Aspose.Cells API.](../Images/dde316c30ad5c588d0c882a7e19639fa.png "Sample Input Microsoft Excel Document containing Data for applying AutoFilter using Aspose.Cells API.")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tM0W4FR5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/AsposeCells/AsposeCells-Screenshots-and-Sample-Files/master/Use-AutoFilter-to-Filter-Excel-Data/Sample-Microsoft-Excel-Apply-AutoFilter-Aspose.Cells-API.png)

# 样本代码

以下示例代码通过执行以下步骤对 Microsoft Excel 数据应用自动筛选

*   加载[样本输入 Microsoft Excel 文档](https://github.com/AsposeCells/AsposeCells-Screenshots-and-Sample-Files/blob/master/Use-AutoFilter-to-Filter-Excel-Data/sampleUseAutoFilterToFilterExcelData.xlsx)，其中包含自动过滤的样本数据。
*   对范围应用自动筛选。
*   向第一列添加两个筛选器。
*   刷新自动过滤器。
*   向第二列添加另外两个过滤器。
*   刷新自动过滤器。
*   以 XLSX 格式保存工作簿。您也可以保存为其他格式，如 XLS，XLSB，XLSM 等。