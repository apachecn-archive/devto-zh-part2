# Aspose 的更多新特性。CAD 18.3

> 原文：<https://dev.to/nnevod/more-new-features-of-asposecad-183--5dfm>

除了我已经在文章中介绍过的两个新特性，比如:[设置默认线帽的新选项](https://dev.to/nnevod/new-option-to-set-default-line-caps-for-asposecad-for-net--4a0l)和[从 CAD 文件中导出特定实体](https://dev.to/nnevod/exporting-particular-entities-from-cad-files-using-asposecad-for-net--13bn)。CAD 18.3 还具有以下新功能:

支持保存到 DXF 文件。
支持在导出 DWG 图像时添加光栅图像
支持在导出 DWG 图像时添加文本。

## 支持保存成 DXF 文件:

这很简单，只需加载 DXF 文件，修改加载的 [CadImage](https://apireference.aspose.com/net/cad/aspose.cad.fileformats.cad/cadimage) 对象，并在其上调用 [Save](https://apireference.aspose.com/net/cad/aspose.cad.datastreamsupporter/save/methods/2) ，输出扩展名为 DXF 的文件名。(文件格式是根据加载和保存的扩展名自动选择的)。

```
using System;
using System.Collections.Generic;
using Aspose.CAD;
using Aspose.CAD.FileFormats.Cad;
using Aspose.CAD.FileFormats.Cad.CadConsts;
using Aspose.CAD.FileFormats.Cad.CadObjects;
using Aspose.CAD.ImageOptions;

namespace DxfDemo
{
    class Program
    {
        static void Main()
        {
            string sourceFilePath =  "example.dxf";
            using (CadImage cadImage = (CadImage)Image.Load(sourceFilePath))
            {
                // do any updates to cadImage there

                //Save to resulting DXF
                cadImage.Save("modified.dxf");
            }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

就是这样。

## 支持导出 DWG 图像时添加光栅图像:

您可以将光栅图像添加到已经加载的 DWG/DXF 图像(即 [CadImage](https://apireference.aspose.com/net/cad/aspose.cad.fileformats.cad/cadimage) 对象)中，然后将整个图像导出为光栅或 PDF:

```
using System;
using System.Collections.Generic;
using Aspose.CAD;
using Aspose.CAD.FileFormats.Cad;
using Aspose.CAD.FileFormats.Cad.CadConsts;
using Aspose.CAD.FileFormats.Cad.CadObjects;
using Aspose.CAD.ImageOptions;

namespace DxfDemo
{
    class Program
    {
        static void Main()
        {
            string dwgPathToFile =  "Drawing11.dwg";
            CadImage cadImage1 = (CadImage)Image.Load(dwgPathToFile);
            // using (Image image = ImageLoader.Load(dwgPathToFile))
            {
                //Setup image data
                CadRasterImageDef cadRasterImageDef = new CadRasterImageDef();
                cadRasterImageDef.ObjectHandle = "A3B4";
                //Raster image file has to be in the same folder as DWG
                cadRasterImageDef.FileName = "road-sign-custom.png";

                //Setup location of image being added within the CAD image 
                CadRasterImage cadRasterImage = new CadRasterImage();
                cadRasterImage.ImageDefReference = "A3B4";
                cadRasterImage.InsertionPoint.X = 26.77;
                cadRasterImage.InsertionPoint.Y = 22.35;
                cadRasterImage.DisplayFlags = 7;
                cadRasterImage.ImageSizeU = 640;
                cadRasterImage.ImageSizeV = 562;
                cadRasterImage.UVector.X = 0.0061565450840500831;
                cadRasterImage.UVector.Y = 0;
                cadRasterImage.VVector.X = 0;
                cadRasterImage.VVector.Y = 0.0061565450840500822;
                cadRasterImage.ClippingState = 0;
                cadRasterImage.ClipBoundaryVertexList.Add(new Cad2DPoint(-0.5, 0.5));
                cadRasterImage.ClipBoundaryVertexList.Add(new Cad2DPoint(639.5, 561.5));

                //Add image description entity to CAD image
                CadImage cadImage = (CadImage)cadImage1;
                cadImage.BlockEntities["*Model_Space"].AddEntity(cadRasterImage);

                //Add image data (raster itself) to CAD image
                List<CadBaseObject> list = new List<CadBaseObject>(cadImage.Objects);
                list.Add(cadRasterImageDef);
                cadImage.Objects = list.ToArray();

                //Export the modified image
                PdfOptions pdfOptions = new PdfOptions();
                CadRasterizationOptions cadRasterizationOptions = new CadRasterizationOptions();
                pdfOptions.VectorRasterizationOptions = cadRasterizationOptions;
                cadRasterizationOptions.DrawType = CadDrawTypeMode.UseObjectColor;
                cadRasterizationOptions.CenterDrawing = true;
                cadRasterizationOptions.PageHeight = 1600;
                cadRasterizationOptions.PageWidth = 1600;
                cadRasterizationOptions.Layouts = new string[] { "Model" };
                cadImage1.Save("export2.pdf", pdfOptions);
            }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 支持在导出 DWG 图像时添加文本:

同样，您可以将文本添加到加载的图像中，使其出现在导出的光栅或 PDF 图像中。这样做有点简单，因为您不必分别提供数据和元数据:

```
using System;
using System.Collections.Generic;
using Aspose.CAD;
using Aspose.CAD.FileFormats.Cad;
using Aspose.CAD.FileFormats.Cad.CadConsts;
using Aspose.CAD.FileFormats.Cad.CadObjects;
using Aspose.CAD.ImageOptions;

namespace DxfDemo
{
    class Program
    {
        static void Main()
        {
            string dwgPathToFile =  "Drawing11.dwg";
            CadImage cadImage1 = (CadImage)Image.Load(dwgPathToFile);
            // using (Image image = ImageLoader.Load(dwgPathToFile))
            {
                //Only have to setup the text entity
                CadText cadText = new CadText();
                cadText.StyleType = "Standard";
                cadText.DefaultValue = "Some custom text";
                cadText.ColorId = 256;
                cadText.LayerName = "0";
                cadText.FirstAlignment.X = 47.90;
                cadText.FirstAlignment.Y = 5.56;
                cadText.TextHeight = 0.8;
                cadText.ScaleX = 0.0;

                //Add text to model space block
                CadImage cadImage = (CadImage)cadImage1;
                cadImage.BlockEntities["*Model_Space"].AddEntity(cadText);

                //Export modified image
                PdfOptions pdfOptions = new PdfOptions();
                CadRasterizationOptions cadRasterizationOptions = new CadRasterizationOptions();
                pdfOptions.VectorRasterizationOptions = cadRasterizationOptions;
                cadRasterizationOptions.DrawType = CadDrawTypeMode.UseObjectColor;
                cadRasterizationOptions.CenterDrawing = true;
                cadRasterizationOptions.PageHeight = 1600;
                cadRasterizationOptions.PageWidth = 1600;
                cadRasterizationOptions.Layouts = new string[] { "Model" };
                cadImage1.Save("Drawing11_with_text.pdf", pdfOptions);
            }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！

如需更多示例，请访问[网站。CAD GitHub](https://github.com/aspose-cad) 页面。Aspose.CAD 上还有 [Twitter](https://twitter.com/AsposeCAD) 和[脸书](https://www.facebook.com/AsposeCAD)的新闻页面

这是主要的目的。CAD 产品页面。