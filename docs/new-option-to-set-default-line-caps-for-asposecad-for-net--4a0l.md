# 为 Aspose 设置默认线帽的新选项。CAD for。网

> 原文：<https://dev.to/nnevod/new-option-to-set-default-line-caps-for-asposecad-for-net--4a0l>

阿斯波斯。CAD for。NET 是一个允许将 CAD 文件(例如，AutoCAD DWG 和 DXF 文件)转换为 PDF 和光栅图像的库，而无需使用 AutoCAD 或任何其他软件。

Aspose 的 18.2 版本。CAD for。NET 将于 2018 年 2 月 27 日发布，允许为未定义线帽样式的线条设置默认线帽样式，这在某些情况下会使导出的图像看起来更好。

以下是加载 DWG 文件并保存为 PDF 格式的示例代码，默认线帽样式设置为扁平。

```
using System;
using System.Drawing.Drawing2D;
using Aspose.CAD;
using Aspose.CAD.FileFormats.Cad;
using Aspose.CAD.ImageOptions;

namespace LineCapDemo
{
    class Program
    {
        static void Main()
        {
            string fileName = "TestFile.dwg"; 
            try 
            { 
                CadImage cadImage = (CadImage)Image.Load(fileName); 
                CadRasterizationOptions rasterizationOptions = new CadRasterizationOptions(); 
                rasterizationOptions.PageWidth = cadImage.Width * 100; 
                rasterizationOptions.PageHeight = cadImage.Height * 100; 

                PdfOptions pdfOptions = new PdfOptions(); 

                //This is the new option for setting default line 
                //cap style added in Aspose.CAD for .NET 18.2 
                rasterizationOptions.PenOptions = new PenOptions 
                { 
                    StartCap = LineCap.Flat, 
                    EndCap = LineCap.Flat 
                }; 

                pdfOptions.VectorRasterizationOptions = rasterizationOptions; 
                string outPath1 = fileName + "_flat" + ".pdf"; 
                Console.WriteLine("output path: " + outPath1); 
                cadImage.Save(outPath1, pdfOptions); 
            } 
            catch (Exception ex) 
            { 
                Console.WriteLine(ex.Message); 
            } 
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

开始盖和结束盖是系统的一部分。Drawing.Drawing2D.LineCap 枚举类型。

所有输出文件格式都支持默认线帽样式，即 PDF、PNG、BMP、GIF、JPEG2000、JPEG、PSD、TIFF 和 WMF。如果不指定缺省线帽样式，系统将使用自己的缺省线帽样式，该样式因情况而异。

完整的 Aspose。CAD for。NET API 引用位于[这里](https://apireference.aspose.com/net/cad/)。