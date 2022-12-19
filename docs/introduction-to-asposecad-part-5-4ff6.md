# Aspose 简介。计算机辅助设计，第 5 部分

> 原文：<https://dev.to/nnevod/introduction-to-asposecad-part-5-4ff6>

[第一部分](https://dev.to/nnevod/introduction-to-asposecad-library-361h)

[第二部分](https://dev.to/nnevod/introduction-to-asposecad-part-2-2kgf)

[第三部分](https://dev.to/nnevod/introduction-to-asposecad-part-3-1mhf)

[第四部分](https://dev.to/nnevod/introduction-to-asposecad-part-4-ma8)

在这一部分，我将再次继续把[描述为一只蜘蛛。CAD](https://products.aspose.com/cad/)DWG \ DXF 文件特有的功能，因为这些文件格式是 Aspose.CAD 最常用的格式

## 导出 3D 对象

要从 DWG\DXF 文件中导出 3D 对象，只需将 [CadRasterizationOptions](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/cadrasterizationoptions) 实例的 [TypeOfEntities](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/cadrasterizationoptions/properties/typeofentities) 属性设置为[type of entities](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/typeofentities). entities 3D，并执行导出:

```
using (Aspose.CAD.Image cadImage = Aspose.CAD.Image.Load("source.dxf"))
{
    Aspose.CAD.ImageOptions.CadRasterizationOptions rasterizationOptions 
        = new Aspose.CAD.ImageOptions.CadRasterizationOptions();
    rasterizationOptions.PageWidth = 500;
    rasterizationOptions.PageHeight = 500;
    rasterizationOptions.TypeOfEntities = TypeOfEntities.Entities3D;

    PdfOptions pdfOptions = new PdfOptions();
    pdfOptions.VectorRasterizationOptions = rasterizationOptions;

    cadImage.Save("export3d.pdf", pdfOptions);
} 
```

Enter fullscreen mode Exit fullscreen mode

## 在 CAD 文件中搜索文本

这是一个有点复杂的任务，因为有几种实体可能包含文本，包括嵌套的实体。下面的代码片段将展示如何处理所有这些问题:

```
 private static void IterateCADNodes(CadBaseEntity obj) 
{ 
  switch (obj.TypeName) { 
      case CadEntityTypeName.TEXT: 
          CadText childObjectText = (CadText) obj; 

          Console.WriteLine(childObjectText.DefaultValue); 

          break; 

      case CadEntityTypeName.MTEXT: 
          CadMText childObjectMText = (CadMText) obj; 

          Console.WriteLine(childObjectMText.Text); 

          break; 

      case CadEntityTypeName.INSERT: 
          CadInsertObject childInsertObject = (CadInsertObject) obj; 

          //Iterate over subnodes
          foreach (var tempobj in childInsertObject.ChildObjects) { 
              IterateCADNodes(tempobj); 
          } 
          break; 

      case CadEntityTypeName.ATTDEF: 
          CadAttDef attDef = (CadAttDef) obj; 

          Console.WriteLine(attDef.DefaultString); 
          break; 

      case CadEntityTypeName.ATTRIB: 
          CadAttrib attAttrib = (CadAttrib) obj; 

          Console.WriteLine(attAttrib.DefaultText); 
          break; 
  } 
} 
```

Enter fullscreen mode Exit fullscreen mode

要迭代块实体和非块实体，使用下面的代码:

```
public static void SearchTextInDWGAutoCADFile(CadImage cadImage) 
{ 

   // Search for text in the entities section 
   foreach (var entity in cadImage.Entities) { 
       IterateCADNodes(entity); 
   } 

   // Search for text in the block section 
   foreach (CadBlockEntity blockEntity in cadImage.BlockEntities.Values) { 
       foreach (var entity in blockEntity.Entities) { 
           IterateCADNodes(entity); 
       } 
   } 
} 
```

Enter fullscreen mode Exit fullscreen mode

目前就这些，敬请关注！

如需更多示例，请访问[网站。CAD GitHub](https://github.com/aspose-cad) 页面。Aspose.CAD 上还有 [Twitter](https://twitter.com/Asposecad) 和[脸书](https://www.facebook.com/AsposeCAD)的新闻页面