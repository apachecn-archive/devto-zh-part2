# Aspose 简介。成像，第 2 部分

> 原文：<https://dev.to/nnevod/introduction-to-asposeimaging-part-2-40p>

[第一部分](https://dev.to/nnevod/introduction-to-asposeimaging-8jd)

在这一部分中，我将继续写一些关于 Aspose 的更一般的特性。成像

## 将一幅图像绘制到另一幅图像上

前面提到的 [Graphics](https://apireference.aspose.com/net/imaging/aspose.imaging/graphics) 类也可以用来将一个[图像](https://apireference.aspose.com/net/imaging/aspose.imaging/image)插入到另一个图像中。它可以用来在图像中插入签名或水印。这个例子显示了这样做是多么简单。

```
 // Create an instance of image options and set its various properties
            JpegOptions imageOptions = new JpegOptions();

            // Create an instance of FileCreateSource and assign it to Source property
            imageOptions.Source = new FileCreateSource("Two_images_result_out.bmp", false);

            // Create an instance of Image and define canvas size
            using (var image = Image.Create(imageOptions, 600, 600))
            {
                // Create and initialize an instance of Graphics, Clear the image surface with white color 
                var graphics = new Graphics(image);
                graphics.Clear(Color.White);
                // And draw another images onto it
                graphics.DrawImage(Image.Load(dataDir + "sample_1.bmp"), 0, 0, 600, 300);
                graphics.DrawImage(Image.Load(dataDir + "File1.bmp"), 0, 300, 600, 300);
                image.Save();
            } 
```

Enter fullscreen mode Exit fullscreen mode

## 旋转绘制的对象

你在图像上画的任何东西都可以被转置、缩放和旋转，就像在 GDI+中一样。这可以用于任意绘图、水印插入等。
举个例子，我们试着从上一个例子对角画出 sample_1.bmp。将插入的行替换为以下行:

```
 // Get size of image we're inserting into
                SizeF sz = graphics.Image.Size;
                // Create an object of Matrix class for transformation
                Matrix matrix = new Matrix();

                // First a translation to center of image we're inserting into 
                matrix.Translate(sz.Width / 2, sz.Height / 2);
                // Then a rotation 
                matrix.Rotate(-45.0f);

                // Set the Transformation through Matrix
                graphics.Transform = matrix;

                //Insert the image to be rotated
                graphics.DrawImage(Image.Load(dataDir + "sample_1.bmp"), 0, 0, 600, 300);

                // Drop the transform so next inserted image will be inserted as is.
                graphics.Transform = null; 
```

Enter fullscreen mode Exit fullscreen mode

矩阵类再次取代了 T2 系统。绘图.矩阵类。

## 多页导出单幅图像和多幅图像

对于使用多页格式的图像(如 DjVu)作为源的情况，有一个选项可以指定必须导出哪些页面。如果输出格式也支持一个文件中的多个图像，如 TIFF，可以一次指定几个页面。看一下例子:

```
 using (DjvuImage image = (DjvuImage)Image.Load( "Sample.djvu"))
            {
                // Create an instance of TiffOptions with preset options and IntRange and initialize it with range of pages to be exported
                TiffOptions exportOptions = new TiffOptions(TiffExpectedFormat.TiffDeflateBw);
                //Specify the pages to export
                IntRange range = new IntRange(0, 2);

                // Initialize an instance of DjvuMultiPageOptions while passing instance of IntRange and  Call Save method while passing instance of TiffOptions
                exportOptions.MultiPageOptions = new DjvuMultiPageOptions(range);
                image.Save(dataDir + "ConvertRangeOfDjVuPages_out.tif", exportOptions);
            } 
```

Enter fullscreen mode Exit fullscreen mode

这里最重要的一点是[tiffo options](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions)([image options base](https://apireference.aspose.com/net/imaging/aspose.imaging/imageoptionsbase)实际上对所有格式都有效)的[multipeageoptions](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/multipageoptions)属性，用于设置该属性的[djvumultipeageoptions](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/djvumultipageoptions)类，以及 [IntRange](https://apireference.aspose.com/net/imaging/aspose.imaging/intrange) 类，它本质上是一个整数列表，用作那里的页码列表。
对于输出格式不支持多页的情况，必须使用一个只指定一页的 IntRange 实例，分别用于每一页。

```
 using (DjvuImage image = (DjvuImage)Image.Load(dataDir + "Sample.djvu"))
            {
                // Create an instance of BmpOptions and Set BitsPerPixel for resultant images
                BmpOptions exportOptions = new BmpOptions();
                exportOptions.BitsPerPixel = 32;

                // Create an instance of IntRange and initialize it with range of pages to be exported
                IntRange range = new IntRange(0, 2);
                int counter = 0;

                // Save each page in separate file, as BMP do not support layering
                foreach (var i in range.Range)
                {
                    exportOptions.MultiPageOptions = new DjvuMultiPageOptions(range.GetArrayOneItemFromIndex(counter));
                    image.Save(dataDir + string.Format("{0}_out.bmp", counter++), exportOptions);
                }
            } 
```

Enter fullscreen mode Exit fullscreen mode

这里，IntRange 的 [GetArrayOneItemFromIndex](https://apireference.aspose.com/net/imaging/aspose.imaging/intrange/methods/getarrayoneitemfromindex) 方法从指定索引处的元素返回值，将其包装到一个数组中。这样做是为了举例，因为 DjvuMultiPageOptions 也有一个构造函数，它接受单个 Int32 值作为单个页码。

目前就这些，敬请关注！

如需更多示例，请访问[网站。成像 GitHub](https://github.com/aspose-imaging) 页面。Aspose.Imaging 上还有 [Twitter](https://twitter.com/Asposeimaging) 和[脸书](https://www.facebook.com/AsposeImaging)的新闻页面