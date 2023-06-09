# 控制 Aspose.CAD 中的栅格化。

> 原文：<https://dev.to/nnevod/control-over-rasterization-in-asposecad-1jn2>

[Aspose。CAD](https://products.aspose.com/cad) 允许对 CAD 文件栅格化为光栅图像和 PDF 文件进行一些控制。让我们看看他们能做些什么。

栅格化本身，本质上是由 [CadRasterizationOptions](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/vectorrasterizationoptions) 类的一个实例控制的，这个类被赋值给 [ImageOptionsBase](https://apireference.aspose.com/net/cad/aspose.cad/imageoptionsbase) 的特定输出格式后代实例的[VectorRasterizationOptions](https://apireference.aspose.com/net/cad/aspose.cad/imageoptionsbase/properties/vectorrasterizationoptions)属性，即 [PdfOptions](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/pdfoptions) 、 [PngOptions](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/pngoptions) 等。因此，当您将 output options 实例传递给 [Image](https://apireference.aspose.com/net/cad/aspose.cad/image) 的 [Save](https://apireference.aspose.com/net/cad/aspose.cad.image/save/methods/3) 方法时，是 CadRasterizationOptions 属性指定了将要渲染的内容，而输出选项本身只设置了包含渲染图像的文件。这里我们将重点关注 CadRasterizationOptions，而不是特定于格式的选项。

### [页面高度](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/vectorrasterizationoptions/properties/pageheight)和[页面宽度](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/vectorrasterizationoptions/properties/pagewidth)属性:

显然，设置输出图像的高度和宽度。在光栅图像的情况下，这些以像素为单位定义图像的尺寸，在 PDF 的情况下，由于这些是矢量图像，所以它们大多是相对的，仅用于定义输出图像的纵横比，除非指定了 [UnitType](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/vectorrasterizationoptions/properties/unittype) ，否则它们将定义 PDF 页面的实际物理尺寸，并且内容将被缩放以适应它。

### [页面大小](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/vectorrasterizationoptions/properties/pagesize):

本质上是一样的，只是接受了一次定义高度和宽度的 SizeF。

### [边境](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/vectorrasterizationoptions/properties/borderx)和[边境](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/vectorrasterizationoptions/properties/bordery):

这些定义了实际图像区域周围的边距，即最终图像的尺寸为页面高度+边框*2，页面宽度+边框*2。

### [插画](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/vectorrasterizationoptions/properties/centerdrawing):

如果设置为 true，将确定显示对象的几何中心，并将其固定到图像的中心。导出 3D 实体时，它将自动被视为已启用。

### [背景色](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/vectorrasterizationoptions/properties/backgroundcolor):

显然，设置背景颜色，因为 CAD 系统通常暗示没有背景，但你确实需要一个光栅图像。默认为白色。

### [DrawColor](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/vectorrasterizationoptions/properties/drawcolor) :

您可以覆盖 CAD 文档中定义的实体颜色，并通过将此属性设置为您想要的颜色并将 [DrawType 属性设置为 UseDrawColor 来替换您自己的颜色。默认情况下，DrawColor 是黑色的。](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/cadrasterizationoptions/properties/drawtype)

### [DrawType](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/cadrasterizationoptions/properties/drawtype) :

指示是使用文档中指定的颜色还是使用 DrawColor 来呈现实体。默认值为 UseDrawColor。

### [单位类型](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/vectorrasterizationoptions/properties/unittype):

指定输出图像的尺寸单位。如果您要导出到 PDF 并指定了输出图像尺寸，则 PDF 页面将具有以指定单位表示的指定页面高度和页面宽度的尺寸。如果您要导出到具有指定单位类型但没有指定尺寸的 PDF，则 PDF 页面将具有与 CAD 文档内容相对应的尺寸，采用指定的单位。例如，如果文档包含一个 100 英寸的杆，其尺寸以英寸为单位，而您将厘米指定为单位类型，则输出 PDF 将包含一个 254 厘米的杆，其尺寸以厘米为单位。

对于光栅图像，只有在未指定输出尺寸时，UnitType 才起作用。那么一个像素代表 1×1 的单位正方形。因此，如果文档中有一个 100 英寸宽的要素，并且将单位类型设置为厘米并在不指定尺寸的情况下导出到栅格，则该要素的宽度将为 254 像素。如果 CAD 图像没有单位，图像的单位将被视为英寸。

### [内容位图](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/vectorrasterizationoptions/properties/contentasbitmap):

仅在导出为 PDF 时适用，如果设置为 true，渲染图像将栅格化并作为光栅图像嵌入 PDF，而不是常规的矢量图像。默认值为 false。

### [【显卡选项】](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/vectorrasterizationoptions/properties/graphicsoptions):

允许为光栅图像(包括嵌入在 PDF 中的图像)设置抗锯齿级别、插值质量和文本渲染模式

### [变焦](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/cadrasterizationoptions/properties/zoom):

指定边距的另一种方法。值 1 对应于精确拟合，值 1 以下允许创建边距，值 1 以上允许按比例绘制。

### [PenOptions](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/cadrasterizationoptions/properties/penoptions) :

允许为没有定义笔选项的实体设置笔选项。目前只允许设置开始和结束线帽。

### [观察点](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/cadrasterizationoptions/properties/observerpoint):

只对 IFC 图像有影响，因为这些图像没有定义观察点。默认情况下，观察点沿着 Z 轴“向下”看。

### [t1](#renderresult)[渲染结果](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/cadrasterizationoptions/fields/renderresult):

渲染完成时将接收 CadRenderResult 的处理程序。允许查看渲染过程中发生的错误列表。

### [t1](#typeofentities)[型态练习](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/cadrasterizationoptions/properties/typeofentities):

指定要为 AutoCAD 文件渲染的 2D 或三维实体。大多数实体以两种方式呈现，有些仅是 2D，有些仅是 3D，更详细的解释在[用 Aspose 澄清 3D 实体的导出中。CAD](https://dev.to/nnevod/clarifying-export-of-3d-entities-with-asposecad-1ppd) 文章。

### [层层](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/cadrasterizationoptions/properties/layers):

获取或设置要为 AutoCAD 文档导出的图层。

### [布局](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/cadrasterizationoptions/properties/layouts):

获取或设置要为 AutoCAD 文档导出的布局名称。如果为空，将导出模型布局。

### [pdf product location](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/cadrasterizationoptions/properties/pdfproductlocation):

AutoCAD 文件可能具有参考 PDF 文件的参考底图。阿斯波斯。CAD 不能自己渲染 PDF，因此该选项用于指定可以读取 PDF 文件的 Aspose.PDF DLL 文件的位置。许可证文件应该在同一文件夹中。

### [自动伸缩](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/cadrasterizationoptions/properties/automaticlayoutsscaling):

将 CAD 文档内容缩放至输出图像大小如果设置为 true，则默认值为 true。

### [无标度](https://apireference.aspose.com/net/cad/aspose.cad.imageoptions/cadrasterizationoptions/properties/noscaling):

定义是否缩放 CAD 内容以匹配输出图像。这将被 AutomaticLayoutScaling 覆盖——如果设置为 true，则无缩放将不起作用。

对于大多数文件格式，当 AutomaticLayoutScaling 为 false 而 NoScaling 为 true 时，根本不会进行缩放，即，如果文件内容符合文件中定义的 5 个相对单位，则这些内容将被栅格化为 5 个像素，因此如果导出为栅格，您将无法辨别任何内容。如果您导出为 PDF，相应地，您必须放大才能看到细节。

IFC 和 AutoCAD 文件是例外，前者总是缩放以适合，后者在这种情况下视口将缩放并居中于图像的中心，而不是实际的实体。因此，如果您正在导出 AutoCAD 文件，其中实体占据视口的一小部分，并且 ScaleMethod 设置为 none，则实体将占据输出图像的相应一小部分，相反，如果使用缩放，这些实体将占据整个输出图像。

目前就这些，敬请关注！

如需更多示例，请访问[网站。CAD GitHub](https://github.com/aspose-cad) 页面。Aspose.CAD 上还有 [Twitter](https://twitter.com/Asposecad) 和[脸书](https://www.facebook.com/AsposeCAD)的新闻页面