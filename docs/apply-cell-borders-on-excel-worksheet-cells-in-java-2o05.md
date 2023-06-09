# 在 Java 中对 Excel 工作表单元格应用单元格边框

> 原文：<https://dev.to/asposecells/apply-cell-borders-on-excel-worksheet-cells-in-java-2o05>

Microsoft Excel 提供了各种类型的内置边框，可应用于工作表中的单元格或单元格区域。Excel 单元格边框由线条样式和颜色两部分组成。因为 Microsoft Excel 中有许多可用的线条样式，所以用户也可以创建自定义边框。 [Aspose。Cells for Java](https://products.aspose.com/cells/java) API 可用于创建任何类型的边框，无论是内置边框还是轻松定制的边框。此外，它可以用来创建，编辑和操纵 Excel 电子表格几乎在任何平台上，没有任何需要安装微软 Excel 或没有使用任何种类的微软办公自动化。

**文章描述**

> 本文的目的是解释开发人员如何在 Java 中对 Excel 工作表单元格应用单元格边框。

**支持的平台**

> [Aspose。Cells](https://products.aspose.com/cells/) API 支持包括 Java、.NET，C++，Android，JavaScript，PHP 等等。此外，[阿斯波斯。Cells 在云中作为 REST 或 RESTful APIs 提供。](https://products.aspose.cloud/cells)

# 内置边框的类型

以下是 Microsoft Excel 中可应用于单元格的内置边框类型。

*   顶端
*   底部
*   左边的
*   对吧
*   对角线向上
*   对角线向下

# 单元格边框类型创建自定义边框

通过更改线条样式，您可以创建自定义边框。下面提到了其中的一些线条样式。另请参见下面给出的在 Microsoft Excel 边框 GUI 中显示线条样式的快照。

*   没有人
*   中等
*   头发
*   厚的
*   薄的
*   虚线
*   有点的
*   虚线

> ***标题:**Microsoft Excel 中的边框线条样式，用 Aspose 中的 CellBorderType 表示。细胞 API。*

[![Border Line Styles in Microsoft Excel represented by CellBorderType in Aspose.Cells API.](img/f036c3447fc2caace5480e05d314c17f.png "Border Line Styles in Microsoft Excel represented by CellBorderType in Aspose.Cells API.")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8kIccGhF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/AsposeCells/AsposeCells-Screenshots-and-Sample-Files/master/Apply-Cell-Borders-on-Excel-Worksheet-Cells/Border-Line-Styles-Microsoft-Excel-Border-Aspose.Cells.png)

# 使用 Aspose 设置单元格边框。单元格 API

使用 *BorderType* 枚举可以访问所有的边框类型。其值如下

*   BorderType。顶部 _ 边框
*   BorderType。底部边框
*   BorderType。左边框
*   BorderType。右边框
*   BorderType。对角线 _ 向上
*   BorderType。对角线 _ 向下

下面的代码访问顶部边框并设置其线条样式和颜色。类似地，您可以使用 *BorderType* 枚举来处理任何边框。

```
 // Set Top Border of Cell. Access cell object.
Cell cell = ws.Cells["B6"];

// Access cell style.
Style style = cell.GetStyle();

// Access top border.
Border topBorder = style.Borders[BorderType.TopBorder];

// Set the line style of the top border.
topBorder.LineStyle = CellBorderType.Thick;

// Set the color of the top border.
topBorder.Color = Color.Red;

// Set the cell style.
cell.SetStyle(style); 
```

# 样本代码

下面的示例代码通过执行以下步骤对 Excel 工作表单元格应用单元格边框。

*   创建空工作簿并设置其默认样式。
*   添加数值并格式化单元格 B3、B6、B9、D3、D6、D9 和 F6。还要给它们设置边框。
*   设置左边框。
*   设置右边框。
*   设置上边框。
*   设置下边框。
*   设置对角线向上边框。
*   设置对角线下边框。
*   设置所有边框。
*   以 XLSX 格式保存工作簿。您也可以保存为其他格式，如 XLS，XLSB，XLSM 等。