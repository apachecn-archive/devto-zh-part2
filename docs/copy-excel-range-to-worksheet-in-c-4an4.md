# 在 C++中将 Excel 区域复制到工作表

> 原文：<https://dev.to/asposecells/copy-excel-range-to-worksheet-in-c-4an4>

Microsoft Excel 区域可以包含一个或多个单元格。它有一个起始单元和一个结束单元。位于起始单元和结束单元内的所有单元构成一个范围。有时，用户希望将单元格区域复制到同一工作表中的其他位置，或者复制到不同工作表中的其他位置。

本文的目的是解释 C++开发人员或程序员如何使用 [Aspose。应用程序中的单元格](https://products.aspose.com/cells/cpp)将 Excel 范围复制到相同的工作表或不同的工作表。

> [Aspose。Cells](https://products.aspose.com/cells/) API 支持多个平台，例如。NET，Java，C++，Android，JavaScript，PHP 等等。此外，[阿斯波斯。Cells 在云中作为 RESTful API](https://products.aspose.cloud/cells)提供。

# 将 Excel 范围复制到同一工作表

下面的 C++示例代码将示例 Excel 文件加载到 Aspose 中。单元格工作簿对象。然后，它访问第一个工作表，该工作表包含我们要复制到同一工作表的源 Excel 区域。然后使用 ***范围。*** 【复制()】方法将其复制到工作表内的新位置。