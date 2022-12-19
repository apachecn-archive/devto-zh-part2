# Aspose 简介。成像，第 4 部分。

> 原文：<https://dev.to/nnevod/introduction-to-asposeimaging-part-4-2o1j>

[第一部分](https://dev.to/nnevod/introduction-to-asposeimaging-8jd)

[第二部分](https://dev.to/nnevod/introduction-to-asposeimaging-part-2-40p)

[第三部分](https://dev.to/nnevod/introduction-to-asposeimaging-part-3-3ah3)

在这篇文章中，我将描述 Aspose 最重要的特性之一。成像，过滤图像，以及可用的过滤器。

## 过滤图像

对图像应用滤镜非常简单，只需创建一个 [FilterOptionsBase](https://apireference.aspose.com/net/imaging/aspose.imaging.imagefilters.filteroptions/filteroptionsbase) 的后代，并将其传递给 [RasterImage](https://apireference.aspose.com/net/imaging/aspose.imaging/rasterimage) 的[滤镜](https://apireference.aspose.com/net/imaging/aspose.imaging/rasterimage/methods/filter)方法:

```
// Load the image
using (Image image = Image.Load(dataDir + "asposelogo.gif"))
{
    // Convert the image into RasterImage, Pass Bounds[rectangle] 
    // of image and GaussianBlurFilterOptions instance to Filter method and Save the results
    RasterImage rasterImage = (RasterImage)image;
    rasterImage.Filter(rasterImage.Bounds, new GaussianBlurFilterOptions(5, 5));
    rasterImage.Save(dataDir + "BlurAnImage_out.gif");
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，如果需要的话，你可以只对图像的一部分进行过滤。

## 过滤类型

有几种受支持的过滤器。首先，有指定中值滤波器的 [MedianFilterOptions](https://apireference.aspose.com/net/imaging/aspose.imaging.imagefilters.filteroptions/medianfilteroptions) 、[big rectangularfilteroptions](https://apireference.aspose.com/net/imaging/aspose.imaging.imagefilters.filteroptions/bigrectangularfilteroptions)和[small rectangularfilteroptions](https://apireference.aspose.com/net/imaging/aspose.imaging.imagefilters.filteroptions/smallrectangularfilteroptions)-不同种类的箱式滤波器、[BilateralSmoothingFilterOptions](https://apireference.aspose.com/net/imaging/aspose.imaging.imagefilters.filteroptions/bilateralsmoothingfilteroptions)，即进行双边平滑，两种卷积滤波器-[GaussianBlurFilterOptions](https://apireference.aspose.com/net/imaging/aspose.imaging.imagefilters.filteroptions/gaussianblurfilteroptions)和 [SharpenFilterOptions](https://apireference.aspose.com/net/imaging/aspose.imaging.imagefilters.filteroptions/sharpenfilteroptions) ，同样具有描述性名称，最有趣的是执行以下操作的去卷积滤波器
bigtrectangularfilteroptions 和 SmallRectangularFilterOptions 是无参数的。
MedianFilterOptions 需要过滤器框大小来创建。
创建 BilateralSmoothingFilterOptions 时可以不使用参数，也可以使用内核大小作为参数。
gaussianbulfilteroptions 可以在没有参数的情况下用默认值创建，也可以提供过滤器半径和 sigma。这同样适用于 SharpenFilterOptions。
GaussWienerFilterOptions 也可以默认创建，或者使用滤镜半径和 sigma -如果你知道应用模糊的参数。
MotionWienerFilterOptions 是用长度、角度创建的，它们定义了模糊向量和模糊的平滑因子，因此您可以调整最佳参数来消除图像中的运动模糊。

目前就这些，敬请关注！

如需更多示例，请访问[网站。成像 GitHub](https://github.com/aspose-imaging) 页面。Aspose.Imaging 上还有 [Twitter](https://twitter.com/Asposeimaging) 和[脸书](https://www.facebook.com/AsposeImaging)的新闻页面