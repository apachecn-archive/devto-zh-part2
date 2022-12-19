# Photoshop 101，网页开发人员入门

> 原文：<https://dev.to/vinceumo/photoshop-101-introduction-for-web-developers-3hom>

# 简介

通常，当作为 web 开发人员工作时，我们需要集成模板。它们可以来自 Photoshop 或其他软件。在这篇文章中，我们将为一个网页开发者看一看 Photoshop 的基础知识。

这些内容最初是为伦敦 DAMDigital】的一个研讨会编写的。

Adobe Photoshop 是一个光栅图形编辑器软件，这意味着它是一个允许用户创建和编辑图像的程序。

它于 1988 年发布，从那以后一直在发展，成为图形软件的行业标准。

你可以在 Adobe 网站上下载 Photoshop 的免费试用版。

# 工作区

Photoshop 的工作空间是模块化的，你可以根据你所做的工作进行调整。Photoshop 中已经设置了一些默认工作空间。在本文中，我使用的是`Graphic and Web`这个。要换成这个，进入`Window/workspace/Graphic and Web`

让我们看看我们的工作空间

[![Layers box screenshot](../Images/bab3558c5fb6047d53418325f7b52686.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--soIWOg75--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s33.postimg.cc/azhnj48qn/photoshop.png)

*   菜单栏:这是你获得大多数 photoshop 选项的地方。
*   **B -选项栏**:该栏将给出当前所选工具的所有选项。
*   C - Toolbox :这个面板包含了 photoshop 中所有可用的工具。相关的工具被分组在一起，长时间点击其中一个就可以看到所有的工具。
*   **D 面板**:是你打开基本面板的区域，比如`Layers`、`History`等。要打开面板，只需进入`Window/(Panel that you want to open)`

# 创建新文件

在 Photoshop 中创建新文件时，你首先需要知道，我的设计将用于什么支持？意思是，它会被用作屏幕吗？(网络、电影和视频)还是印刷？

根据这个问题的答案，您将需要更改 PPI(每英寸像素)值。

> 每英寸像素(PPI)是用于定义计算机显示器分辨率的度量单位。此指标评估特定计算或输出显示设备能够显示的图片/图像质量。每英寸像素数也称为像素密度。 *[Techopedia](https://www.techopedia.com/definition/2741/pixels-per-inch-ppi)*

对于打印，您通常需要 300PPI，但这实际上取决于打印机和打印文档的大小。你也应该把你的颜色转换成 CMYK，但是这同样取决于你如何打印你的文档。如果你想了解更多关于 RGB 和 CMYK 颜色的知识，请查看这篇文章。

用于屏幕和网络😃，您将需要 72PPI 和 RGB 颜色。然后你需要指定你的屏幕尺寸。我建议首先为移动设备设计，然后是平板电脑和台式机😉。

作为 web 开发人员，您可能不需要创建新文件。你可能需要使用网页设计师提供的设计。

我们现在将查看一个现有的`.psd`模板，并使用这个模板来浏览 Photoshop。

你可以从[路易斯·科斯塔](http://lucaal.co/)下载并打开这个 [PSD 模板](https://shibbythemes.com/psd-freebies/surfersco-psd-template/)。

# 层

Photoshop 的核心功能之一就是**图层**。层就像一叠纸张，你可以透过透明区域或低透明度区域(部分透明)看到。

您可以在`Window/Layers`中打开图层面板。

[![Layers box screen shot](../Images/a916a8d3c14129863672406dbb75e0e0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QxERD1Fv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s33.postimg.cc/4yjym1een/layers.png)

在这个面板中，最上面的一层将覆盖下面所有的层。图层也可以在文件夹中进行组织。正确命名层和文件夹非常重要。这将有助于整合。

在每个文件夹和层的旁边，你有一个眼睛图标，它允许你切换这些的可见性。

你的 PSD 可能有很多层和文档。快速找到图层的一种方法是选择`move tool (v)`。右键单击画布，在那里你想找到你的层。你将得到你右击的区域中的所有层。通过点击一个，它会在你的图层面板中选择这个图层。

[![layer tree](../Images/434bc7c5d22da6f7b7207cde2cf4559f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1qvteZRn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s33.postimg.cc/85ei5n3zj/layers-tree.jpg)

# 工具箱

Photoshop 附带了很多工具。我要给你看几个有用的，对你有帮助的😉。

首先，如果你刚刚安装了 Photoshop CC 2018，你需要恢复所有的工具。

[![toolbox](../Images/d4fd984727258722a18a60af18836315.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--P4jgvNWZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://helpx.adobe.com/content/dam/help/en/photoshop/using/tools/_jcr_content/main-pars/image_0/v2_Tools%2520panel_PS_update1.png)

*[图片来自土坯](https://helpx.adobe.com/photoshop/using/tools.html)*

我们将看到一些最有用的工具，用于集成模板:

## A -选择工具

*   **移动**:允许用户在画布上移动一层。正如我们之前看到的，如果你右击你的画布，它也可以用来找到一个层。
*   **矩形选框**:该工具用于选择画布的一个区域，进行复制粘贴、填充等操作。它也可以用来测量。选择完成后，您可以在`Window/Info`中找到所选区域的大小。您可能需要在 photoshop `Edit/preferences/general/Unit & Rulers`中更改默认单位，然后将单位设置为像素。

[![select size](../Images/4ec254babb3891c4f40b1d2a33bce969.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--e0tfn1_d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s33.postimg.cc/60u54khsf/size.jpg)

## B 裁剪和切片工具

*   **裁剪**:该工具可以...裁剪图像🤟。在工具设置(选项栏)中，您可以设置想要保持的纵横比。

## C -测量工具

*   **滴管**:滴管可以让你在设计中快速得到一个颜色参考。只需点击你想要的颜色。然后在你的工具箱的底部，前景色会变成你选择的颜色。如果你点击前景色，它将打开`color picker window`,从这里你可以得到你的颜色值。
*   **颜色采样器**:当整合你的设计时，你可能需要选择多种颜色。我们将再次使用信息窗口`Window/Info`。这个工具允许我们创建一个颜色采样器。只需点击你想获取颜色的图像区域。你将在信息面板中得到每种颜色。您可以通过点击数字下方的滴管图标将颜色类型更改为 web。![color sampler](../Images/d41c29ea0bb6808d999dc7495724f941.png)
*   **尺子**:帮助你测量你的模板。所有信息将出现在你的信息窗口。测量时保持`Shift`,这样你的尺子就能保持笔直。你也可以得到角度。

## G -导航工具

*   **手**:这个工具可以帮助你在画布上四处走动，你也可以在任何时候通过按住空格键并用鼠标拖动来访问这个工具。
*   **缩放**:允许放大和缩小，可以`Ctrl` + `+`，也可以`Ctrl` + `-`。

# 导游

正如你可能已经注意到的，当打开我们的 PSD 文件时，我们的模板上有一些绿线。他们是向导，他们基本上是帮助你在画布上构建或测量事物的助手。

您可以使用`move tool` (v)移动现有的导轨

要创建新的参考线，您需要打开标尺。`View/Ruler`或`Ctrl` + `R`。标尺将出现在您的工作区中。然后，您可以从标尺中拖动画布，这是一个新的向导。

要移除导轨，使用`move tool` (v)并将导轨放回标尺中。

要隐藏和显示所有指南，您只需使用`Ctrl` + `H`，或者转到`View/Extras`。

# 智能物件

什么是智能对象？

> 智能对象是包含来自光栅或矢量图像(如 Photoshop 或 Illustrator 文件)的图像数据的图层。智能对象保留图像的源内容及其所有原始特征，使您能够对图层执行非破坏性编辑。 *[土坯](https://helpx.adobe.com/photoshop/using/create-smart-objects.html)*

当智能对象的缩略图中有以下图标时，可以在图层中识别智能对象:

[![smart object](../Images/d2e673cc96a4e1ad7107ebaa4f1b826f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fIGbnQ4D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s33.postimg.cc/z38f7ebrz/smart-object.png)

如果你处理矢量类型的图像(SVG，EPS，AI ),以及其他复杂的光栅文件，智能对象是非常方便的。

让我们尝试在 PSD 中导入一个智能对象。从 [flaticon](https://www.flaticon.com/free-icon/surfboard_930944#term=surf&page=1&position=10) 下载一个 SVG 文件。要在画布中导入我们的 SVG，只需将文件拖到画布中。我们现在可以在 illustrator 或其他矢量软件中通过双击缩略图或智能对象来编辑我们的 SVG。变化将出现在我们的 PSD。

智能对象能做的远不止这些，如果你想了解更多，看看 Photoshop 中关于智能对象你需要知道的 [10 件事。](https://design.tutsplus.com/tutorials/10-things-you-need-to-know-about-smart-objects-in-photoshop--cms-20268)

# 导出资产

首先，提醒一下，photoshop 是一款**光栅**软件。不是一个**矢量**软件。这意味着我们“不能”从 photoshop 中导出 SVG 文件。为此，您需要从 Illustrator 或 Inkscape 中导出这类文件。

在网络中，我们希望有光图像文件，对于摄影，我们会使用压缩文件。如果你需要使用透明(阿尔法通道)我们将使用`.png`文件。对于动画图像，我们将使用`.gif`。如果你需要一个矢量图像(例如图标)，最好是将你的文件导出为`.svg`。如果你想了解 Photoshop 中所有可用文件的更多信息，你可以在 Adobe 网站查看[“文件格式”。](https://helpx.adobe.com/photoshop/using/file-formats.html)

## 导出我们的画布

要导出画布，只需遵循以下步骤:

1.  转到`File/Export/Save for Web`
2.  选择文件格式
3.  选择图像大小
4.  挑选质量
5.  救援

## 仅从画布中导出一个资产

您可能需要导出模板的一些资产。

让我们导出产品传送带中的左箭头:

[![screenshot carousel](../Images/34bc00b441a81cd0ded340f19373b803.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0nwucDiq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s33.postimg.cc/5o2qydrsv/export1.jpg)

使用移动工具，我们将找到我们的层。右键点击箭头，选择图层`Arrow Left`。现在只需在图层面板中右键单击该图层。选择`export as`。只需选择您需要的文件类型。

我们也可以导出文件夹。

# 动作

Photoshop 里的一个动作是什么？

> 动作是对单个文件或一批文件回放的一系列任务，包括菜单命令、面板选项、工具动作等。例如，您可以创建一个动作来更改图像的大小，对图像应用效果，然后以所需的格式存储文件。 *[土坯](https://helpx.adobe.com/photoshop/using/actions-actions-panel.html)*

如果你想为网页调整一批图片的大小，这个特性真的很方便！

让我们创建一个新动作来裁剪图像并导出此图像:

1.  从 https://unsplash.com/下载一堆图片
2.  打开我们的一张图片
3.  打开`Actions`面板，`Window/Actions`，
4.  通过点击图标**创建一个新动作**(bin 图标左边的那个)，我们将这个动作命名为**导出为 web 客户端名称**
5.  我们现在正在记录我们的行动，记录按钮为红色，您可以通过单击停止图标(左侧的方形图标)停止记录
6.  选择裁剪图标，并将比例设置为 1×1，然后裁剪图像
7.  现在我们要导出我们的图像，`File/Export/Save for Web`，选择`JPG`，质量 50%，宽度 500 像素
8.  单击保存并选择您的目标文件夹
9.  关闭您的图像而不保存
10.  停止记录，点击停止图标(左边的方形图标)

现在我们有了我们的动作，我们可以打开一个图像，只需点击 play 按钮来“播放”我们的动作。

如果我们想将我们的操作应用于一批图像，只需遵循以下步骤:

1.  转到`File/Automate/Batch`
2.  选择`Source`文件夹。
3.  选择我们的行动
4.  点击`Ok`

瞧😉。您的所有图像都在导出文件夹中。

* * *

我希望你喜欢这个小小的面向网络开发者的 Photoshop 101 介绍😃。如果你想有一个 102 版本，让我知道你想知道或听到什么😉。

* * *

*   [Vincent-humau . com](http://vincent-humeau.com/)