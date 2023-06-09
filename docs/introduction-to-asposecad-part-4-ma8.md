# Aspose 简介。CAD，第 4 部分

> 原文：<https://dev.to/nnevod/introduction-to-asposecad-part-4-ma8>

Aspose 简介。CAD，第 4 部分

在这一部分中，我将继续把[描述为一只蜘蛛。CAD](https://products.aspose.com/cad/)DWG \ DXF 文件特有的功能，因为这些文件格式是 Aspose.CAD 最常用的格式

## 迭代实体和块

DWG\DXF 特定的 [CadImage](https://apireference.aspose.com/net/cad/aspose.cad.fileformats.cad/cadimage) 类有一个[实体](https://apireference.aspose.com/net/cad/aspose.cad.fileformats.cad/cadimage/properties/entities)属性，它包含文件的所有实体。特定于 DWG 格式的块作为 [CadBlockEntity](https://apireference.aspose.com/net/cad/aspose.cad.fileformats.cad.cadobjects/cadblockentity) 的实例放入 [CadBlockDictionary](https://apireference.aspose.com/net/cad/aspose.cad.fileformats.cad/cadblockdictionary) 实例中，该实例可通过 CadImage 的 [BlockEntities](https://apireference.aspose.com/net/cad/aspose.cad.fileformats.cad/cadimage/properties/blockentities) 属性访问。块名在字典中用作关键字，因此可以通过块名来访问块。可以通过 CadBlockEntity 的[实体](https://apireference.aspose.com/net/cad/aspose.cad.fileformats.cad.cadobjects/cadblockentity/properties/entities)属性访问特定块的实体。

```
// Load an existing DWG file as CadImage.
using (Aspose.CAD.FileFormats.Cad.CadImage cadImage 
    = (Aspose.CAD.FileFormats.Cad.CadImage)Aspose.CAD.Image.Load(sourceFilePath))
{
  // Go through each entity inside the DWG/DXF file
    foreach (Aspose.CAD.FileFormats.Cad.CadObjects.CadBaseEntity entity in image.Entities)
    {
        Console.WriteLine(entity.TypeName);
    }

//Get information about the block by its name
    System.Console.WriteLine(cadImage.BlockEntities["*MODEL_SPACE"].Description);
} 
```

Enter fullscreen mode Exit fullscreen mode

## 获取衬底信息

参考底图像任何其他实体一样存储，由 [CadUnderlay](https://apireference.aspose.com/net/cad/aspose.cad.fileformats.cad.cadobjects/cadunderlay) 类表示。因此，获取有关参考底图的信息就像这样简单:

```
// Load an existing DWG file and convert it into CadImage 
using (Aspose.CAD.FileFormats.Cad.CadImage image 
        = (Aspose.CAD.FileFormats.Cad.CadImage)Image.Load(fileName))
{
    // Go through each entity inside the DWG file
    foreach (Aspose.CAD.FileFormats.Cad.CadObjects.CadBaseEntity entity in image.Entities)
    {
        // Check if entity is of CadDgnUnderlay type
        if (entity is Aspose.CAD.FileFormats.Cad.CadObjects.CadDgnUnderlay)
        {
            // Access different underlay flags 
            Aspose.CAD.FileFormats.Cad.CadObjects.CadUnderlay underlay 
                = entity as Aspose.CAD.FileFormats.Cad.CadObjects.CadUnderlay;
            //This is the external path to underlay file
            Console.WriteLine(underlay.UnderlayPath);
            Console.WriteLine(underlay.UnderlayName);
            Console.WriteLine(underlay.InsertionPoint.X);
            Console.WriteLine(underlay.InsertionPoint.Y);
            Console.WriteLine(underlay.RotationAngle);
            Console.WriteLine(underlay.ScaleX);
            Console.WriteLine(underlay.ScaleY);
            Console.WriteLine(underlay.ScaleZ);
            //Accessing the packed flags
            Console.WriteLine(
                (underlay.Flags & Aspose.CAD.FileFormats.Cad.CadObjects.UnderlayFlags.UnderlayIsOn)
                == Aspose.CAD.FileFormats.Cad.CadObjects.UnderlayFlags.UnderlayIsOn);
            Console.WriteLine(
                (underlay.Flags & Aspose.CAD.FileFormats.Cad.CadObjects.UnderlayFlags.ClippingIsOn)
                == Aspose.CAD.FileFormats.Cad.CadObjects.UnderlayFlags.ClippingIsOn);
            Console.WriteLine(
                (underlay.Flags & Aspose.CAD.FileFormats.Cad.CadObjects.UnderlayFlags.Monochrome)
                != Aspose.CAD.FileFormats.Cad.CadObjects.UnderlayFlags.Monochrome);
            break;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

唯一更复杂的细微差别是访问打包成位字段的标志，这在本例中有所涉及。

## 将块与布局关联

[CadBlockEntity](https://apireference.aspose.com/net/cad/aspose.cad.fileformats.cad.cadobjects/cadblockentity) 与布局的关联存储在 [CadBlockTableObject](https://apireference.aspose.com/net/cad/aspose.cad.fileformats.cad.cadtables/cadblocktableobject) 的实例中，列在 CadImage 的 [BlocksTables](https://apireference.aspose.com/net/cad/aspose.cad.fileformats.cad/cadimage/properties/blockstables) 属性下。CadBlockTableObject 有一个[hardpointertolautation](https://apireference.aspose.com/net/cad/aspose.cad.fileformats.cad.cadtables/cadblocktableobject/properties/hardpointertolayout)属性，它包含一个[布局](https://apireference.aspose.com/net/cad/aspose.cad.fileformats.cad.cadobjects/cadlayout)的 [ObjectHandle](https://apireference.aspose.com/net/cad/aspose.cad.fileformats.cad.cadobjects/cadbase/properties/objecthandle) 属性的值(不是引用，只是相同的字符串值)。请注意，文件中的每个对象，无论是实体、表或块，还是对象的[属性](https://apireference.aspose.com/net/cad/aspose.cad.fileformats.cad.cadobjects/cadobjectattribute)，都有 ObjectHandle 属性。另一个 CadBlockTableObject 的属性， [BlockName](https://apireference.aspose.com/net/cad/aspose.cad.fileformats.cad.cadtables/cadblocktableobject/properties/blockname) 包含块的名称，可以用来从 CadImage 的 BlockEntities 中获取块。

```
foreach (Aspose.CAD.FileFormats.Cad.CadObjects.CadLayout layout in layouts.Values)
{
    layoutNames[i++] = layout.LayoutName;
    System.Console.WriteLine("Layout " + layout.LayoutName + " is found");

    // Find block, applicable for DWG only
    Aspose.CAD.FileFormats.Cad.CadTables.CadBlockTableObject blockTableObjectReference
     = null;
    foreach (Aspose.CAD.FileFormats.Cad.CadTables.CadBlockTableObject tableObject
                in cadImage.BlocksTables)
    {
        if (string.Equals(tableObject.HardPointerToLayout, layout.ObjectHandle))
        {
            blockTableObjectReference = tableObject;
            break;
        }
    }

     // Collection cadBlockEntity.Entities contains information
    //about all entities on specific layout if this
    //collection has no elements it means layout is a copy
    // of Model layout and contains the same entities
    Aspose.CAD.FileFormats.Cad.CadObjects.CadBlockEntity cadBlockEntity = 
    cadImage.BlockEntities[blockTableObjectReference.BlockName];
} 
```

Enter fullscreen mode Exit fullscreen mode

目前就这些，敬请关注！

如需更多示例，请访问[网站。CAD GitHub](https://github.com/aspose-cad) 页面。Aspose.CAD 上还有 [Twitter](https://twitter.com/Asposecad) 和[脸书](https://www.facebook.com/AsposeCAD)的新闻页面