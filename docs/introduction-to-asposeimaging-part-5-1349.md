# Aspose 简介。成像，第 5 部分

> 原文：<https://dev.to/nnevod/introduction-to-asposeimaging-part-5-1349>

[第一部分](https://dev.to/nnevod/introduction-to-asposeimaging-8jd)

[第二部分](https://dev.to/nnevod/introduction-to-asposeimaging-part-2-40p)

[第三部分](https://dev.to/nnevod/introduction-to-asposeimaging-part-3-3ah3)

[第四部分](https://dev.to/nnevod/introduction-to-asposeimaging-part-4-2o1j)

在这篇关于[的文章中。成像](https://products.aspose.com/imaging/)，我将讲述自动灰度特性的使用，这是我在以前的文章中忘记包括的，最重要的是，如何在 Aspose.Imaging 中使用 XMP 元数据

## 灰度

这是尽可能简单的，任何一个 [RasterImage](https://apireference.aspose.com/net/imaging/aspose.imaging/rasterimage) 的后代都有一个[灰度](https://apireference.aspose.com/net/imaging/aspose.imaging/rasterimage/methods/grayscale)方法，很明显，这个方法把图像变成灰度。

```
// Load an image in an instance of Image
using (Image image = Image.Load("aspose-logo.jpg"))
{
    // Cast the image to RasterCachedImage and Check if image is cached
    RasterCachedImage rasterCachedImage = (RasterCachedImage)image;               
    if (!rasterCachedImage.IsCached)
    {
        // Cache image if not already cached
        rasterCachedImage.CacheData();
    }

    // Transform image to its grayscale representation and Save the resultant image
    rasterCachedImage.Grayscale();
    rasterCachedImage.Save("Grayscaling_out.jpg");
} 
```

Enter fullscreen mode Exit fullscreen mode

## XMP 元数据

用 Aspose 表示的 XMP 元数据。图像被组织成几个“包”,包含在 XMP 包包装中。包对应于用例，由父 [XmpPackage](https://apireference.aspose.com/net/imaging/aspose.imaging.xmp/xmppackage) 类和这些用例的子类表示: [DublinCorePackage](https://apireference.aspose.com/net/imaging/aspose.imaging.xmp.schemas.dublincore/dublincorepackage) ， [PdfPackage](https://apireference.aspose.com/net/imaging/aspose.imaging.xmp.schemas.pdf/pdfpackage) ， [PhotoshopPackage](https://apireference.aspose.com/net/imaging/aspose.imaging.xmp.schemas.photoshop/photoshoppackage) ， [XmpBasicPackage](https://apireference.aspose.com/net/imaging/aspose.imaging.xmp.schemas.xmpbaseschema/xmpbasicpackage) ，[xmpdynamicmediapache](https://apireference.aspose.com/net/imaging/aspose.imaging.xmp.schemas.xmpdm/xmpdynamicmediapackage)，[XmpMediaManagementPackage](https://apireference.aspose.com/net/imaging/aspose.imaging.xmp.schemas.xmpmm/xmpmediamanagementpackage)和[XmpRightsManagementPackage](https://apireference.aspose.com/net/imaging/aspose.imaging.xmp.schemas.xmprm/xmprightsmanagementpackage)。它们中的每一个都有与其用例相对应的属性。

Packet wrapper，即顶层元数据对象，由 [XmpPacketWrapper](https://apireference.aspose.com/net/imaging/aspose.imaging.xmp/xmppacketwrapper) 类表示。包是通过 XmpPacketWrapper 实例的 [AddPackage](https://apireference.aspose.com/net/imaging/aspose.imaging.xmp/xmppacketwrapper/methods/addpackage) 方法附加到它上面的。在添加到 XmpPacketWrapper 之前，必须创建包的实例并设置其属性。
XmpPacketWrapper 本身是使用其他三个类的实例创建的: [XmpHeaderPi](https://apireference.aspose.com/net/imaging/aspose.imaging.xmp/xmpheaderpi) ，它包含这个元数据集合的 GUID， [XmpMeta](https://apireference.aspose.com/net/imaging/aspose.imaging.xmp/xmpmeta) ，它包含通用元数据并且是可选的， [XmpTrailerPi](https://apireference.aspose.com/net/imaging/aspose.imaging.xmp/xmptrailerpi) ，它包含允许或不允许后续处理器修改 XMP 数据的标志。然后将 XmpPacketWrapper 的
实例放入 [RasterImage](https://apireference.aspose.com/net/imaging/aspose.imaging/rasterimage) 类的后代实例的 [XmpData](https://apireference.aspose.com/net/imaging/aspose.imaging/rasterimage/properties/xmpdata) 属性中。
相反，要读取元数据，从加载的图像的 XmpData 属性获取 XmpPacketWrapper 实例，要获取包，使用实例的 [Packages](https://apireference.aspose.com/net/imaging/aspose.imaging.xmp/xmppacketwrapper/properties/packages) 属性。
以下是创建 XMP 元数据并将其嵌入 TIFF 文件的示例:

```
 // Specify the size of image by defining a Rectangle 
Rectangle rect = new Rectangle(0, 0, 100, 200);
TiffOptions tiffOptions = new TiffOptions(TiffExpectedFormat.TiffJpegRgb);
tiffOptions.Photometric = TiffPhotometrics.MinIsBlack;
tiffOptions.BitsPerSample = new ushort[] { 8 };

// Create the brand new image just for sample purposes
using (var image = new TiffImage(new TiffFrame(tiffOptions, rect.Width, rect.Height)))
{
    // Create an instance of XMP-Header
    XmpHeaderPi xmpHeader = new XmpHeaderPi(Guid.NewGuid().ToString());

    // Create an instance of Xmp-TrailerPi, XMPmeta class to set different attributes
    XmpTrailerPi xmpTrailer = new XmpTrailerPi(true);
    XmpMeta xmpMeta = new XmpMeta();
    xmpMeta.AddAttribute("Author", "Mr Smith");
    xmpMeta.AddAttribute("Description", "The fake metadata value");

    // Create an instance of XmpPacketWrapper that contains all metadata
    XmpPacketWrapper xmpData = new XmpPacketWrapper(xmpHeader,xmpTrailer, xmpMeta);

    // Create an instacne of Photoshop package and set photoshop attributes
    PhotoshopPackage photoshopPackage =  new PhotoshopPackage();
    photoshopPackage.SetCity("London");
    photoshopPackage.SetCountry("England");
    photoshopPackage.SetColorMode(ColorMode.Rgb);
    photoshopPackage.SetCreatedDate(DateTime.UtcNow);

    // Add photoshop package into XMP metadata
    xmpData.AddPackage(photoshopPackage);

    // Create an instacne of DublinCore package and set dublinCore attributes
    DublinCorePackage dublinCorePackage = new DublinCorePackage();
    dublinCorePackage.SetAuthor("Charles Bukowski");
    dublinCorePackage.SetTitle("Confessions of a Man Insane Enough to Live With the Beasts");
    dublinCorePackage.AddValue("dc:movie", "Barfly");

    // Add dublinCore Package into XMP metadata
    xmpData.AddPackage(dublinCorePackage);

    //Add metadata to image and save
    image.XmpData = xmpData;
    image.Save("XmpSample.tiff");
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，为了读取创建的数据，请看下一个例子:

```
using (var img = (TiffImage)Image.Load("XmpSample.tiff"))
{
    // Getting the XMP metadata
    XmpPacketWrapper imgXmpData = img.XmpData;

    foreach (XmpPackage package in imgXmpData.Packages)
    {
        // Use package data ...
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

目前就这些，敬请关注！

如需更多示例，请访问[网站。成像 GitHub](https://github.com/aspose-imaging) 页面。Aspose.Imaging 上还有 [Twitter](https://twitter.com/Asposeimaging) 和[脸书](https://www.facebook.com/AsposeImaging)的新闻页面