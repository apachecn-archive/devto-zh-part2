# Aspose 中几何图形的创建和验证。地理信息系统

> 原文：<https://dev.to/nnevod/creation-and-validation-of-geometry-in-asposegis-254m>

[Aspose。GIS](https://products.aspose.com/gis/) 以一种通用的方式处理地理空间数据，而不考虑存储格式，因为原则上所有的 GIS 数据格式都存储相同类型的数据。因此，创建和检查几何数据的方法与格式无关，可以在任何情况下使用。在本文中，我将概述如何在 Aspose 中创建几何数据。并检查其有效性。

## 创建点、线串和多边形

[点](https://apireference.aspose.com/net/gis/aspose.gis.geometries/point)只是一个点， [LineString](https://apireference.aspose.com/net/gis/aspose.gis.geometries/linestring) 是一条折线——一系列相连的点，ang [多边形](https://apireference.aspose.com/net/gis/aspose.gis.geometries/polygon)基本上是由闭合折线围成的区域。因此，Point 可以从另一个点用默认值或通过传递 X、Y 和可选的 Z 和 M 坐标来实例化。LineString 可以通过实例化它然后向它添加点来创建——要么将一个点传递给 [AddPoint](https://apireference.aspose.com/net/gis/aspose.gis.geometries/linestring/methods/addpoint) ，要么只传递坐标。

下面是一个创建点和线串的例子:

```
Point point = new Point(40.7128, -74.006);

LineString line = new LineString();

line.AddPoint(78.65, -32.65);

line.AddPoint(-98.65, 12.65);

line.AddPoint(point); 
```

多边形稍微复杂一点。它有外部边界，一个封闭的多段线，表示为 [LinearRing](https://apireference.aspose.com/net/gis/aspose.gis.geometries/linearring) 实例——这个类是一个确保它是封闭的并且不会自相交的 LineString 并且它可以有多个内部边界，本质上是其几何形状中的“洞”。这些也由线性化实例表示。外部边界放在[外圈](https://apireference.aspose.com/net/gis/aspose.gis.geometries/polygon/properties/exteriorring)属性，而内圈通过[add inter rioring](https://apireference.aspose.com/net/gis/aspose.gis.geometries/polygon/methods/addinteriorring)和[get inter rioring](https://apireference.aspose.com/net/gis/aspose.gis.geometries/polygon/methods/getinteriorring)方法访问，只有总计数表示为[interiorings count](https://apireference.aspose.com/net/gis/aspose.gis.geometries/polygon/properties/interiorringscount)属性。请看创建一个有洞的多边形的例子:

```
Polygon polygon = new Polygon();

LinearRing ring = new LinearRing();
ring.AddPoint(50.02, 36.22);
ring.AddPoint(49.99, 36.26);
ring.AddPoint(49.97, 36.23);
ring.AddPoint(49.98, 36.17);
ring.AddPoint(50.02, 36.22);

LinearRing hole = new LinearRing();
hole.AddPoint(50.00, 36.22);
hole.AddPoint(49.99, 36.20);
hole.AddPoint(49.98, 36.23);
hole.AddPoint(50.00, 36.24);
hole.AddPoint(50.00, 36.22);

polygon.ExteriorRing = ring;
polygon.AddInteriorRing(hole); 
```

## 创建几何图形集合

几何图形集合是以明显的方式创建的，创建一个集合并向其中添加元素:

```
Point point = new Point(40.7128, -74.006);

LineString line = new LineString();
line.AddPoint(78.65, -32.65);
line.AddPoint(-98.65, 12.65);

GeometryCollection geometryCollection = new GeometryCollection();
geometryCollection.Add(point);
geometryCollection.Add(line); 
```

## 键入几何图形集合

有一些特殊的集合只接受相应种类的几何元素:[多线](https://apireference.aspose.com/net/gis/aspose.gis.geometries/multilinestring)、[多点](https://apireference.aspose.com/net/gis/aspose.gis.geometries/multipoint)和[多多边形](https://apireference.aspose.com/net/gis/aspose.gis.geometries/multipolygon)。这些仅接受线串、点和多边形:

```
MultiPoint multipoint = new MultiPoint();
multipoint.Add(new Point(1, 2));
multipoint.Add(new Point(3, 4));

            LineString firstLine = new LineString();
firstLine.AddPoint(7.5, -3.5);
firstLine.AddPoint(-9.6, 12.6);

LineString secondLine = new LineString();
secondLine.AddPoint(8.5, -2.6);
secondLine.AddPoint(-8.6, 1.5);

MultiLineString multiLineString = new MultiLineString();
multiLineString.Add(firstLine);
multiLineString.Add(secondLine);

            LinearRing firstRing = new LinearRing();
firstRing.AddPoint(8.5, -2.5);
firstRing.AddPoint(-8.5, 2.5);
firstRing.AddPoint(8.5, -2.5);
Polygon firstPolygon = new Polygon(firstRing);

LinearRing secondRing = new LinearRing();
secondRing.AddPoint(7.6, -3.6);
secondRing.AddPoint(-9.6, 1.5);
secondRing.AddPoint(7.6, -3.6);
Polygon secondPolygon = new Polygon(secondRing);

MultiPolygon multiPolygon = new MultiPolygon();
multiPolygon.Add(firstPolygon);
multiPolygon.Add(secondPolygon); 
```

如您所见，创建几何体很容易。

## 几何验证

验证检查几何实例中的几何数据对于该实例的类型是否正确，即多边形是否有外部边界，是否包含线性环等。它是自动执行的，并且结果在[是有效的](https://apireference.aspose.com/net/gis/aspose.gis.geometries/geometry/properties/isvalid)属性处可用。看一看:

```
LinearRing linearRing = new LinearRing();
linearRing.AddPoint(0, 0);
linearRing.AddPoint(0, 1);
linearRing.AddPoint(1, 0);
bool valid = linearRing.IsValid; // valid == false , as ring is not enclosed
linearRing.AddPoint(0, 0);
valid = linearRing.IsValid; // valid == true, as ring is now enclosed 
```

此外，还有一个简单性检查，就[而言——比如没有自交，等等。它的工作方式与验证相同，可在](http://www.opengeospatial.org/standards/sfa) [的简单](https://apireference.aspose.com/net/gis/aspose.gis.geometries/geometry/properties/issimple)属性中使用。:

```
 LineString lineString = new LineString();
            bool simple = lineString.IsSimple; // simple == true
            lineString.AddPoint(0, 0);
            lineString.AddPoint(1, 0);
            simple = lineString.IsSimple; // simple == true
            lineString.AddPoint(0.5, 0);
            simple = lineString.IsSimple; // simple == false (line string crosses itself) 
```

目前就这些，敬请关注！

如需更多示例，请访问[网站。GIS GitHub](https://github.com/aspose-gis) 页面。Aspose.GIS 上还有 [Twitter](https://twitter.com/AsposeGis) 和[脸书](https://www.facebook.com/AsposeGis/)的新闻页面