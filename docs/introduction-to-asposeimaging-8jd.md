# Aspose 简介。成像

> 原文：<https://dev.to/nnevod/introduction-to-asposeimaging-8jd>

[Aspose。成像](https://products.aspose.com/imaging/)是[的软件库。Net](https://products.aspose.com/imaging/net) 和 [Java](https://products.aspose.com/imaging/java) 提供了一个 API，用于在不同格式之间转换光栅图像，并允许修改和创建它们。Sharepoint 也有[版本。它不需要额外的软件来工作。](https://products.aspose.com/imaging/sharepoint)

当一只蜘蛛。Imaging 是一个商业库，它允许免费评估，提供所有的功能，并可以随时用于实验，但只加载有限的复杂性和水印导出图像文件。除了预先付费，你还可以使用计量的、按使用付费的许可证。

我将写一系列文章，涵盖使用 Aspose 的要点。使用 [Aspose 成像。为…成像。NET](https://products.aspose.com/imaging/net) ，从最基本的事情开始，比如加载图像并将其保存为不同的格式，到更具体的图像操作。

在本文中，我将描述以下可以用 Aspose 执行的操作。成像:

1.  图像的简单加载和保存
2.  创建图像
3.  在图像上绘图

## 图像的简单加载和保存

看一下示例代码:

```
 // 1 Load an image through file path location or stream
            using (Image image = Image.Load( "Sample1.png"))
            {
                //2 Create an instance of TiffOptions while specifying desired format Passsing TiffExpectedFormat.TiffJpegRGB will set the compression to Jpeg and BitsPerPixel according to the RGB color space.
                TiffOptions options = new TiffOptions(TiffExpectedFormat.TiffJpegRgb);
                //3 Save the image with specified options
                image.Save(dataDir + "SampleTiff_out.tiff", options);
            } 
```

Enter fullscreen mode Exit fullscreen mode

加载和保存图像的过程由三个步骤组成，在示例的注释中相应编号:
1)加载图像
2)设置处理选项
3)使用指定的处理选项保存图像。

