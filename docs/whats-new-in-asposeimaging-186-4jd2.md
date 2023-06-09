# Aspose 中的新内容。成像 18.6

> 原文：<https://dev.to/nnevod/whats-new-in-asposeimaging-186-4jd2>

Aspose 的新版本。成像被释放，指定为 18.6。以下是最重要的变化:

## 新增功能

### 支持 Jpeg2000 文件格式的 JPT 编解码

这也是一个错误修复一次- JPT 编解码器选项是可用的，但没有做什么。参见示例:

```
using (Image img = Image.Load("test.j2k"))
{
   img.Save("test.jp2", new Jpeg2000Options()
   {
       Comments = new string[] { "Aspose" },
       Codec = Jpeg2000Codec.Jpt
   });
} 
```

Enter fullscreen mode Exit fullscreen mode

### 支持光栅图像到 PDF 的转换

有一个将 GIF 转换成 PDF 的代码示例:

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

### 支持 16 位 TIFF 格式

现在做个手势。成像支持加载 16 位每通道 TIFF 文件。然而，有一些限制，即 ICC 配置文件还不适用于 16 位通道..下面是一个例子，介绍了如何从 16 位 TIFFs 中提取通道值。

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

### 显式 alpha 预混控制支持向 JPG 出口。

现在可以强制禁用或启用阿尔法混合时，出口到 JPG 格式，混合可能是不可取的。为此， [PreblendAlphaIfPresent](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/jpegoptions/properties/preblendalphaifpresent) 属性被添加到[jpe options](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/jpegoptions)中。参见例子:

```
string sourcePath = "alphachannel.psd";            
string outputPath = "alphachannel_out.jpg";

PsdLoadOptions loadOptions = new PsdLoadOptions();
loadOptions.ReadOnlyMode = true;

JpegOptions saveOptions = new JpegOptions();
saveOptions.PreblendAlphaIfPresent = false; // Disable alpha blending!

using (PsdImage image = (PsdImage)Image.Load(sourcePath, loadOptions))
{
    image.Save(outputPath, saveOptions);
} 
```

Enter fullscreen mode Exit fullscreen mode

### 矢量格式光栅化的默认字体设置选项

已经有一个选项来设置缺省字体的 PSD 文件的默认字体，而不是它与其他矢量格式- EMF，ODG，SVG 和 WMF。参见示例:

```
FontSettings.DefaultFontName = "Comic Sans MS";

string[] files = new string[] { "missing-font.emf", "missing-font.odg", "missing-font.svg", "missing-font.wmf" };
VectorRasterizationOptions[] options = new VectorRasterizationOptions[] { new EmfRasterizationOptions(), new MetafileRasterizationOptions(), new SvgRasterizationOptions(), new WmfRasterizationOptions() };

for (int i = 0; i < files.Length; i++)
{
    string outFile = files[i] + ".png";
    using (Image img = Image.Load(files[i]))
    {
        options[i].PageWidth = img.Width;
        options[i].PageHeight = img.Height;
        img.Save(outFile, new PngOptions()
        {
            VectorRasterizationOptions = options[i]
        });
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

请注意， [Aspose。Imaging.FontSettings](https://apireference.aspose.com/net/imaging/aspose.imaging/fontsettings) 类还支持设置自定义字体搜索文件夹:

```
var fontFolder = @"c:\myFonts";
List<string> folders = new List<string>(FontSettings.GetFontsFolders());
folders.Add(fontFolder);
FontSettings.SetFontsFolders(folders.ToArray(), true);

//// processing

FontSettings.Reset(); 
```

Enter fullscreen mode Exit fullscreen mode

## Bug 修复

当 JFIF 设置为 JPG 出口时，背景颜色的变化是固定的。

修正了 PSD 到 TIFF 转换的 CMYK 颜色配置文件-以前的颜色与预期的有所不同。

敬请期待！

如需更多示例，请访问[网站。成像 GitHub](https://github.com/aspose-imaging) 页面。Aspose.Imaging 上还有 [Twitter](https://twitter.com/Asposeimaging) 和[脸书](https://www.facebook.com/AsposeImaging)的新闻页面