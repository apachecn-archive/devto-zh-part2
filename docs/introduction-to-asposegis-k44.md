# Aspose 简介。地理信息系统

> 原文：<https://dev.to/nnevod/introduction-to-asposegis-k44>

[Aspose。GIS](https://products.aspose.com/gis) 是一个用于。NET 平台，执行与地理空间数据文件相关的所有常见任务:它可以在支持的数据格式之间转换，可以在不同的空间参考系统之间转换地理空间数据，甚至允许使用自定义参考系统，提供方便的 API 来读取和编辑文件中的地理空间数据及其属性，还可以从头开始创建地理空间数据。不止如此，阿普斯。GIS 可以对地理空间数据进行一些基本的分析，例如确定几何实体的交集和重叠，以及验证地理空间数据。
在这里，我将展示如何使用 Aspose.GIS 的这些功能的基本示例。稍后，我将在单独的文章中更深入地探讨这些功能。

## 使用 Aspose.GIS 的概念

Aspose 背后的主要概念。GIS 是地理空间数据及其属性的一种通用中间表示，与所使用的特定数据格式无关。这是一种自然的方法，因为所有的 GIS 文件格式都表示同一种数据。例如，GIS 数据文件中的一整套数据被表示为 [VectorLayer](https://apireference.aspose.com/net/gis/aspose.gis/vectorlayer/) 类实例，其中包含表示为要素类实例的 GIS 要素，等等。(GDB 文件包含许多层，它们将在下面被覆盖。)这种方法有助于以相同的方式处理所有支持的文件格式。

## 不同地理数据格式之间的转换。

让我们看看如何将数据从一种格式转换成另一种格式。这个操作非常简单，因为在 VectorLayer 类中有一个特殊的静态 [Convert](https://apireference.aspose.com/net/gis/aspose.gis/vectorlayer/methods/convert) 方法，它接受输入文件路径、输入文件格式、输出文件路径和输出文件格式。通过选择相应文件格式的驱动程序来指定文件格式，它们可以作为[驱动程序](https://apireference.aspose.com/net/gis/aspose.gis/drivers/)类的属性。目前有 [GeoJson](https://apireference.aspose.com/net/gis/aspose.gis/drivers/properties/geojson) ， [Kml](https://apireference.aspose.com/net/gis/aspose.gis/drivers/properties/kml) ， [Shapefile](https://apireference.aspose.com/net/gis/aspose.gis/drivers/properties/shapefile) ，[OS XML](https://apireference.aspose.com/net/gis/aspose.gis/drivers/properties/osmxml)， [Gpx](https://apireference.aspose.com/net/gis/aspose.gis/drivers/properties/gpx) ， [Gml](https://apireference.aspose.com/net/gis/aspose.gis/drivers/properties/gml) 和 [FileGdb](https://apireference.aspose.com/net/gis/aspose.gis/drivers/properties/filegdb) 对应文件格式的驱动。或者，您可以将选项指定为 ConversionOptions 的实例，该实例提供对属性转换、目标空间参考系统以及输入和目标文件格式处理的控制。看一下例子:

```
public void ConvertFormats()
{
    ConversionOptions options = new ConversionOptions();

    //Convert shapefile to GeoJSON
    VectorLayer.Convert("input.shp", Drivers.Shapefile, "output_out.json", Drivers.GeoJson);

    //Now let's try to change attributes during conversion and specify custom attribute converter
    options.AttributesConverter = new AttributesConverterExample();

    //Convert GeoJSON to shapefile using ConversionOptions with custom converter
    VectorLayer.Convert("input.json", Drivers.GeoJson,
    "ConvertGeoJSONToShapeFileWithAttributeAdjustment_out.shp", Drivers.Shapefile, options);
}

private class AttributesConverterExample : IAttributesConverter
{
    //This will simply be called for every attribute in the input file, allowing you to modify the attribute.
    public void ModifyAttribute(FeatureAttribute attribute)
    {
        switch (attribute.Name)
        {
            case "name":
                attribute.Width = 10;
                break;
            case "age":
                attribute.Width = 3;
                attribute.Precision = 0;
                break;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 使用空间参考系统

空间参考系统表示为 [SpatialReferenceSystem](https://apireference.aspose.com/net/gis/aspose.gis.spatialreferencing/spatialreferencesystem) 类的实例，也通过该类的静态方法进行管理。公共 Sr 可作为 SpatialReferenceSystem 类的属性，您也可以通过提供 WKT 描述或实例化[ProjectedSpatialReferenceSystemParameters](https://apireference.aspose.com/net/gis/aspose.gis.spatialreferencing/projectedspatialreferencesystemparameters)类来创建自己的 Sr。看一下例子:

#### 从 WKT 创建一个 SRS 实例，并将其与预定义的 WGS84 SRS 进行比较:

```
 string wkt = @"
GEOGCS[""WGS 84"",
    DATUM[""WGS_1984"",
        SPHEROID[""WGS 84"",6378137,298.257223563,
            AUTHORITY[""EPSG"",""7030""]],
        AUTHORITY[""EPSG"",""6326""]],
    PRIMEM[""Greenwich"",0,
        AUTHORITY[""EPSG"",""8901""]],
    UNIT[""degree"",0.01745329251994328,
        AUTHORITY[""EPSG"",""9122""]],
    AUTHORITY[""EPSG"",""4326""]]
";
            var srs = SpatialReferenceSystem.CreateFromWkt(wkt);
            srs.IsEquivalent(SpatialReferenceSystem.Wgs84); // true 
```

Enter fullscreen mode Exit fullscreen mode

#### 通过 API 创建 SRS 并导出到 WKT

```
var parameters = new ProjectedSpatialReferenceSystemParameters
{
    Name = "WGS 84 / World Mercator",
    Base = SpatialReferenceSystem.Wgs84,
    ProjectionMethodName = "Mercator_1SP",
    LinearUnit = Unit.Meter,
    XAxis = new Axis("Easting", AxisDirection.East),
    YAxis = new Axis("Northing", AxisDirection.North),
    AxisesOrder = ProjectedAxisesOrder.XY,
};
parameters.AddProjectionParameter("central_meridian", 0);
parameters.AddProjectionParameter("scale_factor", 1);
parameters.AddProjectionParameter("false_easting", 100);
parameters.AddProjectionParameter("false_northing", 100);

var projectedSrs = SpatialReferenceSystem.CreateProjected(parameters, Identifier.Epsg(3395));

string wkt = projectedSrs.ExportToWkt();
Console.WriteLine(wkt); 
```

Enter fullscreen mode Exit fullscreen mode

#### 检查文件格式中特定 SRS 的支持

```
Drivers.Shapefile.SupportsSpatialReferenceSystem(SpatialReferenceSystem.Wgs72); // true
Drivers.GeoJson.SupportsSpatialReferenceSystem(SpatialReferenceSystem.Wgs84); // true
Drivers.GeoJson.SupportsSpatialReferenceSystem(SpatialReferenceSystem.Wgs72); // false 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，在 Aspose 中使用 SRS。GIS 很简单。

## 从地理数据文件中读取要素

文件作为 VectorLayer 类的实例加载。有两种加载文件的方法:要么调用对应于您的文件格式的 Drivers 类的静态属性上的 [OpenLayer](https://apireference.aspose.com/net/gis/aspose.gis/driver/methods/openlayer) ，指定文件的路径，要么调用 VectorLayer 类的静态 [Open](https://apireference.aspose.com/net/gis/aspose.gis/vectorlayer/methods/open) 方法，指定文件的路径和对应的文件格式驱动程序(同样，通过 Drivers 类的静态属性)。这里的例子显示了第一种方法。
地理空间特征可通过在 VectorLayer 实例上迭代来获得。几何数据存储在[特征](https://apireference.aspose.com/net/gis/aspose.gis/feature/)的[几何](https://apireference.aspose.com/net/gis/aspose.gis/feature/properties/geometry)属性中，可以使用 [GetValue < T > ()](https://apireference.aspose.com/net/gis/aspose.gis/feature/methods/getvalue/_1) 方法
读取属性

```
using (var layer = Drivers.OsmXml.OpenLayer(dataDir + "fountain.osm"))
{
    // get feratures count
    int count = layer.Count;

    Console.WriteLine("Layer count: " + count);
    // get feature at index 2
    Feature featureAtIndex2 = layer[2];

    // iterate through all features.
    foreach (Feature feature in layer)
    {
        // handle feature
        Console.WriteLine(feature.Geometry.GeometryType);

        //read attribute and display if it's present
        string data = featureAtIndex2.GetValue<string>("string_data");
        if(data!=null)
            Console.WriteLine(data);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 处理多层 GDB 文件

由于 GDB 文件包含许多层，它们必须被不同地处理。它们被表示为[数据集](https://apireference.aspose.com/net/gis/aspose.gis/dataset)类的实例，加载过程类似于 VectorLayer:调用数据集的静态 [Open](https://apireference.aspose.com/net/gis/aspose.gis/dataset/methods/open) 方法提供文件和文件格式驱动的路径，或者改为调用[驱动](https://apireference.aspose.com/net/gis/aspose.gis/driver)的 [OpenDataset](https://apireference.aspose.com/net/gis/aspose.gis/driver/methods/opendataset) 方法。目前只有 GDB 驱动程序使用数据集。开放，但必须指定统一性。然后，可以使用 [OpenLayerAt](https://apireference.aspose.com/net/gis/aspose.gis/dataset/methods/openlayerat) 方法拾取单独的层，该方法采用层索引。总层数存储在 [LayersCount](https://apireference.aspose.com/net/gis/aspose.gis/dataset/properties/layerscount) 属性中。也可以使用 [OpenLayer](https://apireference.aspose.com/net/gis/aspose.gis/dataset/methods/openlayer) 方法按名称加载层。

```
using (var dataset = Dataset.Open(dataDir + "ThreeLayers.gdb", Drivers.FileGdb))
{
    Console.WriteLine("FileGDB has {0} layers", dataset.LayersCount);
    for (int i = 0; i < dataset.LayersCount; ++i)
    {
        Console.WriteLine("Layer {0} name: {1}", i, dataset.GetLayerName(i));

        using (var layer = dataset.OpenLayerAt(i))
        {
            Console.WriteLine("Layer has {0} features", layer.Count);
            foreach (var feature in layer)
            {
                Console.WriteLine(feature.Geometry);
            }
        }
        Console.WriteLine("");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 创建一个新的地理数据文件并用数据填充它

创建的方式类似于打开现有文件，只是使用了 [Create()](https://apireference.aspose.com/net/gis/aspose.gis/vectorlayer/methods/create) 方法而不是 Open()。创建后，您就有了一个可以添加属性和特征的 VectorLayer 实例。当您处置该对象时，更改将被推送到文件中。

```
using (VectorLayer layer = VectorLayer.Create("ShapeFile.shp", Drivers.Shapefile))
{
    // add attributes before adding features
    layer.Attributes.Add(new FeatureAttribute("name", AttributeDataType.String));
    layer.Attributes.Add(new FeatureAttribute("age", AttributeDataType.Integer));
    layer.Attributes.Add(new FeatureAttribute("dob", AttributeDataType.DateTime));

    Feature firstFeature = layer.ConstructFeature();
    firstFeature.Geometry = new Point(33.97, -118.25);
    firstFeature.SetValue("name", "John");
    firstFeature.SetValue("age", 23);
    firstFeature.SetValue("dob", new DateTime(1982, 2,5, 16, 30,0));
    layer.Add(firstFeature);

    Feature secondFeature = layer.ConstructFeature();
    secondFeature.Geometry = new Point(35.81, -96.28);
    secondFeature.SetValue("name", "Mary");
    secondFeature.SetValue("age", 54);
    secondFeature.SetValue("dob", new DateTime(1984, 12, 15, 15, 30, 0));
    layer.Add(secondFeature);
} 
```

Enter fullscreen mode Exit fullscreen mode

## 基本分析:检查交点

几何类为基本几何分析提供了实例方法。在这个例子中，我将展示使用 [Intersects()](https://apireference.aspose.com/net/gis/aspose.gis.geometries/geometry/methods/intersects) 和 [Disjoint()](https://apireference.aspose.com/net/gis/aspose.gis.geometries/geometry/methods/disjoint) 方法，但也有其他方法。

```
//Create geometry first.
var geometry1 = new Polygon(new LinearRing(new[]
{
    new Point(0, 0),
    new Point(0, 3),
    new Point(3, 3),
    new Point(3, 0),
    new Point(0, 0),
}));

var geometry2 = new Polygon(new LinearRing(new[]
{
    new Point(1, 1),
    new Point(1, 4),
    new Point(4, 4),
    new Point(4, 1),
    new Point(1, 1),
}));

Console.WriteLine(geometry1.Intersects(geometry2)); // True
Console.WriteLine(geometry2.Intersects(geometry1)); // True

// 'Disjoint' is opposite to 'Intersects'
Console.WriteLine(geometry1.Disjoint(geometry2)); // False 
```

Enter fullscreen mode Exit fullscreen mode

### 支持的文件格式

阿斯波斯。GIS 可以读取和创建 GeoJSON 文件、ESRI shape File 和文件地理数据库、Keyhole 标记语言(KML)文件、OpenStreetMap XML (OSM XML)文件、地理标记语言(GML)文件，还可以读取 GPS 交换格式(GPX)文件。

### 支持的平台

阿斯波斯。地理信息系统可用于。NET Framework 4.7 和更高版本，以及。NET Standard 2.0 及更高版本。因此，它可以在以下平台上工作。NET 框架，。NET Core，Mono 5.4 以上，Xamarin.iOS 10.14 以上，Xamarin。Mac 3.8 或更高版本和 Xamarin。Android 8.0 或更高版本。

目前就这些，敬请关注！

如需更多示例，请访问[网站。GIS GitHub](https://github.com/aspose-gis) 页面。Aspose.GIS 上还有 [Twitter](https://twitter.com/AsposeGis) 和[脸书](https://www.facebook.com/AsposeGis/)的新闻页面