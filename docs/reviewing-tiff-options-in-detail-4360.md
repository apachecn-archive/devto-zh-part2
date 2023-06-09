# 详细查看 TIFF 选项

> 原文：<https://dev.to/nnevod/reviewing-tiff-options-in-detail-4360>

[Aspose。成像](https://products.aspose.com/imaging/)在保存或创建图像时，使用基于[图像选项的](https://apireference.aspose.com/net/imaging/aspose.imaging/imageoptionsbase)对象来设置文件格式。同样，TIFF 格式是通过 [TiffOptions](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/) 类的实例配置的。正如我在[上一篇](https://dev.to/nnevod/clarifying-working-with-tiff-in-asposeimaging-4fgf)文章中提到的，TiffOptions 也可以从加载的图像中读取，所以你可以得到关于特定文件格式设置的信息。TiffOptions 的大多数属性适用于单个的 [TiffFrames](https://apireference.aspose.com/net/imaging/aspose.imaging.fileformats.tiff/tiffframe) ，只有少数属性与作为 TiffFrames 集合的 [TiffImage](https://apireference.aspose.com/net/imaging/aspose.imaging.fileformats.tiff/tiffimage) 相关。考虑到 TIFF 的多图像性质，我将整个 TIFF 称为“文件”,表示为 TiffImage，将其中的单个图像称为“图像”或“页面”,表示为 TiffFrame。

TiffOptions 提供了根据需要设置 TIFF 文件格式的工具。大多数经常使用的选项都是显式支持的，并且可以作为属性使用。如果需要，应用于它们的改变将导致图像数据的相应处理。TiffOptions 还提供了一种直接处理 TIFF 标签的方法，因此 Aspose 不会显式支持任何内容。成像也可以处理。

这里我将首先描述 TiffOptions 的属性，之后我将描述 TiffOptions 的特定于 TIFF 的方法，它们与直接访问标签数据有关。

## 属性

下面是对特定于 TiffOptions 的显式可用属性的描述，大致按照它们的作用域进行分组

### 元数据

#### [艺人](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/artist)

创建图像的人。

#### [版权](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/copyright)

版权声明。

#### [DateTime](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/datetime)

图像创建的日期和时间。

#### [DocumentName](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/documentname)

扫描此图像的文档的名称。

#### [exiffd](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/exififd)

指向 TIFF 文件的 EXIF·IFD 的指针。

#### [文件标准](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/filestandard)

TIFF 6.0 基线或 TIFF 6.0 扩展。

#### [图像描述](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/imagedescription)

图像的标题——注释之类的。不允许 2 字节字符。

#### [墨名](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/inknames)

此图像中使用的油墨名称。油墨是单独光度模型的颜色。

#### [页面名称](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/pagename)

扫描此图像的页面的名称。

#### [t1](#pagenumber)[页号](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/pagenumber)

扫描此图像的页码。

#### [扫描仪制造商](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/scannermanufacturer)

用于扫描此图像的扫描仪制造商的名称。

#### [ScannerModel](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/scannermodel)

用于扫描此图像的扫描仪型号。

#### [t1](#softwaretype)[软件类型](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/softwaretype)

创建此映像的软件的名称和版本(如果由硬件创建，则为固件)。

#### [t1](#targetprinter)[目标印表机](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/targetprinter)

假设打印环境的描述。

#### [【xmpdata】](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/xmpdata)

XMP 格式的元数据，表示为  (Aspose。与 XMP 元数据一起工作的成像 API 对象)

### 尺寸和定位数据

#### [方位](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/orientation)

图像的旋转和镜像。

#### [分辨率单位](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/resolutionunit)

图像的分辨率单位-英寸、厘米等。

#### [曝光](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/xposition)

图像左侧相对于其所在页面左侧的偏移量。

#### [x 分辨率](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/xresolution)

沿水平轴每分辨率单位的像素分辨率。

#### [Yposition](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/yposition)

图像上侧相对于其所在页面上侧的偏移量。TIFF 的 Y 轴是向下的，所以这是正值。

#### [y 分辨率](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/yresolution)

沿垂直轴以像素/分辨率单位表示的分辨率。

### 图像格式

#### [AlphaStorage](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/alphastorage)

定义 alpha 的存储方式——是否预乘。当每个像素超过 3 个样本时使用。

#### [t1](#bitsperpixel)[位像素](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/bitsperpixel)

构成单个像素数据的位数，根据 BitsPerSample 和 SamplesPerPixel 计算。

#### [比特样本](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/bitspersample)

每个颜色分量的位数。这是一个数组，因为每个组件可以有不同的位数。设置后，SamplesPerPixel 将被设置为数组的长度。

#### [t1](#byteorder)[字节顺序](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/byteorder)

这是针对每个文件而非每个映像的设置，描述了文件的字节顺序。

#### [压缩](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/compression)

用于图像栅格数据的压缩方法。

#### [faxt 4 选项](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/faxt4options)

第 3 组传真压缩选项(T4) - 1D 压缩、2D 压缩、无压缩或填充位到字节边界(扩展数据大小)。

#### [FillOrder](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/fillorder)

字节中位的顺序

#### [isextrasamplere](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/isextrasamplespresent)

如果 SamplesPerPixel 中的样本数多于光度设置所需的样本数，则为 True。

#### [IsTiled](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/istiled)

对于平铺图像为真。较大的 TIFF 图像通常被分割成图块，当只需一次显示/处理图像的一部分时，这可以减少内存的使用。

#### [光度](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/photometric)

图像数据的颜色空间。Aspose.Imaging 支持最小值为白色的 BW、最小值为黑色的 BW、RGB、调色板颜色、透明遮罩(在 TIFF v6.0 中已过时)、分色(通常用于 CMYK)、YCbCr、Cielab、Icclab、Itulab、Logl 和 Logluv

#### [平面配置](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/planarconfiguration)

描述颜色是存储在单独的平面上还是交错存储。

#### [预测器](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/predictor)

LZW 压缩的预测值——无或水平差异。

#### [样本格式](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/sampleformat)

样本的数字格式-无符号整数、有符号整数、浮点、无类型、复数整数、复数浮点。每种成分分开。

#### [t1](#samplesperpixel)[采样像素](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/samplesperpixel)

每个像素的颜色分量数。

#### [子文件类型](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/subfiletype)

存储在子文件中的数据类型。或者只是一个图像、文件中另一个图像的缩略图、页面、透明遮罩或 MRC 图像。

#### [ycbcr 系数](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/ycbcrcoefficients)

RGB 数据到 YCbCR 的转换系数。

#### [YCbCrSusbsampling](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/ycbcrsubsampling)

用于 YCbCr 图像数据的子采样。

### 

它与图像格式密切相关，因为它也与光栅数据在文件中的位置有关。

#### [图像长度](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/imagelength)

TIFF 标签中规定的图像高度，以像素为单位。

#### [图像宽度](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/imagewidth)

TIFF 标签中规定的图像宽度，以像素为单位。

TIFF 图像可以组织成条，以便更快的 IO 和更容易的随机访问。这个标签描述了单条图像的行数。每个图像的条带总数可以计算如下:stripesperimage = floor((ImageLength+RowsPerStrip-1)/RowsPerStrip)。

#### [条纹偏移](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/stripoffsets)

组成图像的条带相对于文件开头的偏移量。这用于定位文件中的实际图像数据，除非使用平铺，否则使用平铺偏移。建议数组长度保持在 64k 字节以下，每个条带长度也保持在 64k 字节以下。

#### [条带字节数](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/stripbytecounts)

压缩后条带中的字节数。

#### [瓦片宽度](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/tilewidth)

以像素为单位的平铺宽度(平铺中的列数)。
水平、垂直和总瓦片数可以计算如下:
tiles across =(image width+tile width-1)/tile width
tiles down =(image length+tile length-1)/tile length
tiles perimage = tiles across * tiles down。
必须是 16 的倍数。可能大于 ImageWidth，但这意味着切片太大或图像太小，因此切片不是最佳选择

#### [平铺长度](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/tilelength)

以像素为单位的平铺长度(高度)。这是每个图块中的行数。必须是 16 的倍数。

#### [TileOffsets](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/tileoffsets)

文件中图块相对于文件开头的偏移量。平铺是从左到右和从上到下排序的。如果使用平面配置，第一个偏移系列指向第一个组件平面的图块，然后第二个系列指向第二个平面，依此类推，因此偏移总数等于图块总数乘以每个像素的样本数。

#### [TileByteCounts](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/tilebytecounts)

压缩后图块中的字节数。

### 颜色仿形和处理

#### [色彩图](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/colormap)

存储在文件中的调色板图像。属性是已经加载并处理过的调色板。长度必须符合公式:3*(2^BitsPerSample).

#### [IccProfile](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/iccprofile)

嵌入图像中的颜色配置文件流。

#### [半色调提示](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/halftonehints)

控制半色调功能的值。

#### [MaxSampleValue](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/maxsamplevalue)

图像中每个通道允许的最大样本值。16 位版本。

#### [MinSampleValue](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/minsamplevalue)

图像中每个通道允许的最小样本值。16 位版本。

组件是否必须与颜色配置文件数据相乘。

#### [SmaxSampleValue](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/smaxsamplevalue)

图像中每个通道允许的最大样本值。32 位版本。

#### [SminSampleValue](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/sminsamplevalue)

图像中每个通道允许的最小样本值。32 位版本。

#### [阈值化](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/threshholding)

描述用于将灰度图像转换为 BW 的方法。

### 标签数据

#### [IsValid](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/isvalid)

如果此 TiffOptions 实例的格式有效，则为 True，因为某些选项可能会冲突。使用 [Validate()](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/methods/validate) 方法查看错误。

#### [标签](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/tags)

“原始”形式的实际 TIFF 标签。

#### [t1](#validtagcount)[验证标签计数](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/validtagcount)

图像中有效 TIFF 标签的计数，即可以保存的标签。

### 整个文件相关的属性

#### [t1](#byteorder)[字节顺序](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/byteorder)

这里重复一下，因为它实际上与 TiffImage 相关——这是为整个文件设置的唯一格式属性，而不是在 pages 的标签中设置的。

#### [总页数](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/properties/totalpages)

只有文档中的页数是只读的，因为当您添加或删除页面时它会改变。

## 方法

下面是对特定于 TiffOptions 的方法的描述，主要是使用“原始”标签。标签存储为 [TiffDataType](https://apireference.aspose.com/net/imaging/aspose.imaging.fileformats.tiff/tiffdatatype) 类的后代的实例，它们提供对标签内的标签 ID、类型和值的访问。已知标签 ID 列表可用作 [TiffTags](https://apireference.aspose.com/net/imaging/aspose.imaging.fileformats.tiff.enums/tifftags) enum。

#### [t1](#addtag)[标签](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/methods/addtag)

给图像添加新标签。

#### [添加标签](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/methods/addtags)

将标记数组添加到图像中。

#### [t1](#gettagbytype)[贫民区类型](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/methods/gettagbytype)

通过标记的 ID 返回标记的实例，如果没有这样的标记，则返回 null。

#### [t1](#getvalidtagcount)[验证标签计数](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/methods/getvalidtagscount)

返回所提供的标记数组中有效标记的计数。

#### [【istaragpr】](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/methods/istagpresent)

检查图像是否包含具有指定标签 ID 的标签。

#### [移除标签](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/methods/removetag)

按标签 ID 删除标签。

#### [验证](https://apireference.aspose.com/net/imaging/aspose.imaging.imageoptions/tiffoptions/methods/validate)

检查此 TiffOptions 中的标记组合是否有效。

目前就这些，敬请关注！

如需更多示例，请访问[网站。成像 GitHub](https://github.com/aspose-imaging) 页面。Aspose.Imaging 上还有 [Twitter](https://twitter.com/Asposeimaging) 和[脸书](https://www.facebook.com/AsposeImaging-1702883649750052/)的新闻页面