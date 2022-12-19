# 使用 C#生成 PDF 文件

> 原文：<https://dev.to/asposepdf/generating-pdf-file-using-c-loj>

在这篇文章中，我想分享一些我使用 Aspose.PDF 生成 PDF 文档的经验。NET 并介绍基本呈现技术。

作为一个例子，我选择了一个经常使用的文档——发票。我们的文件将由 5 部分组成:

*   标题部分-包含公司徽标和一般信息；
*   地址部分-包含关于卖方和客户的信息；
*   网格部分-包含产品项目表和总计子部分；
*   terms 部分-包含附加的字符串列表；
*   页脚-可选部分，在页面底部放置一个字符串。

[![The invoice layout](../Images/172ca47404b77aa3eeb52fbaa2db8080.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yurGaPcJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xgjs7mpkeynb6atjmas1.jpg)

*免责声明:*请注意，Apose.PDF 是一个付费产品，但试用版完全适合做一些实验，如创建一个长达 4 页的文档。

**第 0 步**。创建文档的模型。为了方便工作，我们将创建几个类来描述发票细节。

类`LogoImage`将用于在左上角渲染公司标志。

```
namespace Aspose.PDF.Invoicer
{
    public class LogoImage
    {
        public string FileName;
        public int Width;
        public int Height;

        public LogoImage(string filename, int width, int height)
        {
            FileName = filename;
            Width = width;
            Height = height;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

类`TotalRow`将用于呈现 totals 子部分中的行。

```
namespace Aspose.PDF.Invoicer
{
    public class TotalRow
    {
        public string Text;
        public decimal Value;

        public TotalRow(string text, decimal value)
        {
            Text = text;
            Value = value;
        }                
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

类别`ProductItem`表示网格部分中的一个产品项目。

```
namespace Aspose.PDF.Invoicer
{
    public class ProductItem
    {
        public string Id;
        public string Name;
        public decimal Price;
        public int Quantity;
        public decimal Total => Price * Quantity;

        public ProductItem(string id, string name, decimal price, int quantity)
        {
            Id = id;
            Name = name;
            Price = price;
            Quantity = quantity;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

最后一个类将用于表示一个 PDF 文档。

```
using System;
using System.Collections.Generic;
using System.IO;
using Aspose.Pdf;
using Aspose.Pdf.Text;

namespace Aspose.PDF.Invoicer
{

    public class Invoice: IDisposable
    {
        #region Private memebers
        private static readonly License Licence = new License();
        private Color _textColor, _backColor;
        private readonly Font _timeNewRomanFont;
        private readonly TextBuilder _builder;
        private readonly Page _pdfPage;
        private readonly Document _pdfDocument;
        private readonly Rectangle _logoPlaceHolder;        
        #endregion

        public string ForegroundColor
        {
            get { return _textColor.ToString(); }
            set { _textColor = Color.Parse(value); }
        }
        public string BackgroundColor
        {
            get { return _backColor.ToString(); }
            set { _backColor = Color.Parse(value); }
        }

        //Invoice details
        public string Number;
        public uint PaymentPeriod = 14;
        public LogoImage Logo;
        public List<string> BillFrom;
        public List<string> BillTo;
        public List<ProductItem> Items;
        public List<TotalRow> Totals;
        public List<string> Details;
        public string Footer;

        public Invoice()
        {
            _pdfDocument = new Document();
            _pdfDocument.PageInfo.Margin.Left = 36;
            _pdfDocument.PageInfo.Margin.Right = 36;
            _pdfPage = _pdfDocument.Pages.Add();
            _textColor = Color.Black;
            _backColor = Color.Transparent;
            _logoPlaceHolder = new Rectangle(20, 700, 120, 800);
            _timeNewRomanFont = FontRepository.FindFont("Times New Roman");
            _builder = new TextBuilder(_pdfPage);
        }
        public void Save(Stream stream)
        {
            HeaderSection();
            AddressSection();
            GridSection();
            TermsSection();
            FooterSection();

            _pdfDocument.Save(stream);
        }
        private void HeaderSection()
        {
            // TODO: Generate header section
        }
        private void AddressSection()
        {
            // TODO: Generate Address section
        }
        private void GridSection()
        {
            // TODO: Generate Grid section
        }
        private void TermsSection()
        {
            // TODO: Generate Terms section
        }
        private void FooterSection()
        {
            // TODO: Generate Footer section
        }
        #region IDisposable Support
        private bool disposedValue = false; // To detect redundant calls

        protected virtual void Dispose(bool disposing)
        {
            if (!disposedValue)
            {
                if (disposing)
                {                    
                    _pdfPage.Dispose();
                    _pdfDocument.Dispose();
                }
                disposedValue = true;
            }
        }

        public void Dispose()
        {            
            Dispose(true);           
        }
        #endregion
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

第一步。编写一个简单的测试应用程序。在这个控制台应用程序中，我们创建了一张发票，用`C:\aspose\company-logo-design.png`作为徽标图像、3 个产品项目、一些注释和一个演示页脚。

```
using System.Collections.Generic;
using System.IO;
using System.Linq;
using Aspose.PDF.Invoicer;

namespace ConsoleDemo
{
    class Program
    {
        static void Main()
        {
            var productItems = new List<ProductItem> {
                new ProductItem ("1", "Chocolate with Milk", 166.66M, 20),
                new ProductItem ("2", "Chocolate with Nuts", 166.66M, 20),
                new ProductItem ("3", "Chocolate with Pepper", 166.66M, 20)
            };
            var subTotal = productItems.Sum(i => i.Total);
            var invoice = new Invoice
            {
                ForegroundColor = "#0000CC",
                BackgroundColor = "#FFFFFF",
                Number = "ABC-123",
                Logo = new LogoImage(@"C:\aspose\company-logo-design.png", 160, 120),
                BillFrom = new List<string> { "Eastern Chocolate Factory", "Eastern Chocolate House", "44 Shirley Ave.", "West Chicago", "IL 60185" },
                BillTo = new List<string> { "Eastern Chocolate Cafe", "Eastern Chocolate House", "44 Shirley Ave.", "West Chicago", "IL 60185" },
                Items = productItems,
                Totals = new List<TotalRow>
                {
                    new TotalRow("Sub Total", subTotal),
                    new TotalRow("VAT @ 20%", subTotal*0.2M),
                    new TotalRow("Total", subTotal*1.2M)
                },
                Details = new List<string> {
                    "Terms & Conditions",
                    "Payment is due within 15 days",
                    string.Empty,
                    "If you have any questions concerning this invoice, contact our sales department at sales@east-chocolate.factory",
                    "", "Thank you for your business."
                },
                Footer = "https://www.facebook.com/AsposePDF/"
            };

            var fileStream = new FileStream(@"C:\\aspose\\invoice.pdf", FileMode.OpenOrCreate);
            invoice.Save(fileStream);
            fileStream.Close();

        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

**第二步**。呈现标题部分。Aspose.PDF 库提供了几种在文档中放置元素的方法。用最少的设置放置元素序列的最简单方法是使用文档对象模型。根据这个概念，文档对象包含页面对象的集合。这个集合又包含一个 [BaseParagraph](https://apireference.aspose.com/net/pdf/aspose.pdf/baseparagraph) 对象的集合。BaseParagraph 的后代是 [TextFragment](https://apireference.aspose.com/net/pdf/aspose.pdf.text/textfragment) 、[浮动框](https://apireference.aspose.com/net/pdf/aspose.pdf/floatingbox)、[表格](https://apireference.aspose.com/net/pdf/aspose.pdf/table)等。
我们将使用 TextFragment 对象来放置行“Invoice”、“DATE:”和“DUE DATE”。第一行居中，其余行右对齐。徽标图像打破了 DOM 元素的层次结构，必须放在左上角，因此要放置徽标，我们将应用另一种技术——向页面添加资源。这篇文章的目的不是详细解释如何使用资源。我们将在以后的文章中回到这个技巧。简而言之，我们完成以下步骤:

*   将图像添加到页面资源的图像集合中
*   保存当前图形状态
*   定义一个矩阵对象，用于将图像放置在适当的位置
*   将矩阵应用于图像
*   恢复图形状态

下面的代码片段包含了`HeaderSection()`方法的完整实现。

```
private void HeaderSection()
{
    var lines = new TextFragment[3];
    // Create text fragment
    lines[0] = new TextFragment($"INVOICE #{Number}");
    lines[0].TextState.FontSize = 20;
    lines[0].TextState.ForegroundColor = _textColor;
    lines[0].HorizontalAlignment = HorizontalAlignment.Center;

    _pdfPage.Paragraphs.Add(lines[0]);

    lines[1] = new TextFragment($"DATE: {DateTime.Today:MM/dd/yyyy}");
    lines[2] = new TextFragment($"DUE DATE: {DateTime.Today.AddDays(PaymentPeriod):MM/dd/yyyy}");
    for (var i = 1; i < lines.Length; i++)
    {
        // Set text properties 
        lines[i].TextState.Font = _timeNewRomanFont;
        lines[i].TextState.FontSize = 12;
        lines[i].HorizontalAlignment = HorizontalAlignment.Right;

        // Add fragment to paragraph
        _pdfPage.Paragraphs.Add(lines[i]);
    }
    // Logo 
    // Set coordinates
    _logoPlaceHolder.URX = _logoPlaceHolder.LLX + Logo.Width;
    _logoPlaceHolder.URY = _logoPlaceHolder.LLY + Logo.Height;

    // Load image into stream
    var imageStream = new FileStream(Logo.FileName, FileMode.Open);
    // Add image to Images collection of Page Resources
    _pdfPage.Resources.Images.Add(imageStream);
    // Using GSave operator: this operator saves current graphics state
    _pdfPage.Contents.Add(new Operator.GSave());
    // Create Rectangle and Matrix objects

    var matrix = new Matrix(new[] { _logoPlaceHolder.URX - _logoPlaceHolder.LLX, 0, 0,
        _logoPlaceHolder.URY - _logoPlaceHolder.LLY,
        _logoPlaceHolder.LLX, _logoPlaceHolder.LLY });
    // Using ConcatenateMatrix (concatenate matrix) operator: defines how image must be placed
    _pdfPage.Contents.Add(new Operator.ConcatenateMatrix(matrix));
    var ximage = _pdfPage.Resources.Images[_pdfPage.Resources.Images.Count];
    // Using Do operator: this operator draws image
    _pdfPage.Contents.Add(new Operator.Do(ximage.Name));
    // Using GRestore operator: this operator restores graphics state
    _pdfPage.Contents.Add(new Operator.GRestore());
} 
```

Enter fullscreen mode Exit fullscreen mode

**第三步**。呈现地址部分。我们应用镜像对齐的两列布局:左列左对齐，右列右对齐。这样的片段可以用[浮动框](https://apireference.aspose.com/net/pdf/aspose.pdf/floatingbox)类轻松渲染。要执行此布局，我们需要完成 4 个步骤:

1.  创建一个 FloatingBox 类的对象；
2.  将文本片段添加到左栏；
3.  向右侧列添加 1 个文本片段，属性为`IsFirstParagraphInColumn=true`和`HorizontalAlignment = HorizontalAlignment.Right`；
4.  将剩余的片段以正确的对齐方式添加到右列。

下面的代码片段显示了这些步骤:

```
private void AddressSection()
{
    var box = new FloatingBox(524, 120)
    {
        ColumnInfo =
        {
            ColumnCount = 2,
            ColumnWidths = "252 252"
        },
        Padding =
        {
            Top = 20
        }
    };
    TextFragment fragment;

    BillFrom.Insert(0, "FROM:");
    foreach (var str in BillFrom)
    {
        fragment = new TextFragment(str);
        fragment.TextState.Font = _timeNewRomanFont;
        fragment.TextState.FontSize = 12;
        // Add fragment to paragraph
        box.Paragraphs.Add(fragment);
    }

    fragment = new TextFragment("BILL TO:") { IsFirstParagraphInColumn = true };
    fragment.TextState.Font = _timeNewRomanFont;
    fragment.TextState.FontSize = 12;
    fragment.TextState.HorizontalAlignment = HorizontalAlignment.Right;
    box.Paragraphs.Add(fragment);

    foreach (var str in BillTo)
    {
        fragment = new TextFragment(str);
        fragment.TextState.Font = _timeNewRomanFont;
        fragment.TextState.FontSize = 12;
        fragment.TextState.HorizontalAlignment = HorizontalAlignment.Right;
        // Add fragment to paragraph
        box.Paragraphs.Add(fragment);
    }
    _pdfPage.Paragraphs.Add(box);
} 
```

Enter fullscreen mode Exit fullscreen mode

第四步。呈现网格部分。我们将使用[表格](https://apireference.aspose.com/net/pdf/aspose.pdf/table)对象以表格格式表示数据。首先，我们需要定义表格列和单元格的默认装饰。请注意，`ColumnWidths`属性通过设置它们的宽度隐式地定义了一些列。在我们的网格中，我们将对标题行使用反向配色方案。以下算法用于创建表格行:

*   调用`table.Rows.Add()`创建行并获取对新行的引用；
*   调用`row.Cells.Add()`创建每个单元格并获取对新单元格的引用；
*   如果需要，设置单元格装饰；

totals 子部分的创建类似，但是为了更好地查看，我们使用`ColSpan`属性将前 4 个单元格连接起来。

```
private void GridSection()
{
    // Initializes a new instance of the Table
    var table = new Table
    {
        ColumnWidths = "26 257 78 78 78",
        Border = new BorderInfo(BorderSide.Box, 1f, _textColor),
        DefaultCellBorder = new BorderInfo(BorderSide.Box, 0.5f, _textColor),
        DefaultCellPadding = new MarginInfo(4.5, 4.5, 4.5, 4.5),
        Margin =
        {
            Bottom = 10
        },
        DefaultCellTextState =
        {
            Font = _timeNewRomanFont
        }
    };

    var headerRow = table.Rows.Add();
    var cell = headerRow.Cells.Add("#");
    cell.Alignment = HorizontalAlignment.Center;
    headerRow.Cells.Add("Item");
    headerRow.Cells.Add("Price");
    headerRow.Cells.Add("Quantity");
    headerRow.Cells.Add("Sum");
    foreach (Cell headerRowCell in headerRow.Cells)
    {
        headerRowCell.BackgroundColor = _textColor;
        headerRowCell.DefaultCellTextState.ForegroundColor = _backColor;
    }

    foreach (var productItem in Items)
    {
        var row = table.Rows.Add();
        cell = row.Cells.Add(productItem.Id);
        cell.Alignment = HorizontalAlignment.Center;
        row.Cells.Add(productItem.Name);
        cell = row.Cells.Add(productItem.Price.ToString("C2"));
        cell.Alignment = HorizontalAlignment.Right;
        cell = row.Cells.Add(productItem.Quantity.ToString());
        cell.Alignment = HorizontalAlignment.Right;
        cell = row.Cells.Add(productItem.Total.ToString("C2"));
        cell.Alignment = HorizontalAlignment.Right;
    }

    foreach (var totalRow in Totals)
    {
        var row = table.Rows.Add();
        var nameCell = row.Cells.Add(totalRow.Text);
        nameCell.ColSpan = 4;
        var textCell = row.Cells.Add(totalRow.Value.ToString("C2"));
        textCell.Alignment = HorizontalAlignment.Right;
    }

    _pdfPage.Paragraphs.Add(table);
} 
```

Enter fullscreen mode Exit fullscreen mode

**第五步**。渲染`Terms and Conditions`部分。
此部分仅包含文本片段，其渲染与上述相同。

```
private void TermsSection()
{
    foreach (var detail in Details)
    {
        var fragment = new TextFragment(detail);
        fragment.TextState.Font = _timeNewRomanFont;
        fragment.TextState.FontSize = 12;
        _pdfPage.Paragraphs.Add(fragment);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

**第六步**。渲染页脚部分
根据之前声明的布局，最后一部分是可选的，必须放在页面底部。这意味着我们不能将文本片段添加到段落集合中，因为这种添加已经将我们的片段放在了“条款和条件”部分之后。
在这种情况下，我们需要使用一个 [TextBuilder 类](https://apireference.aspose.com/net/pdf/aspose.pdf.text/textbuilder)。这个类帮助我们在任何位置放置文本。此外，下面的代码片段显示了我们如何向文本片段添加超链接。

```
private void FooterSection()
{
    var fragment = new TextFragment(Footer);
    var len = fragment.TextState.MeasureString(fragment.Text);
    fragment.Position = new Position(_pdfPage.PageInfo.Width / 2 - len / 2, 20);
    fragment.Hyperlink = new WebHyperlink(Footer);
    var builder = new TextBuilder(_pdfPage);

    builder.AppendText(fragment);
} 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

在这篇文章中，我们考虑了如何从头开始创建一个简单的 PDF 文档。表格中的大多数参数(如字体大小、单元格边框宽度、列宽度)都是基于 A4 页面大小手动调整的。很明显，我们能够根据页面大小的比例来调整这些参数，但这将使示例稍微复杂一些，但不会改变创建文档的整体技巧。

一种完全不同的方法是使用一些模板并替换部分内容。