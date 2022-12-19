# 使用 C#生成 PDF 文件(发票)

> 原文：<https://dev.to/apitron/generating-pdf-file-invoice-using-c-347i>

在我的第一篇文章中，我想分享一些我使用 Apitron 生成 PDF 文档的经验。PDF。工具包。NET 并介绍基本的 PDF 创建技术。作为一个例子，我选择了一个经常使用的文档——发票。

[![Invoice](../Images/d291d0f79542a627a52ba2308ccd9968.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RuK2n5FR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ggs7sgiwv98ztmxfyg04.png)

### **代码**

这里我不会深入平台细节，而只关注 PDF 生成和相关代码。

下面列出了几个负责生成 PDF 的函数:

##### 私有 void GenerateInvoice(流流)

```
string imagesPath = @"..\..\images";
FlowDocument document = new FlowDocument();

document.StyleManager.RegisterStyle("gridrow.tableHeader",new Style() {Background = RgbColors.LightSlateGray});
document.StyleManager.RegisterStyle("gridrow.centerAlignedCells > *,gridrow > *.centerAlignedCell",new Style() {Align = Align.Center, Margin = new Thickness(0)});
document.StyleManager.RegisterStyle("gridrow > *.leftAlignedCell",new Style() {Align = Align.Left, Margin = new Thickness(5, 0, 0, 0)});
document.StyleManager.RegisterStyle("gridrow > *",new Style() {Align = Align.Right, Margin = new Thickness(0, 0, 5, 0)});

ResourceManager resourceManager = new ResourceManager();
resourceManager.RegisterResource(
    new Apitron.PDF.Kit.FixedLayout.Resources.XObjects.Image("logo",
    Path.Combine(imagesPath, "storeLogo.png"), true) {Interpolate = true});

resourceManager.RegisterResource(new Apitron.PDF.Kit.FixedLayout.Resources.XObjects.Image("stamp",Path.Combine(imagesPath, "stamp.png"), true) {Interpolate = true});
document.PageHeader.Margin = new Thickness(0, 40, 0, 20);
document.PageHeader.Padding = new Thickness(10, 0, 10, 0);
document.PageHeader.Height = 120;
document.PageHeader.Background = RgbColors.LightGray;
document.PageHeader.LineHeight = 60;
document.PageHeader.Add(new Image("logo"){Height = 50, Width = 50, VerticalAlign = VerticalAlign.Middle});
document.PageHeader.Add(new TextBlock("Invoice")
                        {
                            Display = Display.InlineBlock,
                            Align = Align.Right,
                            Font = new Font(StandardFonts.CourierBold, 20),
                            Color = RgbColors.Black
                        });
Section pageSection = new Section() {Padding = new Thickness(20)};
pageSection.AddItems(CreateInfoSubsections(new string[] {txtCompany.Text,"Bill to:\r\n" + txtCustomerInfo.Text}));

pageSection.Add(new Hr() {Padding = new Thickness(0, 20, 0, 20)});
pageSection.Add(CreateProductsGrid());
pageSection.Add(new Br {Height = 20});
pageSection.Add(new Section() {Width = 250, Display = Display.InlineBlock});
pageSection.Add(new Image("stamp"));

document.Add(pageSection);
document.Write(stream, resourceManager, new PageBoundary(Boundaries.A4)); 
```

##### 私有网格 CreateProductsGrid()

```
Grid productsGrid = new Grid(20, Length.Auto, 30, 50, 55, 60);
productsGrid.Add(new GridRow(new TextBlock("#"), new TextBlock("Product"), new TextBlock("Qty."), new TextBlock("Price"), new TextBlock("Disc.(%)"), new TextBlock("Total")) Class = "tableHeader centerAlignedCells"});Decimal invoiceTotal = 0;

foreach (ProductEntry product in products)
{
    TextBlock pos = new TextBlock(product.Pos.ToString()) {Class = "centerAlignedCell"};
    TextBlock description = new TextBlock(product.Description) {Class = "leftAlignedCell"};
    TextBlock qty = new TextBlock(product.Qty.ToString()) {Class = "centerAlignedCell"};
    TextBlock price = new TextBlock( product.Price.ToString(CultureInfo.InvariantCulture));
    TextBlock discount = new TextBlock(product.Discount.ToString(CultureInfo.InvariantCulture));
    TextBlock total = new TextBlock(product.Total.ToString(CultureInfo.InvariantCulture));
    productsGrid.Add(new GridRow(pos, description, qty, price, discount, total));
    invoiceTotal += product.Total;
}

productsGrid.Add(new GridRow(new TextBlock("Total(USD)") {ColSpan = 4}, new TextBlock(invoiceTotal.ToString(CultureInfo.InvariantCulture)) {ColSpan = 2}));

return productsGrid; 
```