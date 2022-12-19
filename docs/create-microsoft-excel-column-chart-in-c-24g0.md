# 用 C++创建 Microsoft Excel 柱形图

> 原文：<https://dev.to/asposecells/create-microsoft-excel-column-chart-in-c-24g0>

图表是数据的图形表示，使用户能够轻松理解大量数据以及数据各部分之间的关系。Microsoft Excel 中有许多类型的图表可用，并且几乎所有的图表都受 [Aspose 支持。](https://products.aspose.com/cells/cpp)单元格为 C++。事实上，是一个。几乎可以在任何平台上使用 Cells 来创建、编辑和操作 Excel 电子表格，而无需安装 Microsoft Excel 或使用任何类型的 Microsoft Office automation。

**文章描述**

> 本文的目的是解释开发人员如何用 C++创建 Microsoft Excel 柱形图。

**支持的平台**

> [Aspose。Cells](https://products.aspose.com/cells) API 支持多个平台，如 C++、。NET，Java，Android，JavaScript，PHP 等等。此外，[阿斯波斯。Cells 在云中作为 REST 或 RESTful APIs 可用](https://products.aspose.cloud/cells)。

# 柱形图的类型

柱形图有多种类型，下面列出了其中一些。

*   圆柱
*   柱状堆叠
*   百分比堆积柱形图
*   3D 列
*   三维聚集列
*   三维堆叠柱形图
*   三维百分比堆积柱形图

# 样本输入微软 Excel 文档

为了演示，我们将使用下面的[样本输入包含图表数据的 Microsoft Excel 文档](https://github.com/AsposeCells/AsposeCells-Screenshots-and-Sample-Files/blob/master/Create-Microsoft-Excel-Column-Chart/sampleCreateMicrosoftExcelColumnChart.xlsx)。这里，列 A 包含分类轴数据，其他列 B、C 和 D 包含图表系列数据。

[![Sample Input Microsoft Excel Document containing Chart Data.](../Images/56bfd89a6db5014d7247460d8d34aa10.png "Sample Input Microsoft Excel Document containing Chart Data.")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--X2VWa5or--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/AsposeCells/AsposeCells-Screenshots-and-Sample-Files/master/Create-Microsoft-Excel-Column-Chart/Input-Microsoft-Excel-Document-containing-Chart-Data.png)

# 样本代码

以下示例代码通过执行以下步骤创建 Microsoft Excel 柱形图。

*   加载[样本输入包含图表数据的 Microsoft Excel 文档](https://github.com/AsposeCells/AsposeCells-Screenshots-and-Sample-Files/blob/master/Create-Microsoft-Excel-Column-Chart/sampleCreateMicrosoftExcelColumnChart.xlsx)。
*   创建具有指定维度的柱形图。
*   设置图表标题并格式化。
*   添加三个垂直系列，设置它们的名称和填充颜色。
*   格式化各种图表项目，例如绘图区、数值轴、分类轴、主要刻度线等。
*   以 XLSX 格式保存工作簿。您也可以保存为其他格式，如 XLS，XLSB，XLSM 等。