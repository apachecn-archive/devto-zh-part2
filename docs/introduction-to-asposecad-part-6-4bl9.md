# Aspose 简介。计算机辅助设计，第 6 部分

> 原文：<https://dev.to/nnevod/introduction-to-asposecad-part-6-4bl9>

[第一部分](https://dev.to/nnevod/introduction-to-asposecad-library-361h)

[第二部分](https://dev.to/nnevod/introduction-to-asposecad-part-2-2kgf)

[第三部分](https://dev.to/nnevod/introduction-to-asposecad-part-3-1mhf)

[第四部分](https://dev.to/nnevod/introduction-to-asposecad-part-4-ma8)

[第五部分](https://dev.to/nnevod/introduction-to-asposecad-part-5-4ff6)

 允许用户使用文件中未指定的字体将 DWG 文件渲染为光栅图像。下面是如何做到这一点:

## 字体替换

DWG 文件中文本的字体是在样式表对象中指定的，用 Aspose 表示。CAD 通过一个 [CadStyleTableObject](https://apireference.aspose.com/net/cad/aspose.cad.fileformats.cad.cadtables/cadstyletableobject/) 类。这些对象的集合存在于 [CadImage](https://apireference.aspose.com/net/cad/aspose.cad.fileformats.cad/cadimage) 实例的[样式](https://apireference.aspose.com/net/cad/aspose.cad.fileformats.cad/cadimage/properties/styles)属性中。风格有名字，可以用来区分。有一个例子是关于如何改变一个有特殊名字的类所使用的字体:

```
using (Aspose.CAD.FileFormats.Cad.CadImage cadImage = (Aspose.CAD.FileFormats.Cad.CadImage)Aspose.CAD.Image.Load(sourceFilePath))
{
    // Iterate over the items of CadStyleDictionary
    foreach (CadStyleTableObject style in cadImage.Styles)
    {
        if (style.StyleName == "Roman")
        {
            // Specify the font for one particular style
            style.PrimaryFontName = "Arial";
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

目前就这些，敬请关注！

如需更多示例，请访问[网站。CAD GitHub](https://github.com/aspose-cad) 页面。Aspose.CAD 上还有 [Twitter](https://twitter.com/Asposecad) 和[脸书](https://www.facebook.com/AsposeCAD)的新闻页面