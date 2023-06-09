# Aspose 的有趣特征。成像

> 原文：<https://dev.to/nnevod/interesting-features-of-asposeimaging-15h7>

你好，在这篇文章中，我将列举出[与众不同的地方。从其他类似的软件中获取图片](https://products.aspose.com/imaging/),并大致介绍其有趣的功能。

## 统一 API 为。net 2.0 以上和 Java

阿斯波斯。成像是为。Net 和 Java 来自同一个源代码，因此这两个版本是相同的，并且具有相同的 API。API 的文档在有两个版本，并根据的惯例设计。Net 和 Java 生态系统。除了造型没什么区别。请注意，在这篇文章和我提到的其他文章中。文件的. Net 版本。

## 云版本 via REST API

有一个公共的 REST API 服务允许您使用 Aspose。成像，上传文件到 API 主机，处理它们并下载回来。请参见[处的文档。影像云文档](https://docs.aspose.cloud/display/imagingcloud/Home)。

## ICC 颜色配置文件支持

阿斯波斯。Imaging 可以使用指定的颜色配置文件转换图像。TIFF、PSD 和 JPEG 图像支持颜色配置文件(当使用 YCCK 或 CMYK 颜色模式时)。在[用 Aspose 将 RGB TIFF 转换为 CMYK TIFF 中可以找到一个使用 TIFF 图像颜色配置文件的例子。成像 18.3](https://dev.to/nnevod/converting-rgb-tiff-to-cmyk-tiff-with-asposeimaging-183-njn) 篇。

### 灰度 ICC 配置文件支持

灰度 ICC 配置文件可以通过 PsdImage 的 GrayColorProfile 属性访问。参见示例:

```
 using (PsdImage psdImage = (PsdImage)Image.Load("gray-d15.psd"))
    {
        //StreamSource cmykProfile = psdImage.CmykColorProfile;
        //StreamSource rgbProfile = psdImage.RgbColorProfile;
        StreamSource grayProfile = psdImage.GrayColorProfile;

        // Save to grayscale TIFF
        TiffOptions saveOptions = new TiffOptions(TiffExpectedFormat.Default);
        saveOptions.Photometric = TiffPhotometrics.MinIsBlack;
        saveOptions.BitsPerSample = new ushort[] { 8 };

        // No ICC profile
        psdImage.Save("gray-d15.psd.noprofile.tif", saveOptions);

        // Embed ICC profile
        saveOptions.IccProfile = ToMemoryStream(grayProfile);
        psdImage.Save("gray-d15.psd.tif", saveOptions);
    } 
```

Enter fullscreen mode Exit fullscreen mode

## 多线程支持。

由 Aspose 加载的所有图像。成像是独立的实例，可以毫无问题地同时处理。不过，对单个图像的操作应该只发生在一个线程中。

## OpenDocument 图形(ODG)支持

阿斯波斯。成像可以加载 ODG 图像并将它们导出到光栅。与任何其他可读格式一样，这些文件加载有[图像。Load()](https://apireference.aspose.com/net/imaging/aspose.imaging/image/methods/load/index) 方法。

## 栅格化 SVG 和图元文件(EMF，WMF)矢量图像

这些格式也可以通过[图像加载。Load()](https://apireference.aspose.com/net/imaging/aspose.imaging/image/methods/load/index) 方法，然后保存到任何支持的导出格式。

```
public void SvgToPng()
{
    string path = "in.svg";
    string destFilePath = "out.png";
    using (Image image = Image.Load(path))
    {
        image.Save(destFilePath, new PngOptions());
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 支持 DICOM 和 DJVU

作为导入格式，它们受到支持。图像处理还不能导出它们。多页支持适用于这两种格式，您可以选择要导出的页面。详细示例在[Aspose 简介中提供。成像，第 2 部分](https://dev.to/nnevod/introduction-to-asposeimaging-part-2-40p)文章。

## 将图像保存为 PDF 格式。

加载的图像可以导出为 PDF 格式。请注意，仅支持 PDF 文件的输出，如 Aspose。成像不读取它们，那是另一个产品 Aspose.PDF 的工作。作为任何导出，可以通过创建 [PdfOptions](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/pdfoptions) 实例并将其传递给[映像来选择它。保存()](https://apireference.aspose.com/net/imaging/aspose.imaging/image/methods/save/index)。下面是例子:

```
public void GifToPdf()
{
    string path = "transparent_orig.gif";
    string destFilePath = "transparent_orig.gif.pdf";
    using (Image image = Image.Load(path))
    {
        image.Save(destFilePath, new PdfOptions());
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## XMP 和 EXIF 元数据支持。

这两种元数据格式都可以用 Aspose.Imaging 读取和操作。
参见[Aspose 简介中使用 XMP 元数据的示例。成像，第五部分](https://dev.to/nnevod/introduction-to-asposeimaging-part-5-1349)文章。

## 大图支持

阿斯波斯。图像支持处理不适合 RAM 的图像，允许您即使在相对较弱的计算机上也能处理非常大的图像文件。

### 每通道 16 位 TIFF 支持

从 18.6 版本开始，Aspose。成像支持 16 位通道的 TIFF 文件。然而，支持尚未完成，ICC 配置文件目前尚未应用。有一个关于如何提取每通道值的示例:

```
// ICC profile is not applied for 16-bit color components at the moment, so disable that option explicitly.
LoadOptions loadOptions = new LoadOptions();
loadOptions.UseIccProfileConversion = false;

Rectangle desiredArea = new Rectangle(470, 1350, 30, 30);

using (RasterImage image = (RasterImage)Image.Load(tiff16File, loadOptions))
{
    long[] colors64Bit = image.LoadArgb64Pixels(image.Bounds);

    ushort alpha, red, green, blue;
    for (int y = desiredArea.Top; y < desiredArea.Bottom; ++y)
    {
        for (int x = desiredArea.Left; x < desiredArea.Right; ++x)
        {
            int offset = y * image.Width + x;
            long color64 = colors64Bit[offset];

            alpha = (ushort)((color64 >> 48) & 0xffff);
            red = (ushort)((color64 >> 32) & 0xffff);
            green = (ushort)((color64 >> 16) & 0xffff);
            blue = (ushort)(color64 & 0xffff);

            Console.WriteLine("A={0}, R={1}, G={2}, B={3}", alpha, red, green, blue);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### DNG 支持

DNG 文件格式可以用 Aspose 读取。照片图像的无损处理成像。与任何其他图像一样，只需调用 [Image。Load()](https://apireference.aspose.com/net/imaging/aspose.imaging/image/methods/load/index) 。

### 无损 JPEG 和 JPEG-LS 支持

这些是 JPEG 文件很少实现的压缩方法。阿斯波斯。成像允许通过[jpe options](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/jpegoptions)的 [CompressionType](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/jpegoptions/properties/compressiontype) 属性设置压缩类型。此外，还支持基线和渐进压缩方法。

### 支持 Jpeg2000。

这也是一种很少被支持的格式。阿斯波斯。图像处理支持 Jpeg2000 图像的导入和导出。与任何其他输出格式一样，可以通过创建 [Jpeg2000Options](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/jpeg2000options) 实例并将其传递给[图像来选择导出。保存()](https://apireference.aspose.com/net/imaging/aspose.imaging/image/methods/save/index)方法。

### 支持导出 EMF 图像

EMF 图像可以通过 Aspose.Imaging 导出。参见如何使用它们的示例:

```
public void TestSaveEmf()
{
    var path = @"TestEmfBezier.emf";
    using (var image = (MetaImage)Image.Load(path))
    {
        image.Save(path + ".emf", new EmfOptions());
    }
}

public void TestSaveEmfPlus()
{
    var path = @"TestEmfPlusFigures.emf";
    using (var image = (MetaImage)Image.Load(path))
    {
        image.Save(path + ".emf", new EmfOptions());
    }
}

public void TestSaveEmfGraphics()
{
    EmfRecorderGraphics2D graphics = new EmfRecorderGraphics2D(
        new Rectangle(0, 0, 5000, 5000),
        new Size(5000, 5000),
        new Size(1000, 1000));
    {
        Font font = new Font("Arial", 10, FontStyle.Bold | FontStyle.Underline);
        graphics.DrawString(font.Name + " " + font.Size + " " + font.Style.ToString(), font, Color.Brown, 10, 10);
        graphics.DrawString("some text", font, Color.Brown, 10, 30);

        font = new Font("Arial", 24, FontStyle.Italic | FontStyle.Strikeout);
        graphics.DrawString(font.Name + " " + font.Size + " " + font.Style.ToString(), font, Color.Brown, 20, 50);
        graphics.DrawString("some text", font, Color.Brown, 20, 80);

        using (EmfImage image = graphics.EndRecording())
        {
            var path = @"Fonts.emf";
            image.Save(path, new EmfOptions());
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 特定图像格式支持优化

阿斯波斯。Imaging 支持有损 GIF 以减小 GIF 图像的大小。
对于 JPEG，率失真优化用于优化压缩过程中的图像质量。

## 即将到来

### [T1。净标准](#net-standard)

。Net 标准将在不久的将来得到支持，因此将会有一个带有 native 的库版本。净核心支持。

### 反向图像搜索

阿斯波斯。云成像(即公共 REST API)将很快支持反向图像搜索！

## 逐渐消失

### PSD 支持

PSD 支持将移至自己的产品 Aspose.PSD 中。

就目前而言，仅此而已。本文以后会有新老有趣的功能更新。