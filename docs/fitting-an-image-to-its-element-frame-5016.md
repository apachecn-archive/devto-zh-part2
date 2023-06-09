# 使图像适合其元素框架

> 原文：<https://dev.to/mortoray/fitting-an-image-to-its-element-frame-5016>

图像需要各种尺寸模式来适应[响应布局](https://mortoray.com/2018/03/26/what-is-responsive-layout/)。在这里，我们将看看元素和视觉之间的关系和基本的调整选项。我还将查看一些额外的有用特性，这些特性我以前都没有实现过。

## 元素和视觉内容

当考虑[框模型](https://mortoray.com/2018/03/29/what-is-a-box-model-ui-element-layout/)时，经常会将上下文框与可视内容本身混淆。如果元素折叠到可视内容的大小，则大小不会有差异。但是，如果元素正在扩展以填充空间，视觉效果可能不会填充可用区域。

在我们进入图像之前，让我们先用一个圆形元素来考虑这个概念。圆永远是圆，而不是椭圆。

[![A circle fitted to the element size](img/258d30788b9780287cdb125969fdcfea.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--W1xk28G1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://mortoray.files.wordpress.com/2018/04/screen-shot-2018-04-11-at-09-34-39.png)

圆形元素可以比可视元素大；圆在可用元素区域内居中，扩展到宽度或高度。

图像元素必须处理相同的情况:元素大小不一定与图像大小相同。确定视觉效果大小和位置的正确方法取决于设计；我们必须提供属性来指定发生了什么。在 Fuse 中，我称之为`StretchMode`；在 CSS 中，这是`background-size`或`object-fit`属性。

## 像素和点精确

也许最明显的尺寸模式就是使用图像的自然尺寸；不管元素大小如何，我们将按原样绘制图像，或者裁剪边缘，或者留出空白。

[![A 320x212 image clipped into a 250x300 element](img/07ba7f1b9de989220eb92c906815cf7e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--j8HqtrKb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://mortoray.files.wordpress.com/2018/04/screen-shot-2018-04-11-at-09-43-44.png)

这种大小调整模式最常用于布局中，在这种布局中，元素的大小与图像的大小相同。例如，您可以在标题栏中使用图标，并让它们定义其大小。

我们需要解决我们所说的“自然大小”的问题。我们可以使用像素或者点。如果您希望图像在显示器上清晰，并且不需要调整大小，则需要像素精确调整大小模式。当您希望图像定义元素的大小并在设备之间保持一致时，可以使用点精确模式。

> 像素精确模式很难在移动设备上使用，因为这些设备的点对点密度差异很大。如果需要，它必须与多密度图像结合使用:根据目标密度使用不同源文件的图像元素。在 Fuse 中，我添加了一个`PointPrefer`模式，以获得两个世界的最佳效果:如果其中一个像素大小足够接近点大小，它将使用像素精确模式；否则，它会将最接近的图像拉伸到所需的磅值。

## 填充和缩放 9

图像通常用于填充某个区域，提供背景、边框或分隔线。这需要拉伸图像，填充整个元素区域，即使图像本身是扭曲的。

[![A stretched image, with aspect distortion.](img/26adaa7451a23d6319bd5d1edaa1dcc4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--A7XJYt8g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://wordpress.com/5b22caa8-ba86-45f0-8ea6-e4e5704d76f7)

> 上面的照片有助于显示失真，但填充功能可能不适合这样的图像。

拉伸的一个有用的特性是 9 网格拉伸:图像被分成 9 个区域，并自行拉伸。这允许为动态调整大小的元素创建边框。CSS 用`border-image`提供了一个相当健壮的特性。

[![A 9-grid image, showing preserved border/corner sizes, but stretched centres.](img/6aa898c013ada7b666b3ba4ac2123c4f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7OABlSb2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://mortoray.files.wordpress.com/2018/04/screen-shot-2018-04-11-at-10-01-13.png)

## 整齐划一

如果整个图像必须适合元素，并且必须保持纵横比，我们使用`Uniform`模式(在 CSS 中称为`contain`)。这将放大图像的大小，使其接触到可用区域的两侧，而将元素的其余部分留空。

[![Preserve ratio, full image inside element.](img/efce83fd5f190dfc7bfee44a5f9029a7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--by7n3Cni--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://mortoray.files.wordpress.com/2018/04/screen-shot-2018-04-11-at-09-46-141.png)

相反，如果你希望填充整个元素并保持长宽比，我们使用`UniformToFill`模式(在 CSS 中称为`cover`)。这将缩放图像，直到它到达元素的所有边缘，剪切元素之外的那些部分。

[![Stretch to fill preserving aspect, clipping image](img/84bc4203a50a6f51530b10407a7db2fd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aaw0wk8---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://mortoray.files.wordpress.com/2018/04/screen-shot-2018-04-11-at-10-08-39.png)

## 内容对齐

除了`Fill`或`Scale9`模式，图像并不完全适合可用空间。这就留下了一个问题:图像应该放在哪里？一个`Uniform`图像应该放在左边还是右边？一个`UniformToFill`图像应该显示图像的底部还是顶部？

将`ContentAlignment`添加到`Image`允许用户指定该选项。一个好的默认值是继承图像元素的对齐方式。如果你将图像右下方对齐，你会期望视觉效果也右下方对齐。

[![Uniform fill with Left and Right content alignment](img/6cae1359cc4dda9c5cd5084df2d7dfcd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xjESdciO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://mortoray.files.wordpress.com/2018/04/screen-shot-2018-04-11-at-10-10-28.png)

有一个选项我从来没有实施过，那就是锚定；基本对齐不能很好地涵盖设计的细微差别。锚点允许我们在照片上选择一个感兴趣的点，并确保它位于中心，例如侧面图像的面部。

扩展这个概念是一个锚定区域:我们在源图像中标记一个感兴趣的矩形，而不仅仅是一个点。这将开启一种`Uniform`模式的可能性，确保感兴趣的区域被完全显示，而图像的其余部分可以用来填充空白区域。

> 我怀疑大多数服务需要方形的个人资料照片，因为他们没有锚定可用。通过锚定，您可以接受任意大小的照片，让用户标记面部区域，或检测它，然后在响应式设计中使用整个图像。

## 选项

对于 Fuse，我们也有一个`StretchDirection`选项。它可以是`Both`(默认)、`UpOnly`或`DownOnly`中的一个。图像只能在允许的方向上拉伸。例如，如果我们有一个`Image {StretchMode=Uniform StretchDirection=DownOnly}`，视觉会缩小以适应一个元素，但永远不会扩大超过其自然大小。实际上，这种情况的使用案例有限。

可选的`Fill`标志提供了填充元素空白区域的另一种方法。使用拉伸模式定位元素后，将重复该操作以覆盖剩余的空间。这对于背景图像或用作画笔时最有意义。

除了锚定之外，`Offset`属性允许创建简单的动画。正常定位和调整视觉对象，然后对结果位置应用偏移量。这很可能与平铺图像上的`Fill`标志结合使用。

## 其他元素

> 如果你正在编写一个 UI 引擎，你可能会对如何做这些计算感兴趣。如果你喜欢这样的文章，请告诉我——这将是一篇数学占主导地位的文章，尽管并不复杂。在 Fuse 中，您可以查找封装了大部分逻辑的`SizingContainer`类——尽管布局计算代码也是其中的一部分。

关于这些大小调整模式，有趣的一点是它们同样适用于任何数量的元素类型，而不仅仅是图像。通用 CSS `object-fit`属性就是朝这个方向发展的。我甚至实现了一次缩放任意元素的`Viewbox`，它支持`StretchDirection`，但强制使用`Uniform`填充模式。

概念重复；对于一个新的 UI 引擎来说，将所有不同的用途合并成一组属性是一个很好的目标。即使某些组合对某些元素没有意义，拥有一组通用的属性也可以缩短学习曲线并确保丰富的特性集。

> 阅读更多关于[写 UI 引擎](https://mortoray.com/topics/writing-a-ui-engine/)的文章。[在 Twitter 上关注我](https://twitter.com/edaqa)，为了让我继续写作，考虑让[成为赞助人](https://www.patreon.com/mortoray)。