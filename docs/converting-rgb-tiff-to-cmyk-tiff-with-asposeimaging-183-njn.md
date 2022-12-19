# 用 Aspose 将 RGB TIFF 转换为 CMYK TIFF。成像 18.3

> 原文：<https://dev.to/nnevod/converting-rgb-tiff-to-cmyk-tiff-with-asposeimaging-183-njn>

在 [Aspose。成像](https://products.aspose.com/imaging/) 18.3 出现了一个可能对一些人有用的新选项:从 RGB 色彩空间转换到 CMYK 色彩空间的能力。
要在 Cmyk 色彩空间中保存图像，您必须使用支持 CMYK 的文件格式(在本例中为 TIFF)，将其设置为使用 CMYK 色彩空间(在本例中通过创建 TiffLzwCmyk 值为 [TiffExpectedFormat](https://apireference.aspose.com/net/imaging/aspose.imaging.fileformats.tiff.enums/tiffexpectedformat) enum 的 [TiffOptions](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions) ，并提供一个映射到 CMYK 色彩空间的色彩配置文件。

下面是 C#中使用 Aspose 的完整示例。为…成像。NET 18.3:

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
            string sourceFilePath = "testTileDeflate.tif";
            string outputFilePath = "testTileDeflate Cmyk Icc.tif";
            string cmykProfilePath = "RSWOP.ICM";

            //Load the output color profile
            MemoryStream cmykProfile;
            using (FileStream stream = new FileStream(cmykProfilePath, FileMode.Open))
            {
                byte[] bytes = new byte[stream.Length];
                stream.Read(bytes, 0, (int)stream.Length);
                cmykProfile = new MemoryStream(bytes);
            }

            //Set up save file format parameters
            TiffOptions options = new TiffOptions(TiffExpectedFormat.TiffLzwCmyk);
            options.IccProfile = cmykProfile;

            //Load and save
            using (Image image = Image.Load(sourceFilePath))
            {
                image.Save(outputFilePath, options);
            }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

正如所见，使用 Aspose.Imaging 执行转换非常简单

如需更多示例，请访问[网站。成像 GitHub](https://github.com/aspose-imaging) 页面。Aspose.Imaging 上还有 [Twitter](https://twitter.com/AsposeImaging) 和[脸书](https://www.facebook.com/pg/AsposeImaging-1702883649750052)的新闻页面