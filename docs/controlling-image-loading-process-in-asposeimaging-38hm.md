# 控制 Aspose 中的图像加载过程。成像

> 原文：<https://dev.to/nnevod/controlling-image-loading-process-in-asposeimaging-38hm>

[Aspose。成像](https://products.aspose.com/imaging/)提供控制图像加载过程的选项，例如是否应用 ICC 配置文件，或者允许多高的容错度。大多数选项都与 PSD 格式相关，但其他一些图像格式也有它们的选项，并且有一些选项是所有格式共有的。

### 使用加载选项

为图像加载过程指定选项很简单:为相应的图像格式创建一个 [LoadOptions](https://apireference.aspose.com/net/imaging/aspose.imaging/loadoptions) 类或其后代的实例，设置其属性并将其传递给[图像](https://apireference.aspose.com/net/imaging/aspose.imaging/image)的 [Load](https://apireference.aspose.com/net/imaging/aspose.imaging.image/load/methods/1) 方法。以下示例禁用加载时使用图像的嵌入式 ICC 配置文件处理图像光栅数据的默认行为。

```
LoadOptions loadOptions = new LoadOptions();
loadOptions.UseIccProfileConversion = false;

using(image = Image.load(sourcePath, loadOptions))
{
    image.save(outputPath, new PngOptions());
} 
```

Enter fullscreen mode Exit fullscreen mode

对于特定格式的选项，只需创建相应类的一个实例，并确保您加载的是该格式的图像，否则是一样的。

### 常用加载选项

所有格式通用的选项在基本的 [LoadOptions](https://apireference.aspose.com/net/imaging/aspose.imaging/loadoptions) 类中定义。共有三个可用选项:
[UseIccProfileConversion](https://apireference.aspose.com/net/imaging/aspose.imaging/loadoptions/properties/useiccprofileconversion)-用于强制禁用或强制启用图像的嵌入式 ICC 配置文件对图像的光栅数据的应用。
[DataBackgroundColor](https://apireference.aspose.com/net/imaging/aspose.imaging/loadoptions/properties/databackgroundcolor)-设置背景颜色。当原始像素颜色无法恢复时，背景色通常用于损坏的数据。
[数据恢复模式](https://apireference.aspose.com/net/imaging/aspose.imaging/datarecoverymode) -设置损伤容限模式。要么在出现任何错误时抛出异常，要么在数据一致且格式未被破坏的情况下尝试恢复数据，要么在不进行任何检查的情况下进行恢复。

### PSD 选项

PSD 作为一种复杂的格式，拥有最多的选择。除了常用选项， [PsdLoadOptions 增加了下一个选项:](https://apireference.aspose.com/net/imaging/aspose.imaging.imageloadoptions/psdloadoptions) [DefaultReplacementFont](https://apireference.aspose.com/net/imaging/aspose.imaging.imageloadoptions/psdloadoptions/properties/defaultreplacementfont)-如果系统中不存在文本层中指定的字体，则在光栅输出期间将使用该字体绘制文本。
[IgnoreAlphaChannel](https://apireference.aspose.com/net/imaging/aspose.imaging.imageloadoptions/psdloadoptions/properties/ignorealphachannel) -如果为真，则丢弃 aplha 数据。
[IgnoreTextLayerWidthOnUpdate](https://apireference.aspose.com/net/imaging/aspose.imaging.imageloadoptions/psdloadoptions/properties/ignoretextlayerwidthonupdate)-当您处理图像时，当文本更新时，是否忽略文本层的固定宽度。
[LoadEffectsResource](https://apireference.aspose.com/net/imaging/aspose.imaging.imageloadoptions/psdloadoptions/properties/loadeffectsresource)-是否应该加载效果资源。默认值为 false。如果设置了该选项，无论如何都只会应用受支持的效果。

[UseDiskForLoadEffectsResource](https://apireference.aspose.com/net/imaging/aspose.imaging.imageloadoptions/psdloadoptions/properties/usediskforloadeffectsresource)-默认情况下，效果资源从磁盘加载，如果您有足够的 RAM，您可以设置它以便效果资源将被加载到内存中，从而使处理速度更快。

### PNG 选项

[pnglodorations](https://apireference.aspose.com/net/imaging/aspose.imaging.imageloadoptions/pngloadoptions)增加了 [StrictMode](https://apireference.aspose.com/net/imaging/aspose.imaging.imageloadoptions/pngloadoptions/properties/strictmode) 选项，这是一个额外的容错设置，检查文件的任何关键块是否被截断。由于文件可以在缺少一些块的情况下加载，因此默认设置为 false。

### DNG 选项

[dnglodorations](https://apireference.aspose.com/net/imaging/aspose.imaging.imageloadoptions/dngloadoptions)增加了控制预去马赛克去噪的选项，作为 [Fbdd](https://apireference.aspose.com/net/imaging/aspose.imaging.imageloadoptions/dngloadoptions/properties/fbdd) 属性。要么不减，要么轻减，要么全减。

### SVG 选项

[SvgLoadOptions](https://apireference.aspose.com/net/imaging/aspose.imaging.imageloadoptions/svgloadoptions/) 添加了 [DefaultHeight](https://apireference.aspose.com/net/imaging/aspose.imaging.imageloadoptions/svgloadoptions/properties/defaultheight) 和 [DefaultWidth](https://apireference.aspose.com/net/imaging/aspose.imaging.imageloadoptions/svgloadoptions/properties/defaultwidth) 属性，这些属性设置或获取图像呈现的相应尺寸。仅在文件中未指定尺寸时使用。

### JPEG2000 选项

Jpeg2000 是一种相对复杂的算法，因此解码可能会很慢。因此 [MaximumDecodingTime](https://apireference.aspose.com/net/imaging/aspose.imaging.imageloadoptions/jpeg2000loadoptions/properties/maximumdecodingtime) 选项出现在 [Jpeg2000LoadOptions](https://apireference.aspose.com/net/imaging/aspose.imaging.imageloadoptions/jpeg2000loadoptions/) 中，以防止在较慢的机器上挂起非常大的超过 5500x6500 像素的图像。时间以秒为单位。

目前就这些，敬请关注！

如需更多示例，请访问[网站。成像 GitHub](https://github.com/aspose-imaging) 页面。Aspose.Imaging 上还有 [Twitter](https://twitter.com/Asposeimaging) 和[脸书](https://www.facebook.com/AsposeImaging)的新闻页面