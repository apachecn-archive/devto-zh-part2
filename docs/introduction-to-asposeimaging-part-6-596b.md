# Aspose 简介。成像，第 6 部分

> 原文：<https://dev.to/nnevod/introduction-to-asposeimaging-part-6-596b>

[第一部分](https://dev.to/nnevod/introduction-to-asposeimaging-8jd)

[第二部分](https://dev.to/nnevod/introduction-to-asposeimaging-part-2-40p)

[第三部分](https://dev.to/nnevod/introduction-to-asposeimaging-part-3-3ah3)

[第四部分](https://dev.to/nnevod/introduction-to-asposeimaging-part-4-2o1j)

[第五部分](https://dev.to/nnevod/introduction-to-asposeimaging-part-5-1349)

在这一部分中，我将告诉你一种更简单的旋转图像的方法，如何创建多页 TIFFs 以及如何从动画 GIF 文件中提取单独的帧。

## 简单旋转。

除了基于变换矩阵的旋转之外，还有一个助手方法，可以在 [Aspose 中轻松旋转。成像](https://products.aspose.com/imaging/)。该方法对[光栅图像](https://apireference.aspose.com/net/imaging/aspose.imaging/rasterimage)及其后代有效。

```
// Load an image to be rotated in an instance of RasterImage
using (RasterImage image = (RasterImage)Image.Load("aspose-logo.jpg"))
{
    // Before rotation, the image should be cached for better performance
    if (!image.IsCached)
    {
        image.CacheData();
    }
    // Perform the rotation on 20 degree while keeping the image size proportional with red background color and Save the result to a new file
    image.Rotate(20f, true, Color.Red);
    image.Save( "RotatingImageOnSpecificAngle_out.jpg");
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，您只需指定旋转角度(顺时针)，新图像是否应该调整大小以适应旋转的内容，以及用什么来填充“未覆盖”的区域。

## 构成多帧 TIFFs

这实际上也很简单，尽管例子会更长。TIFF 文件允许指定几个属性，[bits sample](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/bitspersample)属性-指定像素的每个通道的位数的数组， [Orientation](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/orientation) 属性-明显，[光度学](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/photometric)属性-指定定义 TIFF 响应曲线的颜色解释属性的值等。在这个例子中指定了，[压缩](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/compression)属性-明显-和[填充顺序](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/fillorder) -字符顺序-属性。

```
List<string> files = new List<string>(new[] {  "Frame1.tiff", "Frame2.tiff" });
TiffOptions createOptions = new TiffOptions(TiffExpectedFormat.Default);
createOptions.BitsPerSample = new ushort[] { 1 };
createOptions.Orientation = TiffOrientations.TopLeft;
createOptions.Photometric = TiffPhotometrics.MinIsBlack;
createOptions.Compression = TiffCompressions.CcittFax3;
createOptions.FillOrder = TiffFillOrders.Lsb2Msb;

TiffImage output = null;

List<TiffImage> images = new List<TiffImage>();
try
{
    foreach (var file in files)
    {
        // Create an instance of TiffImage and load the source image
        TiffImage input = (TiffImage)Image.Load(file);
        images.Add(input); // Do not dispose before data is fetched. Data is fetched on 'Save' later.
        foreach (var frame in input.Frames)
        {
            if (output == null)
            {
                // Create a new tiff image with first frame defined.
                output = new TiffImage(TiffFrame.CopyFrame(frame));
            }
            else
            {
                // Add copied frame to destination image
                output.AddFrame(TiffFrame.CopyFrame(frame));
            }
        }
    }

    if (output != null)
    {
        // Save the result
        output.Save("MultiFrameTiff.tif", createOptions);
    }
}
finally
{
    foreach (TiffImage image in images)
    {
        image.Dispose();
    }
    output.Dispose();
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，只有在调用 [Save](https://apireference.aspose.com/net/imaging/aspose.imaging/image/methods/save/index) 时，图像才真正被添加到 TIFF 图像中，所以您应该小心不要在之前处理它们。无论如何，我们在这里创建帧的副本。
要从 TIFF 图像中移除一个帧，调用 [RemoveFrame](https://apireference.aspose.com/net/imaging/aspose.imaging.fileformats.tiff.tiffimage/removeframe/methods/1) 并指定要移除的帧的索引。

## 从多帧 GIF 中保存一帧

这也很容易做到，但术语有点不同——这里，帧被放在块中，块被表示为实现 [IGifBlock](https://apireference.aspose.com/net/imaging/aspose.imaging.fileformats.gif/igifblock) 接口的对象。可以通过[添加模块](https://apireference.aspose.com/net/imaging/aspose.imaging.fileformats.gif/gifimage/methods/addblock)添加模块，通过[移除模块](https://apireference.aspose.com/net/imaging/aspose.imaging.fileformats.gif/gifimage/methods/removeblock)移除模块。要将图像添加为一个块，你只需创建一个新的 GifFrameBlock 并将图像传递给构造函数。还有其他种类的块。因此，以下是访问它们的方法:

```
Image objImage = Image.Load("sample.gif");
using (GifImage gif = (GifImage)objImage)
{
    // Iterate through arry of blocks in the GIF image
    for (int i = 0; i < gif.Blocks.Length; i++)
    {
        // Convert block to GifFrameBlock class instance
        GifFrameBlock gifBlock = gif.Blocks[i] as GifFrameBlock;

        // Check if gif block is then ignore it
        if (gifBlock == null)
        {
            continue;
        }

        // Create an instance of TIFF Option class and Save the GIFF block as TIFF image
        TiffOptions objTiff = new TiffOptions(TiffExpectedFormat.Default);
        gifBlock.Save("sample_frame_"  + i + "_out.tif", objTiff);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

目前就这些，敬请关注！

如需更多示例，请访问[网站。成像 GitHub](https://github.com/aspose-imaging) 页面。Aspose.Imaging 上还有 [Twitter](https://twitter.com/Asposeimaging) 和[脸书](https://www.facebook.com/AsposeImaging)的新闻页面