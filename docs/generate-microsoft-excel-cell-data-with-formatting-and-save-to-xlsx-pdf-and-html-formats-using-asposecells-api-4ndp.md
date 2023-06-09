# 使用 Aspose 生成带格式的 Microsoft Excel 单元格数据并保存为 XLSX、PDF 和 HTML 格式。单元格 API

> 原文：<https://dev.to/asposecells/generate-microsoft-excel-cell-data-with-formatting-and-save-to-xlsx-pdf-and-html-formats-using-asposecells-api-4ndp>

Microsoft Excel 是一个强大的图形用户界面工具，用于创建和操作电子表格，分析图表和数据透视表形式的数字数据，以及进行复杂的公式计算。但是当涉及到用一些服务器端语言编程和生成 Microsoft Excel 文档时，选择非常有限。其中一个选项是 Aspose.Cells

[Aspose。的单元格。NET](https://products.aspose.com/cells/net) API 是一个不需要 Microsoft Excel 就可以创建和操作 Excel 文档的库。这意味着，您不需要安装 Microsoft Office 或 Excel 来创建 Excel 文档。它适用于桌面和服务器应用程序。它支持微软 Excel 的几乎所有功能，例如图表、数据透视表、公式计算、条件格式、表格等。

**支持的平台**

> [Aspose。Cells](https://products.aspose.com/cells/) API 支持所有。NET 框架，例如。NET 2.0，。NET 3.5，。NET 4.0，。NET 7.0，。网芯，。NET Standard 2.0、Xamarin 等。它也可用于其他平台，如 Java、C++、Android、JavaScript、PHP 等。此外，[阿斯波斯。Cells 在云中作为 REST 或 RESTful APIs 可用](https://products.aspose.cloud/cells)。

在这篇文章中，我们将学习一个命题。通过在单元格中添加一些数据并在 C#编程中格式化它。我们将根据输入的数据创建一个简单的*柱形图*。最后，我们将把工作簿对象保存为 XLSX、PDF 和 HTML 格式。

> 请向下滚动到底部，查看完整的示例代码及其显示输出 XLSX、PDF 和 HTML 格式的屏幕截图。

# 处理工作簿、工作表和单元格

#### 创建空工作簿

当您在 Microsoft Excel 中按下 *Ctrl+N* 时，会创建一个全新的或空的工作簿。同样，下面的代码使用 Aspose 创建一个全新的空工作簿。细胞 API。

```
//Create Empty Workbook.
Workbook wb = new Workbook(); 
```

Enter fullscreen mode Exit fullscreen mode

#### 访问工作表

每当创建新工作簿时，它总是至少有一个工作表。您可以通过图纸索引或图纸名称来访问图纸。下面的代码使用工作表索引访问第一个工作表。请注意，与 Microsoft Excel 不同，Aspose 中的索引。单元 API 从 0 开始。因此，第一个工作表索引将为 0，第二个工作表索引将为 1。

```
//Access First Worksheet.
Worksheet ws = wb.Worksheets[0]; 
```

Enter fullscreen mode Exit fullscreen mode

#### 按行列索引访问单元格

如前所述，与 Microsoft Excel 不同，Aspose 中的索引。单元 API 从 0 开始。因此，如果您要访问位于第 5 行第 3 列的单元格，即 C5，您将像这样访问它。

```
//Access cell on 5th row and 3rd column i.e. cell C5
Cell cell = ws.Cells[4, 2]; 
```

Enter fullscreen mode Exit fullscreen mode

#### 按名称访问单元格

类似地，您可以通过名称访问单元格。例如，下面的代码通过名称访问单元格 C5。

```
//Access cell C5 by its name
Cell cell = ws.Cells["C5"]; 
```

Enter fullscreen mode Exit fullscreen mode

#### 在单元格中添加数据

Cell 对象有一个 PutValue()方法。它有各种重载。您可以使用它来添加不同类型的数据。例如，下面的代码解释了如何在单元格中添加文本，以及如何在单元格中添加数字。

```
//Add text in cell
cell.PutValue("Sales");

//Add number in cell
ws.Cells["B2"].PutValue(600); 
```

Enter fullscreen mode Exit fullscreen mode

# 处理单元格格式

#### 用颜色填充单元格

您可以使用 Style 对象用某种颜色填充单元格。请使用风格。图案和风格。用于此目的的前景颜色属性。为了改变单元格样式，请使用单元格。GetStyle()和单元格。SetStyle()方法。以下颜色用黄色填充单元格。

```
//Get the Style object of the Cell
Style st = cell.GetStyle();

// Set the Pattern and ForegroundCOlor properties
st.Pattern = BackgroundType.Solid;
st.ForegroundColor = Color.Yellow;

//Set the Style object of the Cell
cell.SetStyle(st); 
```

Enter fullscreen mode Exit fullscreen mode

#### 设置单元格的字体加粗

您可以使用样式使单元格的字体加粗。Font.IsBold 属性。以下代码将单元格值设置为粗体。

```
//Get the Style object of the Cell
Style st = cell.GetStyle();

//Set the Pattern and ForegroundColor properties
st.Font.IsBold = true;

//Set the Style object of the Cell
cell.SetStyle(st); 
```

Enter fullscreen mode Exit fullscreen mode

#### 设置水平和垂直对齐

可以使用 Style 对象设置单元格的水平和垂直对齐方式。请使用风格。横向对齐和样式。设置对齐方式的 VerticalAlignment 属性。下面的示例代码将单元格的水平和垂直对齐方式设置为居中。

```
//Get the Style object of the Cell
Style st = cell.GetStyle();

//Set the horizontal and vertical alignment of the cell
st.HorizontalAlignment = TextAlignmentType.Center;
st.VerticalAlignment = TextAlignmentType.Center;

//Set the Style object of the Cell
cell.SetStyle(st); 
```

Enter fullscreen mode Exit fullscreen mode

#### 设置边框的线条样式，例如上、下、左、右边框

对象可以用来设置边框的线条样式。下面的代码将上下左右边框的线条样式设置为细。

```
//Get the Style object of the Cell
Style st = cell.GetStyle();

//Set the Line Style of the Borders
st.Borders[BorderType.TopBorder].LineStyle = CellBorderType.Thin;
st.Borders[BorderType.BottomBorder].LineStyle = CellBorderType.Thin;
st.Borders[BorderType.LeftBorder].LineStyle = CellBorderType.Thin;
st.Borders[BorderType.RightBorder].LineStyle = CellBorderType.Thin;

//Set the Style object of the Cell
cell.SetStyle(st); 
```

Enter fullscreen mode Exit fullscreen mode

#### 设置单元格数值的数字格式为货币

请使用风格。Number 属性，并将其值设置为 5、6、7 或 8。这些数字是由 Microsoft Excel 定义的，它们被称为内置数字格式。下面的代码将 5 赋给 Style。数字属性。现在，如果单元格值是 100，它将被格式化为$100。

```
//Get the Style object of the Cell
Style st = cell.GetStyle();

//Set the built-in currency format
st2.Number = 5;

//Set the Style object of the Cell
cell.SetStyle(st); 
```

Enter fullscreen mode Exit fullscreen mode

#### 组合多种类型的单元格格式

以上代码都可以这样合并。例如，下面给出的代码做了以下事情。

1 -用黄色填充单元格
2 -将单元格的字体设置为粗体
3 -将单元格的水平和垂直对齐设置为居中
4 -将边框的线条样式(例如上、下、左、右边框)设置为细
5 -将单元格值的数字格式设置为货币

```
//Get the Style object of the Cell
Style st = cell.GetStyle();

//Set the Pattern and ForegroundColor properties
st.Pattern = BackgroundType.Solid;
st.ForegroundColor = Color.Yellow;

//Set the font bold
st.Font.IsBold = true;

//Set the horizontal and vertical alignment
st.HorizontalAlignment = TextAlignmentType.Center;
st.VerticalAlignment = TextAlignmentType.Center;

//Set the borders
st.Borders[BorderType.TopBorder].LineStyle = CellBorderType.Thin;
st.Borders[BorderType.BottomBorder].LineStyle = CellBorderType.Thin;
st.Borders[BorderType.LeftBorder].LineStyle = CellBorderType.Thin;
st.Borders[BorderType.RightBorder].LineStyle = CellBorderType.Thin;

//Set the number style to currency
st2.Number = 5;

//Set the Style object of the Cell
cell.SetStyle(st); 
```

Enter fullscreen mode Exit fullscreen mode

#### 使用 Range 对象将单元格格式应用于整个单元格范围

当您想要将单元格格式应用于大量单元格时，则不应该使用单元格。GetStyle()和单元格。SetStyle()方法。相反，你应该使用范围。ApplyStyle()方法。该方法采用两个参数，即 Style 和 StyleFlag。下面的代码解释了如何将内置的货币数字格式应用于范围 B2:C5。

```
//Set the Currency format of Range
Range rng2 = ws.Cells.CreateRange("B2:C5");
StyleFlag flag2 = new StyleFlag();
flag2.NumberFormat = true;
Style st2 = wb.CreateStyle();
st2.Number = 5;
rng2.ApplyStyle(st2, flag2); 
```

Enter fullscreen mode Exit fullscreen mode

# 处理图表

#### 在工作表中添加柱形图

以下代码在带有 ChartCollection 的工作表中添加柱形图。Add()方法。

```
//Add Column Chart.
int idx = ws.Charts.Add(ChartType.Column, 10, 1, 30, 8);

//Access Chart.
Chart ch = ws.Charts[idx]; 
```

Enter fullscreen mode Exit fullscreen mode

#### 在图表中添加垂直系列

下面两行在图表中添加两个垂直系列。

```
//Add Two Vertical Series
ch.NSeries.Add("B2:B5", true);
ch.NSeries.Add("C2:C5", true); 
```

Enter fullscreen mode Exit fullscreen mode

#### 设置图表系列的分类数据

下面一行设置类别数据，即图表系列的 X 轴。

```
//Set the Category Data.
ch.NSeries.CategoryData = "A2:A5"; 
```

Enter fullscreen mode Exit fullscreen mode

#### 设置图表系列的名称

这就是通过将单元格名称指定为 series 来设置图表系列名称的方法。名称属性。

```
//Set the Series Names.
ch.NSeries[0].Name = "=B1";
ch.NSeries[1].Name = "=C1"; 
```

Enter fullscreen mode Exit fullscreen mode

#### 设置图表标题

请使用图表。设置图表标题的文本属性。

```
//Set the Chart Title.
ch.Title.Text = "Sales and Expenses by Months"; 
```

Enter fullscreen mode Exit fullscreen mode

#### 计算图表项目

一旦你创建了你的图表，请调用图表。Calculate()方法。只有当您还想创建 PDF 或 HTML 格式时，才需要此方法。但是如果你只想保存为 Excel 格式，那么这个方法就没有必要了，因为 Microsoft Excel 会在打开时自动计算图表项。

```
//Calculate Chart Items.
ch.Calculate(); 
```

Enter fullscreen mode Exit fullscreen mode

# 以各种格式保存工作簿

#### 以 XLSX 格式保存工作簿

请使用保存格式。Xlsx 以 Xlsx 格式保存工作簿。

```
//Save Workbook in Xlsx format.
String dirPath = "D:\\Download\\"; 
wb.Save(dirPath + "output.xlsx", SaveFormat.Xlsx); 
```

Enter fullscreen mode Exit fullscreen mode

#### 以 PDF 格式保存工作簿

请使用 SaveFormat.Pdf 将工作簿保存为 PDF 格式。

```
//Save Workbook in PDF format.
wb.Save(dirPath + "output.pdf", SaveFormat.Pdf); 
```

Enter fullscreen mode Exit fullscreen mode

#### 以 HTML 格式保存工作簿

请使用 SaveFormat.Html 将工作簿保存为 PDF 格式。

```
//Save Workbook in HTML format. 
wb.Save(dirPath + "output.html", SaveFormat.Html); 
```

Enter fullscreen mode Exit fullscreen mode

# 完整样本代码

理解了上面所有的概念和 API 之后，你就可以轻松理解下面的整个代码了。请阅读代码中的注释以获得更多帮助。