让我们更深入地了解一下这些步骤。
1)加载是最简单的步骤，只需调用静态 [Load](https://apireference.aspose.com/net/imaging/aspose.imaging/image/methods/load/index) 方法的 [Image](https://apireference.aspose.com/net/imaging/aspose.imaging/image) 类指定路径到你的图像文件或一个[流](https://msdn.microsoft.com/en-us/library/system.io.stream(v=vs.110).aspx)你的图像数据。图像格式将根据其内容自动确定。所有代码所在的主要步骤。让我们跳到简单的第三步，稍后再回到这里。
3)在您的图像实例上调用 [Save](https://apireference.aspose.com/net/imaging/aspose.imaging/datastreamsupporter/methods/save/index) 方法，指定输出路径或流以及处理选项，就像在 [ImageOptionsBase](https://apireference.aspose.com/net/imaging/aspose.imaging/imageoptionsbase) 类的后代实例中一样。输出图像格式将由特定类型的处理选项对象决定，特定格式的设置将从其相应的字段中导出。因此，您会看到所有事情都在步骤 2 中完成了。

所以，深入第二步。
从 [ImageOptionsBase](https://apireference.aspose.com/net/imaging/aspose.imaging/imageoptionsbase) 派生出几个类，可以用来设置输出图像格式:[b options](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/bmpoptions)， [GifOptions](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/gifoptions) ， [Jpeg2000Options](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/jpeg2000options) ， [JpegOptions](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/jpegoptions) ， [PngOptions](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/pngoptions) ，[PSP options](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/psdoptions)， [SvgOptions](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/svgoptions) ，[web options](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/webpoptions)，这些类中的每一个都提供了将图像保存为相应格式的设置。它们中的大多数使用起来非常简单——只需使用无参数的构造函数创建一个实例就可以了，其他任何设置都是可选的。只有两种格式不同:对于 TiffOptions，您必须通过提供来自[TiffExpectedFormat](https://apireference.aspose.com/net/imaging/aspose.imaging.fileformats.tiff.enums/tiffexpectedformat)enum 的值来指定构造函数的压缩格式和位深度；对于 SvgOptions，您还必须使用 [SvgRasterizationOptions 类的实例来设置您的 SvgOptions 实例的](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/svgrasterizationoptions)[vectorrasterizationoptions](https://apireference.aspose.com/net/imaging/aspose.imaging/imageoptionsbase/properties/vectorrasterizationoptions)属性，并设置该 SvgRasterizationOptions 实例的 [PageHeight](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/vectorrasterizationoptions/properties/pageheight) 和 [PageWidth](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/vectorrasterizationoptions/properties/pagewidth) 由于 SVG 是一种矢量格式，它有页面维度的概念，而不仅仅是 X*Y 像素的矩阵。然后，嵌入到 SVG 页面中的光栅图像将在显示过程中进行缩放，以匹配指定的页面大小。

这对于基本的图像加载和保存已经足够了，你不需要更多了。只需查看带有指定名称空间等的完整示例。下面。

```
using System;
using System.Collections.Generic;
using System.Text;
using System.IO;
using Aspose.Imaging.FileFormats.Tiff;
using Aspose.Imaging.ImageOptions;
using Aspose.Imaging;
using Aspose.Imaging.FileFormats.Tiff.Enums;
using Aspose.Imaging.Sources;

namespace ImagingExample
{
    class Program
    {
        static void Main(string[] args)
        {
            using (Image image = Image.Load("Sample1.png"))
            {
                //2 Create an instance of TiffOptions while specifying desired format Passsing TiffExpectedFormat.TiffJpegRGB will set the compression to Jpeg and BitsPerPixel according to the RGB color space.
                TiffOptions options = new TiffOptions(TiffExpectedFormat.TiffJpegRgb);
                //3 Save the image with specified options
                image.Save( "SampleTiff_out.tiff", options);
            }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

只需为创建新的控制台应用程序项目。Net framework，并用那个替换 Program.cs 中的代码。

## 创建图像。

用阿司匹林。你不仅可以加载一个现有的图像，还可以创建一个新的图像，并以几种不同的方式填充它。看一下例子:

```
 // 1\. Create image options
            BmpOptions ImageOptions = new BmpOptions();
            ImageOptions.BitsPerPixel = 24;

            // 2\. Create an instance of FileCreateSource and assign it to Source property
            ImageOptions.Source = new FileCreateSource(dataDir + "DrawImagesUsingCoreFunctionality_out.bmp", false);

            // 3\. Create an instance of RasterImage and Get the pixels of the image by specifying the area as image boundary
            using (RasterImage rasterImage = (RasterImage)Image.Create(ImageOptions, 500, 500))
            {
                //4\. Create the contents of the image
                Color[] pixels = rasterImage.LoadPixels(rasterImage.Bounds);
                for (int index = 0; index < pixels.Length; index++)
                {
                    // Set the indexed pixel color to yellow
                    pixels[index] = Color.Yellow;
                }

                //4.5 Apply the pixel changes to the image 
                rasterImage.SavePixels(rasterImage.Bounds, pixels);
                //5\. And save all changes.
                rasterImage.Save();
            } 
```

Enter fullscreen mode Exit fullscreen mode

`
图像创建从创建上述 ImageOptionsBase 子类之一的实例开始。

然后，您必须指定一个与图像相关联的文件，因为我们不会从现有的源中加载图像。

在第三步中，使用创建的选项创建 [RasterImage](https://apireference.aspose.com/net/imaging/aspose.imaging/rasterimage) 实例，并以像素为单位指定图像宽度和高度。RasterImage 允许光栅操作。

第四步是你做任何你想做的图像修改。在该示例中，显示了一种执行直接逐像素颜色操作的方法。还有另一种方法，我稍后会解释。

Step 4.5 之所以如此命名，是因为调用 [SavePixels](https://apireference.aspose.com/net/imaging/aspose.imaging/rasterimage/methods/savepixels) 专用于这种图像处理方法，它直接将颜色数组写入图像内容，“像素”数组不是图像内容本身，而是一个副本。

最后，在步骤 5 中，保存图像。由于已经为图像源提供了相关的文件路径，并且从一开始就使用选项集创建了图像，因此不需要指定任何内容。

## 在图像上绘图

这是在 Aspose 中操作图像内容的另一种方法。成像 API。我们来修改一下前面的例子:
`

```
 using (Image image = Image.Create(saveOptions, 100, 100))
                {
                    // Create and initialize an instance of Graphics class and clear Graphics surface
                    Graphics graphic = new Graphics(image);
                    graphic.Clear(Color.Yellow);

                    // Initializes the instance of PEN class with black color and width
                    Pen BlackPen = new Pen(Color.Black, 3);
                    float startX = 10;
                    float startY = 25;
                    float controlX1 = 20;
                    float controlY1 = 5;
                    float controlX2 = 55;
                    float controlY2 = 10;
                    float endX = 90;
                    float endY = 25;

                    // Draw a Bezier shape by specifying the Pen object having black color and co-ordinate Points and save all changes.
                    graphic.DrawBezier(BlackPen, startX, startY, controlX1, controlY1, controlX2, controlY2, endX, endY);
                    image.Save();
                } 
```

Enter fullscreen mode Exit fullscreen mode

这里我们看到了 [Aspose 的用法。成像.图形](https://apireference.aspose.com/net/imaging/aspose.imaging/graphics)类。其 API 与[系统的 API 本质相同。Drawing.Graphics](https://msdn.microsoft.com/en-us/library/system.drawing.graphics(v=vs.110).aspx) 类并支持相同的操作，所以你会觉得自己很熟悉它。可以画贝塞尔曲线，直线，圆弧等。这边走。要保存图像，只需在图像实例上调用 save——图形直接作用于图像内容，因此不需要额外的 Save 调用。

目前就这些，敬请关注！
欲了解更多示例，请访问[网站。成像 GitHub](https://github.com/aspose-imaging) 页面。Aspose.Imaging 上也有 [Twitter](https://twitter.com/AsposeImaging) 和[脸书](https://www.facebook.com/AsposeImaging-1702883649750052)的新闻页面