# 在 Aspose 中使用 ICC 配置文件。成像

> 原文：<https://dev.to/nnevod/working-with-icc-profiles-in-asposeimaging-1n30>

[Aspose。成像](https://products.aspose.com/imaging/)支持检索、应用和存储 PSD 和 JPEG 图像的 ICC 色彩配置文件，并且可以在导出 TIFF 图像时嵌入色彩配置文件。对于其他格式，在加载图像时会处理嵌入的颜色配置文件，但从 v18.7 开始不可操作。在本文中，我将展示如何处理 JPEG、PSD 和 TIFF 的 ICC 配置文件。

### 加载和保存配置文件的帮助方法

存储在 PSD 和 JPEG 图像中的 ICC 颜色配置文件被表示为类型为 [StreamSource](https://apireference.aspose.com/net/imaging/aspose.imaging.sources/streamsource/) 的属性，这有助于直接从/向其加载/保存它们。icc(m)文件。
在本节中，我将提供将在以下部分使用的方法。以下是从文件创建流源并将流源保存到文件的方法:

```
using Aspose.Imaging.Sources;
using Aspose.Imaging.ImageOptions;
using System.IO;

private static StreamSource GetSourceForFilename(string filename)
{
    MemoryStream memory = new MemoryStream(File.ReadAllBytes(filename));
    memory.Seek(0, System.IO.SeekOrigin.Begin);
    StreamSource source = new StreamSource(memory);
    return source;
}

private static void SaveSourceToFile(StreamSource source, string filename)
{
    using (FileStream fileStream = new FileStream(filename, FileMode.Create))
    {
        long position = source.Stream.Position;
        source.Stream.Seek(0, System.IO.SeekOrigin.Begin);
        int byteCount;
        byte[] buffer = new byte[1024];
        while ((byteCount = source.Stream.Read(buffer, 0, buffer.Length)) > 0)
        {
            fileStream.Write(buffer, 0, byteCount);
        }
        source.Stream.Seek(position, System.IO.SeekOrigin.Begin);
        fileStream.Flush();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在 TIFF 文件的情况下，颜色配置文件在保存图像期间被应用，并且在 [TiffOptions](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/) 实例的 [IccProfile](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/iccprofile) 属性中作为 [MemoryStream](https://docs.microsoft.com/en-us/dotnet/api/system.io.memorystream?redirectedfrom=MSDN&view=netframework-4.7.2) 的实例被提供。下面是将 [StreamSource](https://apireference.aspose.com/net/imaging/aspose.imaging.sources/streamsource/) 转换为 MemoryStream 并将文件加载到 MemoryStream 的方法:

```
private static MemoryStream FileToMemoryStream(string filename)
{
    MemoryStream memory = new MemoryStream(File.ReadAllBytes(filename));
    memory.Seek(0, System.IO.SeekOrigin.Begin);
    return memory;
}

        private static MemoryStream SourceToMemoryStream(StreamSource streamSource)
{
    Stream srcStream = streamSource.Stream;
    MemoryStream dstStream = new MemoryStream();

    int byteCount;
    byte[] buffer = new byte[1024];
    long pos = srcStream.Position;
    srcStream.Seek(0, System.IO.SeekOrigin.Begin);
    while ((byteCount = srcStream.Read(buffer, 0, buffer.Length)) > 0)
    {
        dstStream.Write(buffer, 0, byteCount);
    }

    srcStream.Seek(pos, System.IO.SeekOrigin.Begin);
    return dstStream;
} 
```

Enter fullscreen mode Exit fullscreen mode

### 保存为 TIFF 时应用颜色配置文件

这很简单:只需用概要文件设置 [TiffOptions](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/) 的 [IccProfile](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/iccprofile) 属性，并保存图像:

```
using (Image image = Image.Load("sample.jpg"))
{
    TiffOptions options = new TiffOptions(FileFormats.Tiff.Enums.TiffExpectedFormat.Default);
    using(MemoryStream ms = FileToMemoryStream("profile.icc"))
    {
        options.IccProfile = ms;
        image.Save("profiled_sample.tif", options);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### PSD 中的颜色配置文件

[PSDImage](https://apireference.aspose.com/net/imaging/aspose.imaging.fileformats.psd/psdimage/) 中有三个颜色配置文件属性，其中两个应用于 CMYK 图像，必须一起使用才能正确转换，它们是 [RgbColorProfile](https://apireference.aspose.com/net/imaging/aspose.imaging.fileformats.psd/psdimage/properties/rgbcolorprofile) 和 [CmykColorProfile](https://apireference.aspose.com/net/imaging/aspose.imaging.fileformats.psd/psdimage/properties/cmykcolorprofile) 。默认情况下，如果这些配置文件嵌入在图像中，它们将在默认情况下在加载过程中应用。
以下是将 PSD 图像的 CMYK 色彩配置文件嵌入 CMYK TIFF 图像的示例:

```
string inputFile = "cmyk.psd";
string outputFile = "cmyk.tiff";

using (PsdImage image = (PsdImage)Image.Load(inputFile))
{
    TiffOptions tiff = new TiffOptions(FileFormats.Tiff.Enums.TiffExpectedFormat.TiffLzwCmyk);
    using (MemoryStream ms = SourceToMemoryStream(image.CmykColorProfile))
    {
        tiff.IccProfile = ms;
        image.Save(outputFile, tiff);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

第三个是[灰色轮廓](https://apireference.aspose.com/net/imaging/aspose.imaging.fileformats.psd/psdimage/properties/graycolorprofile)，用于单色图像，在加载过程中不应用。为了显式地启用概要文件的应用，创建 [LoadOptions](https://apireference.aspose.com/net/imaging/aspose.imaging/loadoptions/) 实例，将 [UseIccProfileConversion](https://apireference.aspose.com/net/imaging/aspose.imaging/loadoptions/properties/useiccprofileconversion) 设置为 **true** ，并使用该实例加载图像。参见示例:

```
// Save to grayscale TIFF
TiffOptions saveOptions = new TiffOptions(TiffExpectedFormat.Default);
saveOptions.setPhotometric(TiffPhotometrics.MinIsBlack);
saveOptions.setBitsPerSample(new int[] { 8 });

// If the image contains a built-in Gray ICC profile, it is not be applied by default in contrast of the CMYK profile.
// Enable ICC conversion explicitly.
LoadOptions loadOptions = new LoadOptions();
loadOptions.UseIccProfileConversion= true;

using(PsdImage psdImage = (PsdImage)Image.load(sourcePath, loadOptions))
{
    // Embed the gray ICC profile to the output TIFF.
    // The built-in Gray Profile can be read via the PsdImage.GrayColorProfile property.
    saveOptions.setIccProfile(toMemoryStream(psdImage.getGrayColorProfile()));
    psdImage.save(outputPath, saveOptions);
} 
```

Enter fullscreen mode Exit fullscreen mode

### JPEG 中的颜色配置文件

[JpegImage](https://apireference.aspose.com/net/imaging/aspose.imaging.fileformats.jpeg/jpegimage/) 包含四个与颜色配置文件相关的属性。 [RgbColorProfile](https://apireference.aspose.com/net/imaging/aspose.imaging.fileformats.jpeg/jpegimage/properties/rgbcolorprofile) 和 [CmykColorProfile](https://apireference.aspose.com/net/imaging/aspose.imaging.fileformats.jpeg/jpegimage/properties/cmykcolorprofile) 与 PSD 类似，在加载图像时用于 CMYK 和 YCCK JPEG 图像。[DestinationCmykColorProfile](https://apireference.aspose.com/net/imaging/aspose.imaging.fileformats.jpeg/jpegimage/properties/destinationcmykcolorprofile)和[DestinationRgbColorProfile](https://apireference.aspose.com/net/imaging/aspose.imaging.fileformats.jpeg/jpegimage/properties/destinationrgbcolorprofile)在保存 JpegImage 时应用，并应与对手配置文件配对-即当使用 DestinationCmykColorProfile 时，RgbColorProfile 应设置为相应的配置文件，当使用 DestinationRgbColorProfile 时，CmykColorProfile 也应设置为相应的配置文件。
以下是一个示例，展示了加载图像时配置文件的使用:

```
using (JpegImage image = (JpegImage)Image.Load("image.jpg"))
{
    //load profiles
    StreamSource rgbprofile = GetSourceForFilename("rgb.icc");
    StreamSource cmykprofile = GetSourceForFilename("cmyk.icc");
    // set profiles for image
    image.RgbColorProfile = rgbprofile;
    image.CmykColorProfile = cmykprofile;
    //now the profiles are applied when image's data is retrieved
    Aspose.Imaging.Color[] colors = image.LoadPixels(new Rectangle(0, 0, image.Width, image.Height));
} 
```

Enter fullscreen mode Exit fullscreen mode

以下是一个示例，展示了保存图像时配置文件的使用:

```
using (JpegImage image = (JpegImage)Image.Load("image.jpg"))
{
    //load profiles
    StreamSource rgbprofile = GetSourceForFilename("rgb.icc");
    StreamSource cmykprofile = GetSourceForFilename("cmyk.icc");
    // apply profiles to image for saving
    image.DestinationRgbColorProfile = rgbprofile;
    image.DestinationCmykColorProfile = cmykprofile;
    //save changes back to image
    image.Save();
} 
```

Enter fullscreen mode Exit fullscreen mode

### 禁用嵌入颜色配置文件的默认应用。

如果您不想在图像加载期间应用图像的嵌入颜色配置文件，可以选择使用 [LoadOptions](https://apireference.aspose.com/net/imaging/aspose.imaging/loadoptions/) 禁用此处理。参见例子:

```
LoadOptions loadOptions = new LoadOptions();
loadOptions.UseIccProfileConversion= false;

using(image = Image.load(sourcePath, loadOptions))
{
    image.save(outputPath, new PngOptions());
} 
```

Enter fullscreen mode Exit fullscreen mode

目前就这些，敬请关注！

如需更多示例，请访问[网站。成像 GitHub](https://github.com/aspose-imaging) 页面。Aspose.Imaging 上还有 [Twitter](https://twitter.com/Asposeimaging) 和[脸书](https://www.facebook.com/AsposeImaging)的新闻页面