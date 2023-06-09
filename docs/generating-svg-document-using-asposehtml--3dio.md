# 使用 Aspose.HTML 生成 SVG 文档

> 原文：<https://dev.to/andruhovski/generating-svg-document-using-asposehtml--3dio>

SVG -可缩放矢量图形(Scalable Vector Graphics)是一种面向 web 开发者的基于矢量的格式。由于 SVG 是一种矢量格式，它的图像可以以高分辨率显示而不会有质量损失，因此这种格式非常流行。

万维网联盟在 1999 年首次推动了这种语言标记的发展。W3C 给出了 SVG 语言标记的概念，用于创建二维图形界面和图像，作为 HTML 规范的一部分。

# 简介

Aspose.HTML 支持解析 HTML5 和 SVG 文档。它还支持构建基于 W3C 文档对象模型的文档。在前一篇文章中，我们考虑了如何生成 HTML 文档。现在我们将讨论 SVG 图像的创建。

和 HTML 文档一样，我们有一个表示 SVG 图像根的类。这个类名为 SVGDocument，它具有符合 W3C 标准中 SVG 元素描述的方法和属性。另外，`SVGDocument`有方法`Save`和`RenderTo`。

## 由 SVG 创建的基本形状

我们可以使用下面的 SVG 元素来创建形状。

*   线；
*   圆形；
*   Rect (rectangle);
*   椭圆；
*   多边形；
*   折线(多线形状)；
*   路径；
*   文本；

# 创建空的 SVG 文档

下面的代码片段展示了如何创建一个空的 SVG 文档:

```
public static void GenerateSvgDemo(string widthStr, string heightStr, string bkColorStr)
{
    //In this method, we demonstrate several techniques for setting values of width and height
    var width = float.Parse(widthStr);
    var height = float.Parse(heightStr); ;

    //Creat new (empty) image 
    var svgDocument = new SVGDocument(new Configuration());

    //Adjust image size
    svgDocument.RootElement.Width.BaseVal.ValueAsString = widthStr;
    svgDocument.RootElement.Height.BaseVal.ValueAsString = heightStr;

    svgDocument.RootElement.ViewBox.BaseVal.X = 0;
    svgDocument.RootElement.ViewBox.BaseVal.Y = 0;
    svgDocument.RootElement.ViewBox.BaseVal.Width = width;
    svgDocument.RootElement.ViewBox.BaseVal.Height = height;
} 
```

Enter fullscreen mode Exit fullscreen mode

# 绘制 SVG 图像

## SVGLine 元素

line 元素绘制直线。您可以通过定义起始和结束 X 和 Y 坐标来创建线定义。下面给出了在 SVG 中画线的代码:

```
//Line 
if (!(svgDocument.CreateElementNS(SvgXmlns, "line") is SVGLineElement line))
    throw new NullReferenceException("Line Element");

line.X1.BaseVal.ValueAsString = "25";
line.Y1.BaseVal.ValueAsString = "25";
line.X2.BaseVal.ValueAsString = "200";
line.Y2.BaseVal.ValueAsString = "200";
line.Style.CSSText = "stroke:green;stroke-width:3"; 
```

Enter fullscreen mode Exit fullscreen mode

在前面的示例中，属性 X1 和 Y1 是字符串的起始坐标，属性 X2 和 Y2 是直线的尾部属性。您也可以通过简单地更改线的坐标来更改线的方向。

## SVG circle 元素

此元素用于定义圆。要定义圆，您需要定义一个 Cx，Cy 属性，它将是圆的中心，并且您需要定义圆的半径 R。下面给出了在 SVG 中画圆的代码:

```
//Circle

if (!(svgDocument.CreateElementNS(SvgXmlns, "circle") is SVGCircleElement circle))

    throw new NullReferenceException("Circle Element");

圆形。CX . base val . valueasstring = " 120 "；
圆。cy . base val . valueasstring = " 120 "；
圆。R . BaseVal.ValueAsString = " 50
圈子。style . CSS text = " stroke:black；笔画宽度:2 "；
 //设置样式属性的另一种方式
圆。Style.SetProperty("fill "，" red "，string。空)；

svgDocument。root element . AppendChild(circle)； 
```

Enter fullscreen mode Exit fullscreen mode

## 
  
SVGRect 元素

`SVGRectelement`绘制一个矩形。创建矩形就像定义宽度和高度一样简单。下面给出了绘制矩形的代码:

