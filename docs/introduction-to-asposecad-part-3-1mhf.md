# Aspose 简介。计算机辅助设计，第 3 部分

> 原文：<https://dev.to/nnevod/introduction-to-asposecad-part-3-1mhf>

[第 1 部分](https://dev.to/nnevod/introduction-to-asposecad-library-361h)
第 3 部分

大多数情况下。CAD 所要使用的大概都是与 AutoCAD 相关的文件，比如 DWG 和 DXF 格式。因此，我将描述一个怎样的姿势。CAD 工作与 DWG 和 DXF 格式。

让我们从一个简单而常见的事情开始:选择特定的布局或图层。阿斯波斯。CAD 支持列举层和布局，并选择一个或几个特定的层或布局进行导出。让我们看看！

## 获取布局名称列表

请看这段代码:

```
 using (Aspose.CAD.Image image = Aspose.CAD.Image.Load(sourceFilePath))
            {
                Aspose.CAD.FileFormats.Cad.CadImage cadImage = (Aspose.CAD.FileFormats.Cad.CadImage)image;

                Aspose.CAD.FileFormats.Cad.CadLayoutDictionary layouts = cadImage.Layouts;
                foreach (Aspose.CAD.FileFormats.Cad.CadObjects.CadLayout layout in layouts.Values)
                {
                    Console.WriteLine("Layout " + layout.LayoutName);
                }
            } 
```

Enter fullscreen mode Exit fullscreen mode

那里发生了什么事？当您加载 DXF 或 DWG 文件时，它被表示为 [CadImage](https://apireference.aspose.com/net/cad/aspose.cad.fileformats.cad/cadimage) 的子类的实例，该实例具有与这些文件格式相关的特定属性。因此，您可以将图像转换为 CadImage 来访问它们。

然后在 CadImage 中有 [Layouts](https://apireference.aspose.com/net/cad/aspose.cad.fileformats.cad/cadimage/properties/layouts) 属性，它是针对 [CadLayout](https://apireference.aspose.com/net/cad/aspose.cad.fileformats.cad.cadobjects/cadlayout) 对象的特定[字典](https://apireference.aspose.com/net/cad/aspose.cad.fileformats.cad/cadlayoutdictionary)。从其中提取 CadLayout 对象，就像从任何 [IDictionary](https://msdn.microsoft.com/en-us/library/9dhwsays) 中提取一样，在这里您可以访问布局的属性，包括它的[名称](https://apireference.aspose.com/net/cad/aspose.cad.fileformats.cad.cadobjects/cadlayout/properties/layoutname)。

## 选择要导出的布局

与任何导出过程的设置一样，这是通过设置 [ImageOptionsBase](https://apireference.aspose.com/net/cad/aspose.cad/imageoptionsbase) 的后代的实例来完成的，例如[b options](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/bmpoptions)、 [PngOptions](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/pngoptions) 、 [PdfOptions](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/pdfoptions) 等。-让我们称它们为图像导出选项，更具体地说，在这种情况下，通过设置分配给图像导出选项的[VectorRasterizationOptions](https://apireference.aspose.com/net/cad/aspose.cad/imageoptionsbase/properties/vectorrasterizationoptions)属性的 [CadRasterizationOptions](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/cadrasterizationoptions) 的实例。

```
 using (Aspose.CAD.Image image = Aspose.CAD.Image.Load("in.dwg"))
            {

                // Create an instance of PdfOptions
                Aspose.CAD.ImageOptions.PdfOptions pdfOptions = new Aspose.CAD.ImageOptions.PdfOptions();

                // Create an instance of CadRasterizationOptions and set its various properties
                Aspose.CAD.ImageOptions.CadRasterizationOptions rasterizationOptions = new Aspose.CAD.ImageOptions.CadRasterizationOptions();
                rasterizationOptions.PageWidth = 1600;
                rasterizationOptions.PageHeight = 1600;
                // Specify desired layout name
                rasterizationOptions.Layouts = new string[] { "Layout1" };

                // Set the VectorRasterizationOptions property
                pdfOptions.VectorRasterizationOptions = rasterizationOptions;

                image.Save("out.pdf", pdfOptions);                
            } 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，该过程与本系列文章的第 1 部分中概述的过程相同，唯一的变化是使用了[布局](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/cadrasterizationoptions/properties/layouts)属性。设置它将导致只导出这些在布局属性中指定了名称的布局。默认情况下，它将使用模型布局，因此如果您希望恢复到该布局，只需将该属性设置为 null。

## 获取图层名称列表

这与布局的做法非常相似，只有一点不同:

```
 using (Aspose.CAD.Image image = Aspose.CAD.Image.Load(sourceFilePath))
            {
                Aspose.CAD.FileFormats.Cad.CadImage cadImage = (Aspose.CAD.FileFormats.Cad.CadImage)image;

                Aspose.CAD.FileFormats.Cad.CadLayersList layers = cadImage.Layers;
                foreach (string layerName in layers.GetLayersNames())
                {
                    Console.WriteLine("Layer " + layerName);
                }
            } 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，唯一的区别是[层](https://apireference.aspose.com/net/cad/aspose.cad.fileformats.cad/cadimage/properties/layers)属性不是一个字典，而是一个 [CadLayersList](https://apireference.aspose.com/net/cad/aspose.cad.fileformats.cad/cadlayerslist) ，它允许直接获得一个字符串形式的层名称列表。

## 选择特定图层进行导出

同样，这与处理布局非常相似。

```
 // Create an instance of PdfOptions
                Aspose.CAD.ImageOptions.PdfOptions pdfOptions = new Aspose.CAD.ImageOptions.PdfOptions();

                // Create an instance of CadRasterizationOptions and set its various properties
                Aspose.CAD.ImageOptions.CadRasterizationOptions rasterizationOptions = new Aspose.CAD.ImageOptions.CadRasterizationOptions();
                rasterizationOptions.PageWidth = 1600;
                rasterizationOptions.PageHeight = 1600;
                // Specify desired layout name
                // Most of the CAD drawings have a layer by name 0, you may specify any name
                rasterizationOptions.Layers.Add("0");

                // Set the VectorRasterizationOptions property
                pdfOptions.VectorRasterizationOptions = rasterizationOptions;

                image.Save("out.pdf", pdfOptions); 
```

Enter fullscreen mode Exit fullscreen mode

不同的是 CadRasterizationOptions 的 [Layers](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/cadrasterizationoptions/properties/layers) 属性是一个[列表](http://msdn2.microsoft.com/en-us/library/6sh2ey19)，默认情况下，当列表为空时，将导出所有图层。

目前就这些，敬请关注！

如需更多示例，请访问[网站。CAD GitHub](https://github.com/aspose-cad) 页面。Aspose.CAD 上还有 [Twitter](https://twitter.com/Asposecad) 和[脸书](https://www.facebook.com/AsposeCAD)的新闻页面