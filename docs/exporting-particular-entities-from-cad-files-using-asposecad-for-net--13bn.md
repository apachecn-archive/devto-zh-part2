# 使用 Aspose 从 CAD 文件中导出特定实体。CAD for。网

> 原文：<https://dev.to/nnevod/exporting-particular-entities-from-cad-files-using-asposecad-for-net--13bn>

有时需要从 CAD 文件中仅导出一些特定的实体。如何做到这一点可能并不明显。然而，实际上这是一件非常简单的事情。本文描述的方法适用于 DWG、DXF 和 STL 文件。

对于这些文件，加载的文件[图像](https://apireference.aspose.com/net/cad/aspose.cad/image)实际上是  类的后代或实例，该类具有[实体](https://apireference.aspose.com/net/cad/aspose.cad.fileformats.cad/cadimage/properties/entities)属性。该属性是一个数组，包含来自一个文件的所有实体，其形式为 [CadBaseEntity](https://apireference.aspose.com/net/cad/aspose.cad.fileformats.cad.cadobjects/cadbaseentity) 类实例。导出特定的实体就像检索这个数组一样简单，挑选你需要的任何实体，把它们放入另一个数组，并用这个数组设置回[实体](https://apireference.aspose.com/net/cad/aspose.cad.fileformats.cad/cadimage/properties/entities)属性。

下面是一个只留下文本实体显示的例子:

```
using System;
using System.Collections.Generic;
using Aspose.CAD;
using Aspose.CAD.FileFormats.Cad;
using Aspose.CAD.FileFormats.Cad.CadConsts;
using Aspose.CAD.FileFormats.Cad.CadObjects;
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
                //Casting loaded Image to CadImage
                //as plain Image doesn't have Entities property
                CadImage cadImage = (CadImage)Image.Load(fileName); 

                CadRasterizationOptions rasterizationOptions = new CadRasterizationOptions(); 
                rasterizationOptions.PageWidth = cadImage.Width * 100; 
                rasterizationOptions.PageHeight = cadImage.Height * 100; 

                PdfOptions pdfOptions = new PdfOptions();

                //Retrieving all entities
                CadBaseEntity[] entities = cadImage.Entities;
                List<CadBaseEntity> filteredEntities = new List<CadBaseEntity>();

                foreach (CadBaseEntity entity in entities)
                {
                    //selecting only entities of TEXT type
                    if(entity.TypeName == CadEntityTypeName.TEXT)
                        filteredEntities.Add(entity);
                }

                //Making array of filtered entities
                entities = filteredEntities.ToArray();
                //Now only filtered entities are left in image
                cadImage.Entities = entities;

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

正如你所看到的，你可以以任何你想要的方式过滤实体，只要把你选择的任何东西赋回[实体](https://apireference.aspose.com/net/cad/aspose.cad.fileformats.cad/cadimage/properties/entities)属性，就完成了。