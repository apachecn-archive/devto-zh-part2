# Aspose 的有趣特征。计算机辅助设计

> 原文：<https://dev.to/nnevod/interesting-features-of-asposecad-1nk2>

你好，在这篇文章中，我将列举出[与众不同的地方。CAD](https://products.aspose.com/cad/) 从其他类似的软件和一般关于它的有趣的功能。

## 导出到指定的输出尺寸

可以为输出 PDF 文件设置特定的尺寸，例如导出为 A4 大小的文档。有一个关于如何设置 Aspose 的例子。CAD 输出所需的 PDF 大小。导出区域的内容将被重新缩放以适合输出文档区域，而纵横比不变。还要注意，代码示例还包含光栅输出的示例，这要简单得多——只需将所需的 DPI 乘以所需的输出文档尺寸，就可以得到所需的光栅图像分辨率。

```
public static void Run()
{
    using (var cadImage = Image.Load("visualization_-_conference_room.dwg"))
    {

        // export to pdf
        CadRasterizationOptions rasterizationOptions = new CadRasterizationOptions();
        rasterizationOptions.Layouts = new string[] { "Model" };

        bool currentUnitIsMetric = false;
        double currentUnitCoefficient = 1.0;
        DefineUnitSystem(cadImage.UnitType, out currentUnitIsMetric, out currentUnitCoefficient);

        if (currentUnitIsMetric)
        {
            double metersCoeff = 1 / 1000.0;

            double scaleFactor = metersCoeff / currentUnitCoefficient;

            rasterizationOptions.PageWidth = (float)(210 * scaleFactor);
            rasterizationOptions.PageHeight = (float)(297 * scaleFactor);
            rasterizationOptions.UnitType = UnitType.Millimeter;
        }
        else
        {
            rasterizationOptions.PageWidth = (float)(8.27f / currentUnitCoefficient);
            rasterizationOptions.PageHeight = (float)(11.69f / currentUnitCoefficient);
            rasterizationOptions.UnitType = UnitType.Inch;
        }

        rasterizationOptions.AutomaticLayoutsScaling = true;

        PdfOptions pdfOptions = new PdfOptions
        {
            VectorRasterizationOptions = rasterizationOptions
        };

        cadImage.Save("out.pdf", pdfOptions);

        PngOptions png = new PngOptions();
        png.VectorRasterizationOptions = rasterizationOptions;
        // export to raster
        //A4 size at 300 DPI - 2480 x 3508 
        rasterizationOptions.PageHeight = 3508;
        rasterizationOptions.PageWidth = 2480;

        cadImage.Save("out.png", png);
    }

}

private static void DefineUnitSystem(UnitType unitType, out bool isMetric, out double coefficient)
{
    isMetric = false;
    coefficient = 1.0;

    switch (unitType)
    {
        case UnitType.Parsec:
            coefficient = 3.0857 * 10000000000000000.0;
            isMetric = true;
            break;
        case UnitType.LightYear:
            coefficient = 9.4607 * 1000000000000000.0;
            isMetric = true;
            break;
        case UnitType.AstronomicalUnit:
            coefficient = 1.4960 * 100000000000.0;
            isMetric = true;
            break;
        case UnitType.Gigameter:
            coefficient = 1000000000.0;
            isMetric = true;
            break;
        case UnitType.Kilometer:
            coefficient = 1000.0;
            isMetric = true;
            break;
        case UnitType.Decameter:
            isMetric = true;
            coefficient = 10.0;
            break;
        case UnitType.Hectometer:
            isMetric = true;
            coefficient = 100.0;
            break;
        case UnitType.Meter:
            isMetric = true;
            coefficient = 1.0;
            break;
        case UnitType.Centimenter:
            isMetric = true;
            coefficient = 0.01;
            break;
        case UnitType.Decimeter:
            isMetric = true;
            coefficient = 0.1;
            break;
        case UnitType.Millimeter:
            isMetric = true;
            coefficient = 0.001;
            break;
        case UnitType.Micrometer:
            isMetric = true;
            coefficient = 0.000001;
            break;
        case UnitType.Nanometer:
            isMetric = true;
            coefficient = 0.000000001;
            break;
        case UnitType.Angstrom:
            isMetric = true;
            coefficient = 0.0000000001;
            break;
        case UnitType.Inch:
            coefficient = 1.0;
            break;
        case UnitType.MicroInch:
            coefficient = 0.000001;
            break;
        case UnitType.Mil:
            coefficient = 0.001;
            break;
        case UnitType.Foot:
            coefficient = 12.0;
            break;
        case UnitType.Yard:
            coefficient = 36.0;
            break;
        case UnitType.Mile:
            coefficient = 63360.0;
            break;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 标准化为导出的绝对指标

默认情况下，Aspose。CAD 使用绘图的相关单位。但是在 [CadRasterizationOptions](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/cadrasterizationoptions/) 类中有一个 [UnitType](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/vectorrasterizationoptions/properties/unittype) 属性，它指定了使用的单元类型。在渲染过程中，一个单位被解释为 1 个像素，因此如果图像的指定大小为 10x10 米，并且选择厘米作为单位类型，则该图像将被栅格化为 1000x1000 像素的图像。

```
 string fileName = GetFileFromDesktop("Floorplan.dwg");
    using (Aspose.CAD.Image image = Aspose.CAD.Image.Load(fileName))
    {
        BmpOptions bmpOptions = new BmpOptions();
        CadRasterizationOptions cadRasterizationOptions = new CadRasterizationOptions();
        bmpOptions.VectorRasterizationOptions = cadRasterizationOptions;
        cadRasterizationOptions.CenterDrawing = true;
        cadRasterizationOptions.UnitType = UnitType.Centimeter;
        cadRasterizationOptions.Layouts = new string[] { "Model" };
        // export
        string outPath = fileName + ".bmp";
        image.Save(outPath, bmpOptions);
    } 
```

Enter fullscreen mode Exit fullscreen mode

## 支持 PDF/A

阿斯波斯。CAD 支持指定符合 PDF/A 标准来呈现存档的 PDF 文档。该过程包括创建常规的 [PdfOptions](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/pdfoptions/) 来导出图像，将其 [CorePdfOptions](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/pdfoptions/properties/corepdfoptions) 属性设置为 [PdfDocumentOptions](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/pdfdocumentoptions/) 的新实例，并在该实例中设置 [Compliance](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/pdfdocumentoptions/properties/compliance) 字段。之后，使用该 PdfOptions 实例保存的图像将被保存为 PDF/A 兼容的 PDF 文件。例子:

```
PdfOptions pdfOptions = new Aspose.CAD.ImageOptions.PdfOptions
{
    VectorRasterizationOptions = rasterizationOptions
};

pdfOptions.CorePdfOptions = new PdfDocumentOptions();

pdfOptions.CorePdfOptions.Compliance = PdfCompliance.PdfA1a;
cadImage.Save(outPath, pdfOptions);

pdfOptions.CorePdfOptions.Compliance = PdfCompliance.PdfA1b;
cadImage.Save(outPath, pdfOptions); 
```

Enter fullscreen mode Exit fullscreen mode

## 导出时的边距控制。

默认情况下，Aspose。CAD 呈现的 CAD 文件在文件或页面的整个内容周围有很小的边距。在 [CadRasterizationOptions](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/cadrasterizationoptions/) 类中有一个 [Zoom](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/cadrasterizationoptions/properties/zoom) 属性控制图像缩放。默认情况下，它被设置为略小于 1，以提供边距。如果您不需要边距，请将其设置为 1。例子:

```
using (CadImage cadImage = (CadImage)Image.Load(fileName))
{
    // call after changes done to image to check new size of the image.
    cadImage.UpdateSize();

    CadRasterizationOptions rasterizationOptions = new CadRasterizationOptions();

    rasterizationOptions.UnitType = UnitType.Micrometer;
    rasterizationOptions.PageHeight = cadImage.Height;
    rasterizationOptions.PageWidth = cadImage.Width;
    rasterizationOptions.Zoom = 1f;

    PdfOptions pdfOptions = new PdfOptions();
    pdfOptions.VectorRasterizationOptions = rasterizationOptions;
    cadImage.Save(outDir + fileName + ".pdf", pdfOptions);
} 
```

Enter fullscreen mode Exit fullscreen mode

## 导出 CAD 文档的特定区域

这也是可能的，但是，它适用于 DWG 文档，并且不容易做到，因为它涉及到为一个区域创建一个自定义视口。参见示例:

```
 CadImage cadImage = Image.Load(FileName) as CadImage;

    CadRasterizationOptions rasterizationOptions = new CadRasterizationOptions();
    rasterizationOptions.Layouts = new string[] { "Model" };
    rasterizationOptions.NoScaling = true;

    rasterizationOptions.PageHeight = height;
    rasterizationOptions.PageWidth = width;

    // note: preserving some empty borders around part of image is the responsibility of customer
    // top left point of region to draw

    CadVportTableObject newView = new CadVportTableObject();

    // note: exactly such table name is required for active view
    newView.TableName = "*Active";
    newView.CenterPoint.X = topLeft.X + width / 2f;
    newView.CenterPoint.Y = topLeft.Y - height / 2f;
    newView.ViewHeight.Value = height;
    //newView.ViewAspectRatio.Value = width / height;

    // search for active viewport and replace it
    for (int i = 0; i < cadImage.ViewPorts.Count; i++)
    {
        CadVportTableObject currentView = (CadVportTableObject)(cadImage.ViewPorts[i]);
        if ((currentView.TableName == null && cadImage.ViewPorts.Count == 1) ||
        string.Equals(currentView.TableName.ToLowerInvariant(), "*active"))
        {
            cadImage.ViewPorts[i] = newView;
            break;
        }
    }
    PngOptions pngOptions = new PngOptions();
    pngOptions.VectorRasterizationOptions = rasterizationOptions;
    cadImage.Save("output.png", pngOptions); 
```

Enter fullscreen mode Exit fullscreen mode

## 跟踪导出错误

阿斯波斯。CAD 有一种方法可以记录在将 CAD 文件导出为光栅或 PDF 时发生的错误。在 [CadRasterizationOptions](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/cadrasterizationoptions/) 类中有一个 [RenderResult](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/cadrasterizationoptions/fields/renderresult) 事件，该事件在导出完成时被调用。事件处理程序接收一个 [CadRenderResult](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/cadrenderresult/) ，它包含一个在[失败](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/cadrenderresult/fields/failures)字段中的错误列表。查看如何使用它的示例:

```
using (Image image = Image.Load("example.dxf"))
            using (FileStream stream = new FileStream("output_example.pdf", FileMode.Create))
            {
                PdfOptions pdfOptions = new PdfOptions();

                CadRasterizationOptions cadRasterizationOptions =
                    new CadRasterizationOptions();
                pdfOptions.VectorRasterizationOptions = cadRasterizationOptions;
                cadRasterizationOptions.PageWidth = 800;
                cadRasterizationOptions.PageHeight = 600;

                int idxError = 1;
                cadRasterizationOptions.RenderResult +=
                    new CadRasterizationOptions.CadRenderHandler(
                        delegate (CadRenderResult result)
                {

                    Console.WriteLine("Tracking results of exporting");

                    if (result.IsRenderComplete)
                        return;

                    Console.WriteLine("Have some problems:");

                    foreach (RenderResult rr in result.Failures)
                        Console.WriteLine(string.Format("{0}. {1}, {2}", idxError++, rr.RenderCode.ToString(), rr.Message));

                });

                Console.WriteLine("Exporting to pdf format");
                image.Save(stream, pdfOptions);
            } 
```

Enter fullscreen mode Exit fullscreen mode

## 3D 对象导出支持

AutoCAD 和其他文件格式中的 3D 对象可以使用 Aspose.CAD 导出。该库使用存储在文件中的视点，因此导出的图像将与加载文件后立即在 AutoCAD 中看到的图像一样。
默认情况下，AutoCAD 文件只导出 2D 对象。要切换到 3D 对象导出，请将 [CadRasterizationOptions](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/cadrasterizationoptions) 实例的 [TypeOfEntities](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/cadrasterizationoptions/properties/typeofentities) 属性设置为[type of entities](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/typeofentities). entities 3D 并执行导出。
请注意，IFC 文件没有存储视点信息，因此您必须在导出时提供观察点。参见示例:

```
 using (IfcImage ifcImage = (IfcImage)Image.Load("ifcimage.ifc"))
            {
                JpegOptions options = new JpegOptions();
                options.VectorRasterizationOptions = new CadRasterizationOptions();
                options.VectorRasterizationOptions.PageWidth = 1500;
                options.VectorRasterizationOptions.PageHeight = 1500;
                float xAngle = 45;
                float yAngle = 0;
                float zAngle = 180;
                ((CadRasterizationOptions)(options.VectorRasterizationOptions)).ObserverPoint = new ObserverPoint(xAngle, yAngle, zAngle);
                ((CadRasterizationOptions)(options.VectorRasterizationOptions)).DrawType = CadDrawTypeMode.UseObjectColor;
                ifcImage.Save("ifcrender.jpg", options);
            } 
```

Enter fullscreen mode Exit fullscreen mode

## 多线程支持。

由 Aspose 加载的所有 CAD 文件。CAD—[Image](https://apireference.aspose.com/net/cad/aspose.cad/image/)类实例——是独立的，可以毫无问题地并发处理。不过，对单个图像的操作应该只发生在一个线程中。

## 即将到来——。净核心支持

。Net 标准将在不久的将来得到支持，因此将会有一个带有 native 的库版本。NET 核心支持，因此，不仅在 Java 中支持多平台，而且在。网也是。

## 即将推出-云版本

允许您使用 Aspose 的公共 REST API 服务。CAD，上传文件到 API 主机，处理它们并下载回来正在进行中。

暂时就这样吧！本文以后会有新老有趣的功能更新。