# Aspose 中的几何分析功能。地理信息系统

> 原文：<https://dev.to/nnevod/geometry-analysis-features-in-asposegis-1hmd>

[Aspose。GIS](https://products.aspose.com/gis) 不仅可以在文件格式之间转换地理空间数据，允许创建和读取数据，还允许对数据进行一些分析。这里我将把 gow 描述为一个 pose。GIS 可用于更详细的地理空间数据分析。

地理空间要素被表示为[几何](https://apireference.aspose.com/net/gis/aspose.gis.geometries/geometry)类- [点](https://apireference.aspose.com/net/gis/aspose.gis.geometries/point)、[表面](https://apireference.aspose.com/net/gis/aspose.gis.geometries/surface)、[线串](https://apireference.aspose.com/net/gis/aspose.gis.geometries/linestring)(折线)、[线性](https://apireference.aspose.com/net/gis/aspose.gis.geometries/linearring)、[多边形](https://apireference.aspose.com/net/gis/aspose.gis.geometries/polygon)、[多点](https://apireference.aspose.com/net/gis/aspose.gis.geometries/multipoint)、[多线串](https://apireference.aspose.com/net/gis/aspose.gis.geometries/multilinestring)、[多多边形](https://apireference.aspose.com/net/gis/aspose.gis.geometries/multipolygon)和[几何集合](https://apireference.aspose.com/net/gis/aspose.gis.geometries/geometrycollection)的子类。用于分析的方法由几何类提供，因此可用于任何地理空间要素。在本文中，我将描述 Geometry 类为分析提供了哪些方法。

## 属性

首先，让我们回顾一下 Geometry 类提供了哪些属性:

#### [圆坐标](https://apireference.aspose.com/net/gis/aspose.gis.geometries/geometry/properties/coordinatedimension)

获取此几何图形的坐标维数。即只有 X 和 Y 坐标时为 2，有 X、Y 和 Z 或 M 坐标时为 3，等等。

#### [维度](https://apireference.aspose.com/net/gis/aspose.gis.geometries/geometry/properties/dimension)

获取几何图形的拓扑尺寸，即它是点、线还是面。

#### [t1](#geometrytype)[几何类型](https://apireference.aspose.com/net/gis/aspose.gis.geometries/geometry/properties/geometrytype)

获取几何的类型——本质上是实例的类的名称——如果它是多边形，GeometryType 将返回 [GeometryType](https://apireference.aspose.com/net/gis/aspose.gis.geometries/geometrytype) .Polygon。

#### [哈斯姆](https://apireference.aspose.com/net/gis/aspose.gis.geometries/geometry/properties/hasm)

获取此几何图形是否有 M 坐标。
M 坐标(测量值)是传达地理要素信息的值，它与定义要素位置的坐标一起存储。
例如，假设您在应用程序中表示高速公路。如果希望应用程序处理表示直线距离或里程标的值，可以将这些值与定义公路沿线位置的坐标一起存储。

#### [t1](#hasz)[用](https://apireference.aspose.com/net/gis/aspose.gis.geometries/geometry/properties/hasz)

获取此几何图形是否有 Z 坐标(高程)。

#### [是空的](https://apireference.aspose.com/net/gis/aspose.gis.geometries/geometry/properties/isempty)

获取此几何图形是否为空(即不包含地理空间数据)。

#### [is example](https://apireference.aspose.com/net/gis/aspose.gis.geometries/geometry/properties/issimple)

获取这个几何图形在 [SFA](http://www.opengeospatial.org/standards/sfa) 项中是否简单。
这里有一个例子:

```
LineString lineString = new LineString();
bool simple = lineString.IsSimple; // simple == true
lineString.AddPoint(0, 0);
lineString.AddPoint(1, 0);
simple = lineString.IsSimple; // simple == true
lineString.AddPoint(0.5, 0);
simple = lineString.IsSimple; // simple == false (line string crosses itself) 
```

#### [IsValid](https://apireference.aspose.com/net/gis/aspose.gis.geometries/geometry/properties/isvalid)

获取此几何图形是否有效。这里有一个例子:

```
LinearRing linearRing = new LinearRing();
linearRing.AddPoint(0, 0);
linearRing.AddPoint(0, 1);
linearRing.AddPoint(1, 0);
bool valid = linearRing.IsValid; // valid == false
linearRing.AddPoint(0, 0);
valid = linearRing.IsValid; // valid == true 
```

#### [Null](https://apireference.aspose.com/net/gis/aspose.gis.geometries/geometry/properties/null)

返回空几何的静态方法。

#### [空间参考系统](https://apireference.aspose.com/net/gis/aspose.gis.geometries/geometry/properties/spatialreferencesystem)

获取该几何体的 [SpatialReferenceSystem](https://apireference.aspose.com/net/gis/aspose.gis.spatialreferencing/spatialreferencesystem) 。如果 SRS 未知，它可以为空。分配新的服务请求不会执行任何转换，只会更改引用。

## 基本属性分析方法

#### [GetArea](https://apireference.aspose.com/net/gis/aspose.gis.geometries/geometry/methods/getarea)

获取几何图形的面积，如果是集合，则为所有面积的总和。

#### [GetDistanceTo](https://apireference.aspose.com/net/gis/aspose.gis.geometries/geometry/methods/getdistanceto)

获取从该几何图形到指定的其他几何图形的最近距离。如果至少有一个几何图形为空，则返回-1。

#### [GetLength](https://apireference.aspose.com/net/gis/aspose.gis.geometries/geometry/methods/getlength)

获取几何图形的长度。如果是多边形，则返回周长。如果是集合，则返回所有长度的总和。

## 用于布尔关系的方法

#### [被](https://apireference.aspose.com/net/gis/aspose.gis.geometries/geometry/methods/coveredby)覆盖

如果此几何图形在空间上被另一个几何图形覆盖，则返回 true，否则返回 false。

#### [封面](https://apireference.aspose.com/net/gis/aspose.gis.geometries/geometry/methods/covers)

如果此几何图形在空间上覆盖了其他几何图形，则返回 true。本质上与被此几何图形和其他几何图形交换覆盖的相同。

#### [穿越](https://apireference.aspose.com/net/gis/aspose.gis.geometries/geometry/methods/crosses)

测试两个几何图形是否相交，即它们有一些但不是所有的公共内点，并且相交的维数小于至少一个几何图形的维数，例如，相交是一条线，而一个几何图形是一个表面。这里有一个例子:

```
var geometry1 = new LineString();
geometry1.AddPoint(0, 0);
geometry1.AddPoint(2, 2);

var geometry2 = new LineString();
geometry2.AddPoint(1, 1);
geometry2.AddPoint(3, 3);

Console.WriteLine(geometry1.Crosses(geometry2)); // False

var geometry3 = new LineString();
geometry3.AddPoint(0, 2);
geometry3.AddPoint(2, 0);

Console.WriteLine(geometry1.Crosses(geometry3)); // True 
```

#### [不相交](https://apireference.aspose.com/net/gis/aspose.gis.geometries/geometry/methods/disjoint)

如果两个几何图形没有公共点，则返回 true。

#### [相交](https://apireference.aspose.com/net/gis/aspose.gis.geometries/geometry/methods/intersects)

不相交的反义词。这里有一个例子:

```
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

#### [重叠](https://apireference.aspose.com/net/gis/aspose.gis.geometries/geometry/methods/overlaps)

测试几何图形是否重叠，因为它们有一些共同的内部点，并且交集与几何图形具有相同的维度，例如，几何图形是曲面，交集也是曲面。如果几何尺寸不同，则始终为 false。这里有一个例子:

```
var geometry1 = new LineString();
geometry1.AddPoint(0, 0);
geometry1.AddPoint(0, 2);

var geometry2 = new LineString();
geometry2.AddPoint(0, 2);
geometry2.AddPoint(0, 3);

Console.WriteLine(geometry1.Overlaps(geometry2)); // False

var geometry3 = new LineString();
geometry3.AddPoint(0, 1);
geometry3.AddPoint(0, 3);

Console.WriteLine(geometry1.Overlaps(geometry3)); // True 
```

#### [空间上包含](https://apireference.aspose.com/net/gis/aspose.gis.geometries/geometry/methods/spatiallycontains)

测试该几何体是否在空间上包含另一个几何体。这里有一个例子:

```
var geometry1 = new Polygon();
geometry1.ExteriorRing = new LinearRing(new[]
{
    new Point(0, 0),
    new Point(0, 4),
    new Point(4, 4),
    new Point(4, 0),
    new Point(0, 0),
});
geometry1.AddInteriorRing(new LinearRing(new[]
{
    new Point(1, 1),
    new Point(1, 3),
    new Point(3, 3),
    new Point(3, 1),
    new Point(1, 1),
}));

var geometry2 = new Point(2, 2);

Console.WriteLine(geometry1.SpatiallyContains(geometry2)); // False

var geometry3 = new Point(0.5, 0.5);
Console.WriteLine(geometry1.SpatiallyContains(geometry3)); // True

// 'a.SpatiallyContains(b)' equals to 'b.Within(a)'
Console.WriteLine(geometry3.Within(geometry1)); // True 
```

#### [在空间上等于](https://apireference.aspose.com/net/gis/aspose.gis.geometries/geometry/methods/spatiallyequals)

测试两个几何图形在空间上是否相等。本质上，测试两个几何图形在投影到二维空间时是否占据相同的空间。这里有一个例子:

```
var geometry1 = new MultiLineString
{
    new LineString(new [] { new Point(0, 0), new Point(1, 1) }),
    new LineString(new [] { new Point(1, 1), new Point(2, 2) }),
};

var geometry2 = new LineString(new[]
{
    new Point(0, 0), new Point(2, 2),
});

Console.WriteLine(geometry1.SpatiallyEquals(geometry2)); // True

geometry2.AddPoint(3, 3);
Console.WriteLine(geometry1.SpatiallyEquals(geometry2)); // False 
```

#### [触动](https://apireference.aspose.com/net/gis/aspose.gis.geometries/geometry/methods/touches)

如果两个几何图形至少有一个公共边界点，但没有内部点，则返回 true。这里有一个例子:

```
var geometry1 = new LineString();
geometry1.AddPoint(0, 0);
geometry1.AddPoint(2, 2);

var geometry2 = new LineString();
geometry2.AddPoint(2, 2);
geometry2.AddPoint(3, 3);

Console.WriteLine(geometry1.Touches(geometry2)); // True
Console.WriteLine(geometry2.Touches(geometry1)); // True

var geometry3 = new Point(2, 2);
Console.WriteLine(geometry1.Touches(geometry3)); // True

var geometry4 = new LineString();
geometry4.AddPoint(1, 1);
geometry4.AddPoint(4, 4);

Console.WriteLine(geometry1.Touches(geometry4)); // False 
```

#### 内

测试几何图形是否包含在另一个几何图形中。等同于交换了此几何和其他几何的 SpatiallyContains。

## 衍生几何图形的生成方法

#### [差异](https://apireference.aspose.com/net/gis/aspose.gis.geometries/geometry/methods/difference)

返回此几何图形与参数几何图形之差的几何图形，包含此几何图形中不包含在参数几何图形中的点。

#### [t1](#getbuffer)[山羊缓冲剂](https://apireference.aspose.com/net/gis/aspose.gis.geometries/geometry/methods/getbuffer)

返回包含距此几何图形指定距离内所有点的几何图形。当你需要一个围绕几何图形的“边界区域”时很有用。

#### [获取质心](https://apireference.aspose.com/net/gis/aspose.gis.geometries/geometry/methods/getcentroid)

返回几何图形的几何中心，通俗地说，就是形状的剪切点可以完美地平衡在大头针上的点。

#### [【get conviction】](https://apireference.aspose.com/net/gis/aspose.gis.geometries/geometry/methods/getconvexhull)

返回几何图形的凸包。如果几何图形为空，则为 Null 如果几何图形为点，则为两点，然后在两点之间连线，否则为围绕所有点的凸包。

#### [路口](https://apireference.aspose.com/net/gis/aspose.gis.geometries/geometry/methods/intersection)

返回包含点的几何图形，这些点包含在此几何图形和自变量几何图形中。

#### [同异](https://apireference.aspose.com/net/gis/aspose.gis.geometries/geometry/methods/symdifference)

返回包含点的几何图形，这些点包含在此几何图形或参数几何图形中，但不同时包含在两者中。

#### [联盟](https://apireference.aspose.com/net/gis/aspose.gis.geometries/geometry/methods/union)

返回是此几何和参数几何之和的几何，包含来自这两个几何的点。

## 一般情况关系，DE-9IM 模型。

[Relate](https://apireference.aspose.com/net/gis/aspose.gis.geometries/geometry/methods/relate) 方法允许用户为 [DE-9IM](https://en.wikipedia.org/wiki/DE-9IM) 交集矩阵指定任何期望的模式。事实上，上述大多数关系派生方法都是该方法的包装器。方法为两个几何图形构建 DE-9IM 矩阵，并测试它是否符合提供的模式。
图案是一串 9 个字符。
每个位置代表特定的空间关系:

*   0 -几何图形内部之间。
*   1 -在此几何图形的内部和另一个几何图形的边界之间。
*   2 -在此几何图形的内部和另一个几何图形的外部之间。
*   3 -在此几何图形的边界和另一个几何图形的内部之间。
*   4 -几何图形的边界之间。
*   5 -此几何图形的边界与另一个几何图形的外部之间。
*   6 -在此几何图形的外部和另一个几何图形的内部之间。
*   7 -在此几何图形的外部和另一个几何图形的边界之间。
*   8 -几何图形外部之间。

每个字符可以有以下值:

*   * -任何值
*   F -无交叉
*   任何十字路口
*   0 点相交(共享点)
*   单线相交(共享线段)
*   2 -曲面相交(多边形的共享部分)

例如，相交模式“F0*******”意味着几何图形内部之间不应有相交，几何图形边界之间的相交必须是一个点。你也可以查看 [OpenGIS 简单特征规范](http://www.opengeospatial.org/standards/sfa)了解更多细节。这里有一个如何使用它的例子:

```
var geometry1 = new LineString();
geometry1.AddPoint(0, 0);
geometry1.AddPoint(0, 2);

var geometry2 = new LineString();
geometry2.AddPoint(0, 1);
geometry2.AddPoint(0, 3);

// Relate method takes a string representation of DE-9IM matrix
// (Dimensionally Extended Nine-Intersection Model matrix).
// see Simple Feature Access specification for more details on DE-9IM.

// this is the equivalent of 'geometry1.SpatiallyEquals(geometry2)'
Console.WriteLine(geometry1.Relate(geometry2, "T*F**FFF*")); // False

// this is the equivalent of 'geometry1.Disjoint(geometry2)'
Console.WriteLine(geometry1.Relate(geometry2, "FF*FF****")); // False

// this is the equivalent of 'geometry1.Overlaps(geometry2)'
Console.WriteLine(geometry1.Relate(geometry2, "1*T***T**")); // True 
```

## 杂法

#### [RoundM](https://apireference.aspose.com/net/gis/aspose.gis.geometries/geometry/methods/roundm)

将 M 坐标舍入到指定的小数位数。

#### [RoundXY](https://apireference.aspose.com/net/gis/aspose.gis.geometries/geometry/methods/roundxy)

将 X 和 Y 坐标舍入到指定的小数位数。

#### [回合](https://apireference.aspose.com/net/gis/aspose.gis.geometries/geometry/methods/roundz)

将 Z 坐标舍入到指定的小数位数。

#### [SetEmpty](https://apireference.aspose.com/net/gis/aspose.gis.geometries/geometry/methods/setempty)

清空几何体。

总之，这些方法允许你分析几何的拓扑关系，做“切饼”，比较它们的面积等等。

目前就这些，敬请关注！

如需更多示例，请访问[网站。GIS GitHub](https://github.com/aspose-gis) 页面。Aspose.GIS 上还有 [Twitter](https://twitter.com/AsposeGis) 和[脸书](https://www.facebook.com/AsposeGis/)的新闻页面