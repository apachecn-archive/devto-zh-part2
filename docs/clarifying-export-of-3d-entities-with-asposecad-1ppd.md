# 用 Aspose 阐明 3D 实体的导出。计算机辅助设计

> 原文：<https://dev.to/nnevod/clarifying-export-of-3d-entities-with-asposecad-1ppd>

[Aspose。CAD](https://products.aspose.com/cad) 支持从 AutoCAD 和 STL 文件中导出 3D 实体。对于其他格式，目前(从 18.8 开始)不支持 3D 实体。

用户可以使用 [CadRasterizationOptions](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/cadrasterizationoptions) 的 [TypeOfEntities](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/cadrasterizationoptions/properties/typeofentities) 属性指定导出哪些实体——它是 [TypeOfEntities](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/typeofentities/) 枚举类型的
，接受 Entities3D 或 Entities2D(缺省)值。请看例子:

```
using (Aspose.CAD.Image cadImage = Aspose.CAD.Image.Load("image.dwg"))
{
    Aspose.CAD.ImageOptions.CadRasterizationOptions rasterizationOptions = new
    Aspose.CAD.ImageOptions.CadRasterizationOptions();
    rasterizationOptions.PageWidth = 500;
    rasterizationOptions.PageHeight = 500;
    rasterizationOptions.TypeOfEntities = TypeOfEntities.Entities3D;

    PdfOptions pdfOptions = new PdfOptions();
    pdfOptions.VectorRasterizationOptions = rasterizationOptions;

    cadImage.Save("Export3DImagestoPDF_out.pdf", pdfOptions);
} 
```

Enter fullscreen mode Exit fullscreen mode

对于 AutoCAD 文件，大多数实体类型都将在这两种情况下导出，尽管由于投影的原因会有一些差异。但是，有些实体只有在设置了相应的实体类型时才会被导出。
仅在 2D 模式下导出的实体包括:属性和属性定义实体、参考底图、光栅图像、表格、文本和多行文字实体。仅在 3D 模式下导出的图元包括:3D 面和实体图元、网格图元、平面、扫掠、拉伸和旋转曲面。
如果选择 3D 实体进行导出，输出图像将自动以它们为中心——与将 CadRasterizationOptions 的 CenterDrawing 属性设置为 **true** 时的方式相同——图像中所有 3D 实体的几何中心被确定并固定在输出图像的中心。

对于一个 STL 文件，你可以不指定 type of entities——它会自动设置为 Entities3D，因为 STL 只有 3D 实体。居中以同样的方式发生。

对于其他文件格式，指定 3D 实体导出只会导致居中-这是所有格式的默认行为。

目前就这些，敬请关注！

如需更多示例，请访问[网站。CAD GitHub](https://github.com/aspose-cad) 页面。Aspose.CAD 上还有 [Twitter](https://twitter.com/Asposecad) 和[脸书](https://www.facebook.com/AsposeCAD)的新闻页面