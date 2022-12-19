# Aspose 简介。CAD 库

> 原文：<https://dev.to/nnevod/introduction-to-asposecad-library-361h>

[Aspose。CAD](https://products.aspose.com/cad) 是一个独立的软件库。Net 和 Java 平台，可以读取 CAD 文件，如 DWG，DXF，DNG，IFC，STL 文件，并可以将其内容导出为 PDF 文件和光栅图像。它不需要额外的软件工作-不需要 AutoCAD 或 ReadDWG，它自己工作。

它还允许部分导出文件，仅导出 DWG 和 DXF 文件中的特定实体或图层，在导出过程中替换 CAD 文件中指定的颜色或字体，以及导出 3D 实体。

当一只蜘蛛。CAD 是一个商业库，它允许免费评估，提供所有的功能，并可以随时用于实验，但只加载有限的复杂性和水印导出图像文件。除了预先付费，你还可以使用计量的、按使用付费的许可证。

我将写一系列文章，涵盖使用 Aspose 的要点。使用 [Aspose 的 CAD。CAD for。NET](https://products.aspose.com/cad/net) ，从加载 CAD 文件、导出图像等最基础的东西开始，到更具体的方面。

所以，让我们从最简单的例子开始:

## 加载 CAD 文件并导出图像

该过程包括三个主要步骤:
1)加载 CAD 文件。
2)设置导出设置。
3)保存图像。

### 加载 CAD 文件

第一步非常简单。只需调用 [Image](https://apireference.aspose.com/net/cad/aspose.cad/image) 类的静态 [Load](https://apireference.aspose.com/net/cad/aspose.cad.image/load/methods/2) 方法，并将文件路径传递给它:

```
 string dwgPathToFile =  "files/cad.dwg";
            Image cadImage1 = Image.Load(dwgPathToFile); 
```

Enter fullscreen mode Exit fullscreen mode

Load 方法将根据文件扩展名确定文件格式，并创建相应的 Image 子类对象。没有争论！

现在我将跳到第三步，因为它同样简单，然后再回到第二步。

### 保存图像

在你加载的图像上调用 [Save](https://apireference.aspose.com/net/cad/aspose.cad.image/save/methods/3) 方法，传递要保存的文件路径和 [ImageOptionsBase](https://apireference.aspose.com/net/cad/aspose.cad/imageoptionsbase) 对象，就完成了。

```
 cadImage1.Save("cad.pdf", pdfOptions); 
```

Enter fullscreen mode Exit fullscreen mode

输出图像格式将由 ImageOptionsBase 的特定子类决定，pdfOptions 就是其中的一个实例。所有输出格式都有单独的类。让我们在第二步中更深入地探索。

### 设置导出选项

从图书馆用户的角度来看，这是主要工作完成的地方。您可以通过创建 ImageOptionsBase 的相应子类的对象来选择输出格式。
image options base 有以下子类:
[PdfOptions](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/pdfoptions) 、[bmoptions](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/bmpoptions)、 [GifOptions](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/gifoptions) 、 [JpegOptions](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/jpegoptions) 、 [PngOptions](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/pngoptions) 、[PSP options](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/psdoptions)、 [TiffOptions](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/tiffoptions) 以及一些 [more](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/) 。每个都提供了相应输出文件格式的设置。然后通过设置它的属性来设置它，然后传递到第三步。
最重要的属性是[VectorRasterizationOptions](https://apireference.aspose.com/net/cad/aspose.cad/imageoptionsbase/properties/vectorrasterizationoptions)属性，我们应该用一个[CadRasterizationOptions](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/cadrasterizationoptions)类的实例来设置它。这个类指定 CAD 图像应该如何呈现-宽度、像素高度、CAD 内容是否应该居中、背景颜色和对象的覆盖颜色等。在其最简单的形式中，该步骤可以通过以下方式完成:

```
 PdfOptions pdfOptions = new PdfOptions();
                CadRasterizationOptions rasterizationOptions= new CadRasterizationOptions();
                pdfOptions.VectorRasterizationOptions = rasterizationOptions;

                rasterizationOptions.PageHeight = 1600;
                rasterizationOptions.PageWidth = 1600;
                rasterizationOptions.DrawType = CadDrawTypeMode.UseObjectColor; 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们创建一个 PdfOptions 实例，它将输出设置为 PDF 文件格式，创建新的 CadRasterizationOptions 来设置 CAD 图像的呈现方式，并用它设置 PdfOptions 的 VectorRasterizationOptions 属性，然后指定输出图像的高度和宽度(
默认情况下，您的 CAD 图像将被拉伸/收缩以适应指定的输出页面大小，保持纵横比)，并让栅格化使用 CAD 文件中指定的颜色。
设置完成后，我们就可以开始第三步了。

现在，您已经知道如何使用 Aspose 将 CAD 文件导出为光栅图像或 PDF 文件。CAD for .NET. [Java](https://products.aspose.com/cad/java) 版本的 Aspose。CAD 本质上也是一样的。

下面是完整的例子:

```
using System;
using System.Collections.Generic;
using Aspose.CAD;
using Aspose.CAD.ImageOptions;

namespace ConsoleExampleAsposeCAD
{
    class Program
    {
        static void Main()
        {

            string dwgPathToFile =  "files/cad.dwg";
            Image cadImage1 = Image.Load(dwgPathToFile);

            PdfOptions pdfOptions = new PdfOptions();
            CadRasterizationOptions rasterizationOptions= new CadRasterizationOptions();
            pdfOptions.VectorRasterizationOptions = rasterizationOptions;

            rasterizationOptions.PageHeight = 1600;
            rasterizationOptions.PageWidth = 1600;
            rasterizationOptions.DrawType = CadDrawTypeMode.UseObjectColor;

            cadImage1.Save("cad.pdf", pdfOptions);            

        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

只需创建一个新的控制台应用程序，将示例内容复制到 Program.cs 中，安装 Aspose。CAD for。NET from NuGet，自己看看。

## 旋转和翻转图像

用阿司匹林。这是非常容易的出口 CAD 图像不仅是因为它，但在旋转或镜像的形式。回到第一部分的第二步，稍微调整一下:

```
 PdfOptions pdfOptions = new PdfOptions();
                //Here it is, the rotation and flip!
                pdfOptions.Rotation = RotateFlipType.Rotate270FlipY;
                //^^^^
                CadRasterizationOptions rasterizationOptions= new CadRasterizationOptions();
                pdfOptions.VectorRasterizationOptions = rasterizationOptions;

                rasterizationOptions.PageHeight = 1600;
                rasterizationOptions.PageWidth = 1600;
                rasterizationOptions.DrawType = CadDrawTypeMode.UseObjectColor; 
```

Enter fullscreen mode Exit fullscreen mode

如图所示，[旋转](https://apireference.aspose.com/net/cad/aspose.cad/imageoptionsbase/properties/rotation)属性接受来自[旋转类型](https://apireference.aspose.com/net/cad/aspose.cad/rotatefliptype)枚举的值。它列出了以 90 度为步长旋转和沿 X 和 Y 轴镜像的所有组合——一次或两次。Rotation 属性可用于 ImageOptionsBase 的所有子类，这意味着它支持任何输出格式。

目前就这些，敬请关注！

如需更多示例，请访问[网站。CAD GitHub](https://github.com/aspose-cad) 页面。Aspose.CAD 上还有 [Twitter](https://twitter.com/AsposeCAD) 和[脸书](https://www.facebook.com/AsposeCAD)的新闻页面