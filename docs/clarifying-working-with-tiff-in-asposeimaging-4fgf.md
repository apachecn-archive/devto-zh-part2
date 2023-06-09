# 阐明在 Aspose 中使用 TIFF。成像

> 原文：<https://dev.to/nnevod/clarifying-working-with-tiff-in-asposeimaging-4fgf>

提供对  格式的支持，这种格式被认为是光栅数据交换的“经典”格式，最初用于扫描和传真，现在用于许多多边形、科学和一般图像处理应用，这些应用比面向最终用户的 JPEG 和 PNG 等常见图像格式要求更高的精度和可调整性。

考虑到 TIFF 格式的灵活性，如何在一般情况下和 Aspose 中正确使用这种格式可能并不明显。成像也一样。因此，本文将解释 TIFF 的一些基本概念以及如何在 Aspose.Imaging 中使用它。还有一篇文章将深入探讨 Aspose.Imaging 中 TIFF 可用的选项

## 一个文件中的多个图像

与大多数其他光栅图像格式不同，TIFF 本质上是多个独立光栅图像的容器，每个图像都有自己的标题标签集，描述图像的不同属性:大小、清晰度、压缩、数据排列-像素顺序、字节顺序、每像素位数/通道数等。同样地，在某种程度上。图像，这些包含的图像由一个特定的 [TiffFrame](https://apireference.aspose.com/net/imaging/aspose.imaging.fileformats.tiff/tiffframe) 类表示，该类公开了大多数特定于 TIFF 的属性。 [TiffImage](https://apireference.aspose.com/net/imaging/aspose.imaging.fileformats.tiff/tiffimage/) 类在 [Frames](https://apireference.aspose.com/net/imaging/aspose.imaging.fileformats.tiff/tiffimage/properties/frames) 属性中包含一组  实例。TiffImage 类还必须公开一些所有栅格格式通用的属性，例如 size，但它们被映射到 [ActiveFrame](https://apireference.aspose.com/net/imaging/aspose.imaging.fileformats.tiff/tiffimage/properties/activeframe) ，这是 Frames 属性中被选择为活动的 TiffFrame 实例之一，默认情况下，它是 Frames 数组的第一个元素。当您将 TiffImage 实例作为 [RasterImage](https://apireference.aspose.com/net/imaging/aspose.imaging/rasterimage) 或 [Image](https://apireference.aspose.com/net/imaging/aspose.imaging/image) 访问时，也会发生同样的情况。
照此，在 Aspose。如果你真的想处理 TIFF 特有的属性，TiffFrame 是你必须处理的，而 TiffImage 只是一个带有一些兼容性措施的集合。您可以像处理其他图像的子类一样处理它，但是首先选择您想要处理的特定活动框架。
使用 TiffFrame 和 ActiveFrame 的例子:

```
// Create instances of FileStream and initialize with Tiff images
FileStream fileStream = new FileStream("TestDemo.tif", FileMode.Open);
FileStream fileStream1 = new FileStream("sample.tif", FileMode.Open);

// Create an instance of TiffImage and load the destination image from filestream
using (TiffImage image = (TiffImage) Image.Load(fileStream))
{
    // Create an instance of TiffImage and load the source image from filestream
    using (TiffImage image1 = (TiffImage) Image.Load(fileStream1))
    {
        // Create an instance of TIffFrame and copy active frame of source image
        TiffFrame frame = TiffFrame.CopyFrame(image1.ActiveFrame);

        // Add copied frame to destination image
        image.AddFrame(frame);
    }

    // Save the image with changes
    image.Save("ConcatenatingTIFFImagesfromStream_out.tif");

    // Close the FileStreams
    fileStream.Close();
    fileStream1.Close();
} 
```

## 创建 TiffImage 和 TiffFrame

因此，TiffImage 的创建方式与其他 RasterImage 不同。您可以使用无参数的构造函数创建空的 TiffImage，或者使用接受单个 TiffFrame 的构造函数创建包含单个栅格的 TiffImage，或者使用接受 TiffFrame 数组的构造函数创建包含多个栅格的 TiffImage。实际选项，如像素格式，图像大小等。是为 TiffFrame 保留的。

TiffFrame 是一个真实的图像，其创建更像其他 RasterImage 的后代:您可以从现有的图像创建一个新的图像(通过图像文件的路径、通过图像文件的流或已经加载的 RasterImage 实例)，您可以指定 [TiffOptions](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/) 和尺寸来创建一个全新的空图像，并从现有的图像创建新的图像，同时指定 TiffOptions(也有三个如何指定源的变量)，以便您可以将图像光栅数据加载到所需的 TIFF 格式。

```
// Create an instance of TiffOptions and set its various properties
// The options themselves will be described in following article on TiffOptions
TiffOptions options = new TiffOptions(TiffExpectedFormat.Default);
options.BitsPerSample = new ushort[] { 8, 8, 8 };
options.Photometric = TiffPhotometrics.Rgb;
options.Xresolution = new TiffRational(72);
options.Yresolution = new TiffRational(72);
options.ResolutionUnit = TiffResolutionUnits.Inch;
options.PlanarConfiguration = TiffPlanarConfigs.Contiguous;

// Set the Compression to AdobeDeflate
options.Compression = TiffCompressions.AdobeDeflate;

// Or Deflate 
// Options.Compression = TiffCompressions.Deflate;

// Create a new TiffImage with specific size and TiffOptions settings
// via creating TiffFrame with that size and options
using (TiffImage tiffImage = new TiffImage(new TiffFrame(options, 100, 100)))
{
    // Loop over the pixels to set the color to red
    for (int i = 0; i < 100; i++)
    {
        tiffImage.ActiveFrame.SetPixel(i, i, Color.Red);
    }
    // Save resultant image
    tiffImage.Save("output.tiff");
} 
```

## TiffFrame 属性

TiffFrame 实际上是 RasterImage(或者更具体地说，  )的后代，因此，在很大程度上，它可以像任何其他 RasterImage 实例一样进行处理。
与 RasterImage 的不同之处在于，TiffFrame 有 [ExifData](https://apireference.aspose.com/net/imaging/aspose.imaging.fileformats.tiff/tiffframe/properties/exifdata) 属性，它包含 EXIF 元数据作为 ExifData 实例，以及 [FrameOptions](https://apireference.aspose.com/net/imaging/aspose.imaging.fileformats.tiff/tiffframe/properties/frameoptions) 属性，它包含描述框架的 TIFF 相关属性的 TiffOptions 实例。 [GetOriginalOptions](https://apireference.aspose.com/net/imaging/aspose.imaging/image/methods/getoriginaloptions) 方法也是如此，只是没有显式地类型化为 TiffOptions。如果您需要获取图像的 TIFF 格式属性，这是您想要使用的。
此外，TiffFrame 还有两个新方法:

#### [相框](https://apireference.aspose.com/net/imaging/aspose.imaging.fileformats.tiff/tiffframe/methods/copyframe)

创建框架的相同副本。这已经在上面的例子中使用。

#### [从](https://apireference.aspose.com/net/imaging/aspose.imaging.fileformats.tiff/tiffframe/methods/createframefrom)创建框架

创建一个新的框架，像 CopyFrame，但是具有在 TiffOptions 中指定的属性，所以你可以用它来转换成不同的像素格式。

获取 TIFF 帧压缩方法的例子:

```
using (TiffImage tiff = (TiffImage)Image.Load("TiffCompressed.tiff"))
{
    foreach(TiffFrame frame in tiff.Frames)
    {
        Console.WriteLine(frame.FrameOptions.Compression.ToString());
    }
} 
```

## t 图像属性

如前所述，TiffImage 保留了 RasterImage 的大部分方法和属性，但只将它们应用于当前的 ActiveFrame。大多数特定于 TiffImage 的添加都与 TiffImage 是一个图像集合有关。唯一的例外是 [ByteOrder](https://apireference.aspose.com/net/imaging/aspose.imaging.fileformats.tiff/tiffimage/properties/byteorder) 属性，它定义了文件中数据的字节顺序，并应用于文件的所有内容，因此 TiffFrame 没有该属性。TiffOptions 确实指定了该属性，因为 TIFF 图像也可以通过使用带有 TiffOptions 实例的 [Save()](https://apireference.aspose.com/net/imaging/aspose.imaging.image/save/methods/3) 方法保存任何格式的加载图像来创建，而不是通过逐帧的方法。在这种情况下，文件中的所有帧将具有相同的参数。另外两个属性已经在 ActiveFrame 和 Frames 中描述过。
TiffImage 的新方法还没有被描述，所以它们是:

#### [添加](https://apireference.aspose.com/net/imaging/aspose.imaging.fileformats.tiff/tiffimage/methods/add)

将提供的 TiffImage 的所有帧添加到当前图像的帧数组的末尾。

#### [AddFrame](https://apireference.aspose.com/net/imaging/aspose.imaging.fileformats.tiff/tiffimage/methods/addframe)

将提供的 TiffFrame 添加到当前图像的帧数组的末尾。

#### [添加帧](https://apireference.aspose.com/net/imaging/aspose.imaging.fileformats.tiff/tiffimage/methods/addframes)

将提供的 TiffFrame 数组添加到当前图像的 Frames 数组的末尾。

#### [插入帧](https://apireference.aspose.com/net/imaging/aspose.imaging.fileformats.tiff/tiffimage/methods/insertframe)

将提供的 TiffFrame 插入到框架数组的指定索引中。

#### [移除帧](https://apireference.aspose.com/net/imaging/aspose.imaging.fileformats.tiff.tiffimage/removeframe/methods/1)

通过索引或通过[实例](https://apireference.aspose.com/net/imaging/aspose.imaging.fileformats.tiff/tiffimage/methods/removeframe)移除帧。

还有几个辅助方法可以同时作用于所有帧:

#### [对齐分辨率](https://apireference.aspose.com/net/imaging/aspose.imaging.fileformats.tiff/tiffimage/methods/alignresolutions)

使所有帧的垂直和水平分辨率相等

#### [调整大小比例](https://apireference.aspose.com/net/imaging/aspose.imaging.fileformats.tiff/tiffimage/methods/resizeproportional)

根据 newWidth/width 和 newHeight/height 的比例调整所有图像帧的大小。

#### [t1](#rotateflipall)[旋转挡板](https://apireference.aspose.com/net/imaging/aspose.imaging.fileformats.tiff/tiffimage/methods/rotateflipall)

旋转/翻转图像的所有帧。

## 从 TIFF 中提取帧并保存为单独的图像

下面是一个如何将不同帧的 TIFF 保存到不同 BMP 文件的例子:

```
using (TiffImage multiImage = (TiffImage)Image.Load("SampleTiff1.tiff"))
{
    // Create an instance of int to keep track of frames in TiffImage
    int frameCounter = 0;

    // Iterate over the TiffFrames in TiffImage
    foreach (TiffFrame tiffFrame in multiImage.Frames)
    {
        multiImage.ActiveFrame = tiffFrame;

        // Load Pixels of TiffFrame into an array of Colors
        Color[] pixels = multiImage.LoadPixels(tiffFrame.Bounds);

        // Create an instance of bmpCreateOptions
        BmpOptions bmpCreateOptions = new BmpOptions();
        bmpCreateOptions.BitsPerPixel = 24;

        // Set the Source of bmpCreateOptions as FileCreateSource by specifying the location where output will be saved
        bmpCreateOptions.Source = new FileCreateSource(string.Format("ConcatExtractTIFFFramesToBMP_out{0}.bmp", frameCounter), false);

        // Create a new bmpImage
        using (BmpImage bmpImage = (BmpImage)Image.Create(bmpCreateOptions, tiffFrame.Width, tiffFrame.Height))
        {
            // Save the bmpImage with pixels from TiffFrame
            bmpImage.SavePixels(tiffFrame.Bounds, pixels);
            bmpImage.Save();
        }
        frameCounter++;
    }
} 
```

目前就说这么多，接下来的文章会深入描述 TiffOptions！

如需更多示例，请访问[网站。成像 GitHub](https://github.com/aspose-imaging) 页面。Aspose.Imaging 上还有 [Twitter](https://twitter.com/Asposeimaging) 和[脸书](https://www.facebook.com/AsposeImaging-1702883649750052/)的新闻页面