```
// Rectangle

if (!(svgDocument.CreateElementNS(SvgXmlns, "rect") is SVGRectElement rect))

    throw new NullReferenceException("Rect Element");

rect.X.BaseVal.Value = 250;

rect.Y.BaseVal.Value = 250;

rect.Width.BaseVal.Value = width/4;

rect.Height.BaseVal.Value = height/4;

rect.Style.SetProperty("fill", "#" + bkColorStr, string.Empty);

svgDocument。root element . AppendChild(rect)； 
```

Enter fullscreen mode Exit fullscreen mode

## 
  
SVGEllipse 元素

元素用于在 SVG 中绘制一个椭圆。绘制椭圆类似于绘制圆，但是要定义椭圆，需要定义两个半径。下面给出了在 SVG 中绘制椭圆的代码:

```
// Ellipse

if (!(svgDocument.CreateElementNS(SvgXmlns, "ellipse") is SVGEllipseElement ellipse))

    throw new NullReferenceException("Rect Element");

ellipse.Cx.BaseVal.Value = 100;

ellipse.Cy.BaseVal.Value = 250;

ellipse.Rx.BaseVal.Value = 20;

ellipse.Ry.BaseVal.Value = 30;

ellipse.Style.SetProperty("fill", "#112233", string.Empty);

svgDocument。RootElement.AppendChild(椭圆)； 
```

Enter fullscreen mode Exit fullscreen mode

## 
  
SVG polygon 元素

多边形是二维形状。它们由直线组成，形状是“封闭的”(所有的线都连接在一起)。

```
// Polygon

if (!(svgDocument.CreateElementNS(SvgXmlns, "polygon") is SVGPolygonElement polygon))

    throw new NullReferenceException("Polygon Element");

polygon.SetAttribute("points", "210,10 10,260 300,260");

polygon.Style.SetProperty("fill", "#440066", string.Empty);

svgDocument。RootElement.AppendChild(多边形)； 
```

Enter fullscreen mode Exit fullscreen mode

## 
  
【SVGPolygonElement】

多段线是由多条线定义组成的图形。这个例子与上一个很相似:

```
// Polyline

if (!(svgDocument.CreateElementNS(SvgXmlns, "polyline") is SVGPolylineElement polyline))

    throw new NullReferenceException("Polyline Element");

polyline.SetAttribute("points", "0,50 50,50 50,100 100,100 100,150 150,150 150,200");

polyline.Style.CSSText = "fill:white;stroke:black;stroke-width:2";

svgDocument。RootElement.AppendChild(多段线)； 
```

Enter fullscreen mode Exit fullscreen mode

## 
  
【SVGPathElement】

要绘制所有元素和所有其他复杂元素，可以使用 path 元素。使用 path 元素，您可以创建一个任意的绘图。

```
// Path

if (!(svgDocument.CreateElementNS(SvgXmlns, "path") is SVGPathElement path))

    throw new NullReferenceException("Path Element");

path.SetAttribute("d", "M 100 350 q 150 -300 300 0");

path.Style.CSSText = "stroke:black;stroke-width:2";

svgDocument。RootElement.AppendChild(路径)； 
```

Enter fullscreen mode Exit fullscreen mode

## 
  
绘图文字

最后一个例子展示了如何向 SVG 图像添加文本:

```
if (!(svgDocument.CreateElementNS(SvgXmlns, "text") is SVGTextElement text))

    throw new NullReferenceException("Path Element");

文字。SetAttributeNS(null，“x”，(宽度/ 2)。ToString())；
正文。SetAttributeNS(null，“y”，(height / 2)。ToString())；
正文。text content =＄" { width str } x { heightStr } "；

文字。Style.SetProperty("font-family "，" Arial "，string。空)；
正文。Style.SetProperty("font-weight "，" bold "，string。空)；
正文。style . set property(" dominant-baseline "，" central "，string。空)；

var fontSize = Math。圆(数学。Max(12，数学。最小值(. 75 *数学。最小(宽度，高度)，0.75 *数学。Max(宽度，高度)/12)))；
正文。Style.SetProperty("font-size "，$"{fontSize}px "，字符串。空)；
正文。Style.SetProperty("fill "，" purple "，string。空)；
文字。Style.SetProperty("文本锚"，"中间"，字符串。空)；

svgDocument。root element . AppendChild(text)； 
```

Enter fullscreen mode Exit fullscreen mode

# 
 [T3】
结论](#conclusion) 

这就是 SVG 的所有基础知识，在下面的文章中将会考虑使用矢量图形的更复杂的例子，但是现在作为一个简单的实例，我邀请您考虑一下占位符生成器[。](https://github.com/aspose-html/Aspose.HTML-for-.NET/tree/master/Showcases/SimplePlaceHolder)