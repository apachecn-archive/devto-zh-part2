# 阿司匹林的最新进展。成像

> 原文：<https://dev.to/nnevod/recent-progress-of-asposeimaging-1107>

在这篇文章中，我将讲述最近对 Aspose 的显著改进。成像。

### 固定保存基线 JPEG 到渐进。

以前，如果您读取一个基准 JPEG 图像，并试图将其导出为渐进式 JPEG，您会得到一个异常。现在这个错误被修复了，你可以这样做了:

```
using (Image image = Image.Load("interleaved.jpg"))
{
    JpegOptions saveOptions = new JpegOptions();
    saveOptions.CompressionType = JpegCompressionMode.Progressive;

    image.Save(dir + "interleaved_progressive.jpg", saveOptions);
} 
```

### 不正确的电动势创建。

以前，EMF 头中的图形元素计数设置不正确，导致最后添加的图形元素的表示不正确。错误已修复，emf 已正确创建。

### 导出到 SVG 时虚线另存为实线。

当包含虚线的源文件被保存到 SVG 时——无论是矢量图形文件还是新创建的图元集，它们都作为实线导出到 SVG。像蜘蛛一样。成像 18.9，针对授权模式修复了这个问题。然而，当无照经营时，线路仍然会被转换为实线。这个问题应该尽快解决。成像 18.10 版本。

### JPEG 编码器优化。

JPEG DCT 编码器经过优化，性能提升了 50%(=编码时间缩短了 30%)。

### 导出到 BMP 时 DICOM 失去颜色。

使用无损 JPEG 压缩的具有 RGB 色彩空间的 DICOM 文件在导出到 BMP 时被导出为灰度(实际上，它们被读取为灰度)。现在这个问题已经解决了，输出 JPEG 应该是正确的。

目前就这些，敬请关注！

如需更多示例，请访问[网站。成像 GitHub](https://github.com/aspose-imaging) 页面。Aspose.Imaging 上还有 [Twitter](https://twitter.com/Asposeimaging) 和[脸书](https://www.facebook.com/AsposeImaging-1702883649750052/)的新闻页面