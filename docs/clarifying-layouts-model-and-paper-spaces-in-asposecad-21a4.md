# 在 Aspose.CAD 中阐明布局、模型和图纸空间

> 原文：<https://dev.to/nnevod/clarifying-layouts-model-and-paper-spaces-in-asposecad-21a4>

在 AutoCAD 工作流中，有独立模型和图纸空间的概念。在模型空间中，您创建实体，就像它们实际上应该出现的那样，具有真实世界的尺寸等。要创建可打印图形，可以切换到图纸空间，并创建包含显示模型空间不同视图和各种注释的视口的布局。这里的尺寸对应于您在实际纸质印刷品中看到的尺寸。从“模型”选项卡可以访问模型空间，从“布局”选项卡可以访问图纸空间。

## 如何使用 Aspose 查看我的图纸空间布局。CAD？

[Aspose。CAD](https://products.aspose.com/cad) 支持所有这些，但是有一点不同的术语。模型空间和图纸空间的布局都称为布局，模型空间只是有一个特定的布局名称-“模型”。要选择要导出的布局，创建一个布局名称数组(当然是字符串类型的)，并用该数组设置 [CadRasterizationOptions](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/cadrasterizationoptions/) 实例的[布局](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/cadrasterizationoptions/properties/layouts)属性。然后，所有指定的布局将一次导出为不同的页面，因此，如果您想一次导出多个布局，请使用 PDF 或 TIFF 作为输出格式。如果指定单页输出格式，将只导出第一个指定的布局。请看下面的例子:

```
using (Aspose.CAD.Image image = Aspose.CAD.Image.Load("source.dwg"))
{
    // Create an instance of CadRasterizationOptions
    Aspose.CAD.ImageOptions.CadRasterizationOptions rasterizationOptions = new Aspose.CAD.ImageOptions.CadRasterizationOptions();

    // Set page width & height
    rasterizationOptions.PageWidth = 1200;
    rasterizationOptions.PageHeight = 1200;

    // Specify a list of layout names
    rasterizationOptions.Layouts = new string[] { "Model", "Layout1" };

    // Create an instance of TiffOptions for the resultant image
    ImageOptionsBase options = new Aspose.CAD.ImageOptions.TiffOptions(Aspose.CAD.FileFormats.Tiff.Enums.TiffExpectedFormat.Default);

    // Set rasterization options
    options.VectorRasterizationOptions = rasterizationOptions;

    // Save resultant image
    image.Save("", options);                
} 
```

如我们所见，这里我们将模型空间和图纸空间的“Layout1”布局导出到两个单独的页面。默认情况下，Layouts 属性为空。默认情况下，导出过程中实际行为会因文件而有所不同。在大多数情况下，所有布局都将被导出。但是，空图纸空间布局可能无法导出。(如果指定单页输出格式，则仅导出模型空间，因为默认情况下它是第一个布局。)要导出特定布局或强制所有布局，您必须指定它们的名称，为此，您显然需要知道它们。有鉴于此，一个问题出现了:

## 那么我如何知道我的布局的名字呢？

谢天谢地，这不是问题。[CAD image](https://apireference.aspose.com/net/cad/aspose.cad.fileformats.cad/cadimage/)class’实例有一个 [Layouts](https://apireference.aspose.com/net/cad/aspose.cad.fileformats.cad/cadimage/properties/layouts) 属性，它包含一个 [CadLayoutDictionary](https://apireference.aspose.com/net/cad/aspose.cad.fileformats.cad/cadlayoutdictionary) ，这只是一个带有字符串键和 [CadLayout](https://apireference.aspose.com/net/cad/aspose.cad.fileformats.cad.cadobjects/cadlayout) 值的字典。本词典包含文件中存在的所有布局，包括模型布局。CadLayout 用于 Aspose.CAD 中 AutoCAD 布局的内部表示。关键字是相应布局的名称。布局名称也可以通过 CadLayout 的 [LayoutName](https://apireference.aspose.com/net/cad/aspose.cad.fileformats.cad.cadobjects/cadlayout/properties/layoutname) 属性访问。下面的例子将把所有文件的布局打印到控制台:

```
using (Aspose.CAD.Image image = Aspose.CAD.Image.Load("source.dwg"))
{
    Aspose.CAD.FileFormats.Cad.CadImage cadImage = (Aspose.CAD.FileFormats.Cad.CadImage)image;

    Aspose.CAD.FileFormats.Cad.CadLayoutDictionary layouts = cadImage.Layouts;
    foreach (Aspose.CAD.FileFormats.Cad.CadObjects.CadLayout layout in layouts.Values)
    {
        //Console output:
        //Model
        //Layout1
        Console.WriteLine(layout.LayoutName); 
    }
} 
```

现在我们有了导出我们想要的布局所需的一切。

## 从文件中导出所有图纸空间布局。

所以我们得到所有的布局名称，从中过滤出“模型”，然后导出所有剩余的布局:

```
using (Aspose.CAD.Image image = Aspose.CAD.Image.Load("source.dwg"))
{
    Aspose.CAD.FileFormats.Cad.CadImage cadImage = (Aspose.CAD.FileFormats.Cad.CadImage)image;

    List<string> layoutNames = new List<string>();

    Aspose.CAD.FileFormats.Cad.CadLayoutDictionary layouts = cadImage.Layouts;
    foreach (Aspose.CAD.FileFormats.Cad.CadObjects.CadLayout layout in layouts.Values)
    {
        if (layout.LayoutName != "Model")
            layoutNames.Add(layout.LayoutName);
    }

    Aspose.CAD.ImageOptions.PdfOptions pdfOptions = new Aspose.CAD.ImageOptions.PdfOptions();

    Aspose.CAD.ImageOptions.CadRasterizationOptions cadOptions = new ImageOptions.CadRasterizationOptions();

    cadOptions.PageWidth = 1000;
    cadOptions.PageHeight = 1000;

    cadOptions.Layouts = layoutNames.ToArray();

    pdfOptions.VectorRasterizationOptions = cadOptions;

    cadImage.Save("output.pdf", pdfOptions);
} 
```

瞧，我们现在有了一个 PDF 文件，它包含了 AutoCAD 文件中作为单独页面的所有图纸空间布局。

目前就这些，敬请关注！

如需更多示例，请访问[网站。CAD GitHub](https://github.com/aspose-cad) 页面。Aspose.CAD 上还有 [Twitter](https://twitter.com/Asposecad) 和[脸书](https://www.facebook.com/AsposeCAD)的新闻页面