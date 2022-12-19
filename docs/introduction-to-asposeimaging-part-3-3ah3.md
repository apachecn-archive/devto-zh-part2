# Aspose 简介。成像，第 3 部分

> 原文：<https://dev.to/nnevod/introduction-to-asposeimaging-part-3-3ah3>

这里我就说说怎么用 [Aspose。成像](https://products.aspose.com/imaging/)修改光栅图像最常见的方式:亮度、对比度和伽玛的调整，被认为是光栅图像的“常见”调整，裁剪和调整图像大小，抖动和二值化通常在准备打印图像时进行。

## 亮度、对比度和伽玛。

这些都是通过一行命令执行的，本质上非常相似，所以我将提供一个实现所有这些功能的示例:

```
 using (Image img = Image.Load("sample.png"))
            {
                // Cast object of Image to RasterImage
                RasterImage rasterImage = (RasterImage)img;

                // Check if RasterImage is cached and Cache RasterImage for better performance
                if (!rasterImage.IsCached)
                {
                    rasterImage.CacheData();
                }

                // Adjust the brightness
                rasterImage.AdjustBrightness(100);

                //Adjust the contrast
                rasterImage.AdjustContrast(50);

                //Adjust gamma
                rasterImage.AdjustGamma(2.2f, 2.2f, 2.2f);

                // Create an instance of TiffOptions for the resultant image, Set various properties for the object of TiffOptions and Save the resultant image
                PngOptions options = new PngOptions();
                rasterImage.Save("sample_out.png", options);
            } 
```

Enter fullscreen mode Exit fullscreen mode

因此，当这些方法修改它们被调用的实例时，rasterImage 将立刻拥有所有的改变:亮度、对比度和 gamma。现在，这些方法到底是如何工作的？
[调整亮度](https://apireference.aspose.com/net/imaging/aspose.imaging/rasterimage/methods/adjustbrightness)本质上是将图像所有像素的亮度移动规定的量，就在数值代码中。由于我们的图像通常以每通道 8 位的格式编码，这意味着调用 AdjustBrightness(-256)将使图像变黑，而 AdjustBrightness(256)将使图像变白，不管图像中有什么——因为每个代码都将被减少/增加到最小/最大可能值。
[调整对比度](https://apireference.aspose.com/net/imaging/aspose.imaging/rasterimage/methods/adjustcontrast)也调整相对于当前状态的对比度。调节范围从-100 到 100，绝对值基本上是百分之几。尽管可以向该方法传递更大的绝对值，但它们将分别被舍入到-100 或 100。-100 产生均匀颜色的图像，+100 增加当前图像对比度两倍。
[AdjustGamma](https://apireference.aspose.com/net/imaging/aspose.imaging/rasterimage/methods/adjustgamma/index) 相对于当前状态再次工作。伽马系数按原样应用，即输入图像被解释为在线性空间中。因此，将系数指定为 1.0 不会导致图像发生变化。有两种重载，一种显示为每个 R，G 和 B 通道设置单独伽马值，另一种采用一个值并将其应用于所有通道。

## 裁剪一幅图像

对于裁剪，Aspose.Imaging 中有两种基本方法。首先是指定一个将用作裁剪边界的[矩形](https://apireference.aspose.com/net/imaging/aspose.imaging/rectangle)，另一个是指定将从图像中移除的边界——上、下、左、右。

```
 using (RasterImage rasterImage = (RasterImage)Image.Load("sample.png"))
            {
                // Create an instance of Rectangle class with desired size,
                // Perform the crop operation on object of 
                // Rectangle class and Save the results to disk
                Rectangle rectangle = new Rectangle(20, 20, 20, 20);
                rasterImage.Crop(rectangle);
                rasterImage.Save(dataDir + "Sample_crop.jpg");
            }

            using (RasterImage rasterImage = (RasterImage)Image.Load("sample.png"))
            {
                // Create an instance of Rectangle class with desired size, Perform the crop operation on object of Rectangle class and Save the results to disk

                // Crop off 20-pixel-wide borders from each side
                rasterImage.Crop(20,20,20,20);
                rasterImage.Save(dataDir + "Sample_crop1.jpg");
            } 
```

Enter fullscreen mode Exit fullscreen mode

矩形的 Y 坐标从图像的上边界开始测量，这在光栅图形中很典型。

## 调整图像大小

阿斯波斯。Imaging 允许分别为宽度和高度调整图像的大小，也可以同时为两者调整大小，并且允许指定调整大小的方法。

```
 using (Image image = Image.Load("sample.png"))
            {
                if (!image.IsCached)
                {
                    image.CacheData();
                }

                // Specifying only height, width and ResizeType
                int newWidth = image.Width / 2;
                image.ResizeWidthProportionally(newWidth, ResizeType.LanczosResample);
                int newHeight = image.Height / 2;
                image.ResizeHeightProportionally(newHeight, ResizeType.NearestNeighbourResample);

                image.Resize(newWidth * 3, newHeight * 2, ResizeType.HighQualityResample);

                image.Save("samlpe_out.png");
            } 
```

Enter fullscreen mode Exit fullscreen mode

[resize height proportional](https://apireference.aspose.com/net/imaging/aspose.imaging/image/methods/resizeheightproportionally/index)和[resize width proportional](https://apireference.aspose.com/net/imaging/aspose.imaging/image/methods/resizewidthproportionally/index)分别采用新的高度和宽度，在不改变比例的情况下调整图像大小。[调整大小](https://apireference.aspose.com/net/imaging/aspose.imaging/image/methods/resize/index)不保留比例，因为同时提供了高度和宽度。 [ResizeType](https://apireference.aspose.com/net/imaging/aspose.imaging/resizetype) 列举了各种可能的重新缩放算法。

## 抖动和二值化。

抖动与大多数其他变换一样容易执行，只需指定所需的位深度和抖动方法:

```
 using (var image = (PngImage)Image.Load("sample.png"))
            {
                // Peform dithering on the current image and Save the resultant image
                image.Dither(DitheringMethod.ThresholdDithering, 4);
                image.Save("sample_out.png");
            } 
```

Enter fullscreen mode Exit fullscreen mode

[抖动方法](https://apireference.aspose.com/net/imaging/aspose.imaging/ditheringmethod)提供阈值抖动和 Floyd-Steinberg 抖动选项。还有一个重载允许使用自定义的[调色板](https://apireference.aspose.com/net/imaging/aspose.imaging/icolorpalette)进行抖动。

二值化，即将图像转换为黑白图像，无需抖动，可以使用自定义阈值，使用 [Otsu 方法](https://en.wikipedia.org/wiki/Otsu's_method)和 [Bradley 方法](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.420.7883&rep=rep1&type=pdf)完成。所有方法都同样简单易用:

```
 using (Image image = Image.Load("sample.png"))
            {
                // Cast the image to RasterCachedImage and Check if image is cached 
                RasterCachedImage rasterCachedImage = (RasterCachedImage)image;
                if (!rasterCachedImage.IsCached)
                {
                    // Cache image if not already cached
                    rasterCachedImage.CacheData();
                }

                // Binarize image with predefined fixed threshold 
                rasterCachedImage.BinarizeFixed(250);

                // Binarize image with Otsu Thresholding and Save the resultant image 
                rasterCachedImage.BinarizeOtsu();

                // Define threshold value for Bradley method, Call BinarizeBradley method and pass the threshold value as paramete
                double threshold = 0.15;
                objimage.BinarizeBradley(threshold);

                rasterCachedImage.Save("sample.png");
            } 
```

Enter fullscreen mode Exit fullscreen mode

为了简洁起见，这个例子只是把所有的方法放在一个代码片段中，不要试图用那种方式实现二进制化——结果将毫无意义。
[二进制固定](https://apireference.aspose.com/net/imaging/aspose.imaging/rasterimage/methods/binarizefixed)会将大于阈值的像素设置为白色，其他设置为黑色。[二值化 Otsu 使用](https://apireference.aspose.com/net/imaging/aspose.imaging/rasterimage/methods/binarizeotsu) [Otsu 的](https://en.wikipedia.org/wiki/Otsu's_method)方法将从图像内容中确定阈值，试图通过算法找到最佳阈值。[二进制布拉德利](https://apireference.aspose.com/net/imaging/aspose.imaging/rasterimage/methods/binarizebradley)使用[布拉德利的](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.420.7883&rep=rep1&type=pdf)方法在本地工作，如果一个像素的亮度低于相邻像素的平均亮度乘以阈值，它会将该像素变为黑色。

目前就这些，敬请关注！

如需更多示例，请访问[网站。成像 GitHub](https://github.com/aspose-imaging) 页面。Aspose.Imaging 上还有 [Twitter](https://twitter.com/Asposeimaging) 和[脸书](https://www.facebook.com/AsposeImaging)的新闻页面