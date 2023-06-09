# 阐明 Aspose 中的图像创建。成像

> 原文：<https://dev.to/nnevod/clarifying-image-creation-in-asposeimaging-2dp6>

正如在以前的文章中所说的。成像不仅允许加载、操作和导出图像，还允许从头开始创建图像。然而，创建过程可能不是很明显，所以我将在这里用几句话来描述它。

在 Aspose 中创建新图像。成像，使用[图像](https://apireference.aspose.com/net/imaging/aspose.imaging/image)的[创建](https://apireference.aspose.com/net/imaging/aspose.imaging/image/methods/create)方法。它接受三个参数——一个 [ImageOptions](https://apireference.aspose.com/net/imaging/aspose.imaging/imageoptionsbase) 后代的实例(即 [PngOptions](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/pngoptions) 、[BM options](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/bmpoptions)等)。)，以及以像素为单位的宽度和高度。ImageOptions 的后代实例与通常用于导出图像的实例相同——因为 ImageOptions 指定了新图像所需的内容——像素格式等。在这种情况下，它只会产生干净的白色文件，而不是用另一个图像的内容填充它。这就是为什么宽度和高度是分开指定的——因为导出图像的尺寸是从其内容中推断出来的，而当创建新图像时，我们还没有内容。看看例子，不要跳过下面的段落！

```
using (FileStream stream = new FileStream("newImage.png", FileMode.Create))
{
    // So we want to create a PNG image
    PngOptions saveOptions = new PngOptions();
    // Here you can set whatever properties of saveOptions you need

    // Set the Source for PngOptions and create an instance of Image
    saveOptions.Source = new StreamSource(stream);               
    using (Image image = Image.Create(saveOptions, 100, 100))
    {
        // Create and initialize an instance of Graphics class and clear Graphics surface
        Graphics graphic = new Graphics(image);
        graphic.Clear(Color.Yellow);

        // Draw an arc shape by specifying the Pen object having red black color and coordinates, height, width,    start & end angles 
        int width = 100;
        int height = 200;
        int startAngle = 45;
        int sweepAngle = 270;

        // Draw arc to screen and save all changes.
        graphic.DrawArc(new Pen(Color.Black), 0, 0, width, height, startAngle, sweepAngle);
        // Note you can just call Save() without parameters, as source is bound to image
        image.Save();
    }
    // Now close the stream.
    stream.Close();
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，除了您在导出图像时指定的所有典型属性之外，这里我们还指定了 [Source](https://apireference.aspose.com/net/imaging/aspose.imaging/imageoptionsbase/properties/source) 属性。这就是我们如何指定新创建的图像应该存储在哪里，并且该属性必须用从有效的可写[流](http://msdn2.microsoft.com/en-us/library/8f86tw9e)创建的[源](https://apireference.aspose.com/net/imaging/aspose.imaging/source)来设置，以创建图像。

还有一个帮助器方法，允许你跳过自己创建流，这个方法就是 [FileCreateSource](https://apireference.aspose.com/net/imaging/aspose.imaging.sources/filecreatesource) 类中的 [FileCreateSource](https://apireference.aspose.com/net/imaging/aspose.imaging.sources/filecreatesource/constructors/1) 方法。看一看使用它:

```
ImageOptions.Source = new FileCreateSource("myNewImage.png", false); 
```

Enter fullscreen mode Exit fullscreen mode

注意第二个参数，它允许创建临时文件。如果设置为 true，文件将被创建为临时文件。尽管如此，您应该在使用完该流后对其进行处理。

还要注意，在第一个例子中，我们如何通过调用无参数的 [Save()](https://apireference.aspose.com/net/imaging/aspose.imaging/image/methods/save) 将更改应用到图像——这正是因为指定了 Source，并且图像被绑定到一个文件。这与加载现有图像不同，后者是虚拟复制的，必须保存到显式提供的文件中。

目前就这些，敬请关注！

如需更多示例，请访问[网站。成像 GitHub](https://github.com/aspose-imaging) 页面。Aspose.Imaging 上还有 [Twitter](https://twitter.com/Asposeimaging) 和[脸书](https://www.facebook.com/AsposeImaging-1702883649750052/)的新闻页面