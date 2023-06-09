# 反应材料的权威指南

> 原文：<https://dev.to/bnevilleoneill/the-definitive-guide-to-react-material-4969>

[![](img/efeb7b60e82b06e8fe869a077d293b93.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UOcFgeJM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AUB4RgGVPSwQWXZGBwViNyA.png)

[材料设计](https://material.io/)是谷歌在 2014 年首次推出的一种设计语言。它是一种视觉语言，利用基于网格的布局、响应动画和过渡、填充和深度效果，如照明和阴影。

材料设计的目标归结为三点:**创造**、**统一**和**定制。**

通过 **Create** ，材料设计旨在提供一种视觉语言，综合优秀设计的经典原则。通过 **Unify，**它旨在开发一个单一的底层系统，统一跨平台、设备和输入法的用户体验，通过 **Customize，**它为创新和品牌表达提供了一种视觉语言和灵活的基础。

在本文中，我们将探索如何创建具有材质设计主题的 React 应用程序。有各种各样的库对此有所帮助，但是对于本文，将使用 [material-ui](https://github.com/mui-org/material-ui) 库。

### 入门

[material-ui](https://material-ui.com/getting-started/installation/) 是一套实现 Google 材质设计的 React 组件(material-ui 最近[发布了](https://medium.com/material-ui/material-ui-v1-is-out-e73ce13463eb)一个 v1 的库。).这些组件独立工作，这意味着它们是自支持的，并且将只注入它们需要显示的样式。

要开始使用 material-ui，您需要做的就是运行下面的终端命令。

```
npm install @material-ui/core 
```

接下来，让我们看看与材料设计相关的一些 material-ui 的基础知识，并看看它是如何为后面的文章做准备的。

[![](img/2063f22e152ef36aa267c13ae9842601.png)T2】](https://logrocket.com/?cid=banner_a)

### **排版**

说到材质设计，字体选择默认为 [Roboto](https://fonts.google.com/specimen/Roboto) 。然而，material-ui 并没有附带 Roboto 故障。可以使用以下两种方法之一将字体导入 React 项目。

#### *来自 CDN*

```
<link rel="stylesheet" href="https://fonts.googleapis.com/css?family=Roboto:300,400,500"> 
```

#### *与 NPM 一起安装*

你可以通过在你的终端键入下面的命令把它作为一个 npm [包](https://github.com/KyleAMathews/typefaces/tree/master/packages/roboto)安装:

```
npm install typeface-roboto --save 
```

然后，您可以像这样将其导入到您的项目中:

```
import 'typeface-roboto' 
```

material-ui 还提供了一个名为[排版](https://material-ui.com/api/typography/#__next)的组件。排版组件基本上允许你在应用程序中显示文本。是这样写的:

```
<Typography variant="title" color="inherit">Some text in here</Typography> 
```

变体道具有助于应用主题排版样式，颜色是组件的颜色。它支持适合组件的主题颜色。

### **CSS 基线**

如果你一直在编写前端代码，你应该已经知道 normalize.css 是什么了，如果不是，它是 HTML 元素和属性样式规范化的集合。Material-UI 提供了自己的 normalize.css 版本，即 CssBaseline，该组件提供了一个优雅、一致和简单的基线来构建。

CSSBaseline 执行以下操作:

*   所有浏览器中的边距都被删除
*   将应用默认的材料设计背景色
*   启用字体抗锯齿可以更好地显示 Roboto 字体
*   上没有声明基本字体大小，但假定为 16px(浏览器默认)

### **网格**

Material Design 的响应式用户界面基于 12 列网格布局。这个网格在布局之间创造了视觉上的一致性。

网格系统具有以下特点:

*   它使用 Flexbox
*   布局有两种类型:*容器*和*项目*
*   项目宽度以百分比设置，因此它们总是相对于它们的父元素而变化和调整大小
*   项目具有填充以在各个项目之间创建间距。
*   有五个网格断点:xs、sm、md、lg 和 xl

### **图标**

图标是材料设计的一个重要组成部分，它们被用在按钮上来传达一个动作，传达信息。它被用来象征一个命令、文件、设备或目录。

图标也用于表示诸如回收站、打印和保存之类的操作，通常出现在应用程序栏、工具栏、按钮和列表中。

可以使用两种方法渲染 Material-UI 中的图标；Icon 用于呈现字体图标，SvgIcon 用于呈现 SVG 路径。

SvgIcon 组件将 SVG path 元素作为其子元素，并将其转换为显示路径的 React 组件，并允许设置图标样式和响应鼠标事件。这可以从下面的例子中看出:

[https://medium . com/media/d 56 A0 e 81 c 1c 79 b 07 aadd 81 c 9228 ce 6 e 5/href](https://medium.com/media/d56a0e81c1c79b07aadd81c9228ce6e5/href)

Material-UI 也有一个包，它附带了在 React 应用程序中使用的预设图标。这是一个完全不同的[包](https://www.npmjs.com/package/@material-ui/icons)，它包括转换成 SvgIcon 组件的官方材质图标。你可以用下面的终端命令安装:

```
npm install @material-ui/icons 
```

### **按钮**

按钮允许用户只需轻轻一点就可以采取行动和做出选择。它们有助于传达用户可以采取的行动。

因为 material-ui 组件是独立的，所以您需要导入 Button 组件。

```
import Button from '@material-ui/core/Button';

<Button _color_="primary">Hello World</Button> 
```

按钮组件可以采用许多道具，如颜色、变体、大小等。您可以在此查看完整列表。

material-ui 和 Material Design 中的按钮通常可以采用以下任何形式:

#### *扁平按钮*

平面按钮是只有文本的按钮。它们没有背景颜色和边框。它们可以用在对话框、工具栏或内嵌中。它们不提升，而是在印刷机上填充颜色。

```
<Button color="primary">Primary</Button> 
```

[https://medium . com/media/da 5 e 7d 467259640 D5 e ddcc 7d 3478 b 6 b/href](https://medium.com/media/da5e7d467259640d5e5ddcc7d3478b6b/href)

#### *概述按钮*

轮廓按钮是中等强调的纯文本按钮。它们的行为类似于平面按钮，但有一个轮廓，通常用于重要的操作，但不是应用程序中的主要操作。

它们的外观通常以悬停时的边框和背景色为特征。

```
<Button variant="outlined">Default</Button> 
```

[https://medium . com/media/4330 b 561d 2c 74 e5f 65162d 125467 E4 a5/href](https://medium.com/media/4330b561d2c74e5f65162d125467e4a5/href)

#### *凸起的按钮*

凸起的按钮是矩形按钮。它们在印刷机上提升和显示油墨反应。

```
<Button variant="raised" color="secondary">Secondary</Button> 
```

[https://medium . com/media/22bc 78370 f 40 D1 a3 f 05 fa 2477357 a88/href](https://medium.com/media/22bc78370f40df1a3f05fa2477357a88/href)

#### *浮动动作按钮*

浮动操作按钮(FAB)代表屏幕中的主要操作，如编写新电子邮件、添加新联系人或删除图像条目。

它的形状像一个圆形，它漂浮在所有其他视图的上方，背景颜色应用于焦点上，点击或单击时它就会升起。也可以设置为点击时显示更多动作或内容。

```
<Button variant="fab" color="primary" aria-label="add"><AddIcon /</Button> 
```

[https://medium . com/media/a 3d 288 e 88 c 7 BC 12b 6199483953 b62d 4d/href](https://medium.com/media/a3d288e88c7bc12b6199483953b62d4d/href)

### **材质-UI 组件**

material-ui 有很多 ui 组件，可以帮助构建一个材质设计主题的 React 应用。让我们来看看一些材质 ui 组件。每个组件都有一个例子，你可以点击它们各自的 CodeSandbox 链接来查看代码。

### **App 工具栏**

[应用栏](https://material.io/design/components/app-bars-top.html)，在 Android 中原名为动作栏，它的作用是一个标题导航菜单。这是一个用于品牌、导航、搜索和操作的工具栏。要使用 App bar，需要从 material-ui:
获得这两个组件

```
import AppBar from '@material-ui/core/AppBar';
import Toolbar from '@material-ui/core/Toolbar'; 
```

[https://medium . com/media/1685 ebea 545650 e9 B9 E8 b 9799364 b96a/href](https://medium.com/media/1685ebea545650e9b9e8b9799364b96a/href)

### **导航**

[底部导航菜单](https://material-ui.com/demos/bottom-navigation/)位于底部，只需轻轻一点，即可轻松在顶层视图之间切换。

导航菜单由三到五个动作组成，每个动作包含一个图标和一个标签。尽管需要注意的是，只有具有三个动作的导航栏才应该同时包含图标和标签。

[https://medium . com/media/82 ed 4d f 438 ECF dfea 80193148 b 7 bbde 0/href](https://medium.com/media/82ed4df438ecfdfea80193148b7bbde0/href)

### **标签页**

[选项卡](https://material.io/design/components/tabs.html)便于浏览和在不同视图间切换。选项卡组件可以是一个简单的没有附加内容的选项卡，如下面的第一个示例所示，也可以是一个具有多个选项卡的可滚动选项卡。

标签也可以只包含图标作为标题/标签。

[https://medium . com/media/7d 122329 ee 447 FD 0 b 0a6a 807597 b 9347/href](https://medium.com/media/7d122329ee447fd0b0a6a807597b9347/href)

### **列举**

[列表](https://material.io/design/components/lists.html)将多个行项目垂直显示为一个连续的元素。

列表由连续的一列行组成。每行包含一个图块。主要操作填充该区域，补充操作由图标和文本表示。

[https://medium . com/media/84c 83 b 8 f 6 b 0 e 2 a 95 DCD 03 a 71781d 45 a 7/href](https://medium.com/media/84c83b8f6b0e2a95dcd03a71781d45a7/href)

### **纸牌**

一张[卡片](https://material.io/design/components/cards.html)是一张材料，作为更详细信息的入口点。

卡片是显示由不同元素组成的内容的一种便捷方式。卡片是用来展示信息的，可以很容易地浏览，通常有行动号召。

[https://medium . com/media/18e 7 B2 a 641792d 63 A0 d 591 b0ae 86839 e/href](https://medium.com/media/18e7b2a641792d63a0d591b0ae86839e/href)

### **模态**

模态组件为创建对话框、弹出窗口、灯箱等提供了坚实的基础

每当单击一个模式按钮时，它都会执行以下操作:

*   当一次一个不够时，管理对话框堆叠
*   创建背景，用于禁用模式下的交互
*   它恰当地管理焦点；移动到模式内容，并保持在那里，直到模式关闭
*   它禁止在打开时滚动页面内容
*   自动添加合适的 ARIA 角色有[https://medium . com/media/6609756 a 92 c 7 c 39 AE 2d 3 e 65 c 85 e 20862/href](https://medium.com/media/6609756a92c7c39ae2d3e65c85e20862/href)# # #**网格列表**

[网格列表](https://material.io/design/components/image-lists.html)是上述标准列表视图的替代。网格列表由以垂直和水平布局排列的重复模式的单元格组成，它通常突出显示图像。

[https://medium . com/media/b47c 2709 a 35 c 811 a1 BF 1163998986 f01/href](https://medium.com/media/b47c2709a35c811a1bf1163998986f01/href)

下面是一个网格列表的高级例子，它使用 GridListTileBar 为每个 GridListTile 添加一个覆盖。覆盖层可以容纳标题、副标题和辅助动作——在这个例子中是一个 IconButton，可以用来传达更多的信息。

[https://medium . com/media/47 D6 b5 ECD 920 e 441 FB 5 BAF 767 bb1a 618/href](https://medium.com/media/47d6b5ecd920e441fb5baf767bb1a618/href)

### **表**

[数据表](https://material.io/design/components/data-tables.html)显示多组原始数据。它们通常出现在桌面企业产品中。

数据表在顶部包含一个列名的标题行，后面是数据行。

为了便于访问，第一列被设置为

element, with a scope of "row". This enables screen readers to identify a cell's value by it's row and column name. [https://medium.com/media/f414705f8a2b75ea959246345ec1f107/href](https://medium.com/media/f414705f8a2b75ea959246345ec1f107/href)

在下面的示例中可以看到表格的高级用法。如果用户需要选择或操作数据，它会使用伴随每一行的复选框。它还使用可点击的行进行选择。

[https://medium . com/media/c 9de 96 ef 5628478 E8 C1 ff 21 C4 e 63 E8 a 4/href](https://medium.com/media/c9de96ef5628478e8c1ff21c4e63e8a4/href)

### 结论

现在的问题是你应该在你的 React 项目中使用材质设计吗？

这个问题的答案取决于你的项目的用例以及你想要达到的目标。材料设计可以为你的应用程序提供更好的用户界面。它可以启发你的设计者根据指南去思考信息的层次。

与此同时，如果你试图进行品牌重新设计，材料设计可能不是你要找的。你想让你的品牌看起来像互联网上成百上千的其他网站吗？

还可以探索其他材质设计库，比如 [react-md](https://react-md.mlaursen.com/) 、 [materialize](http://materializecss.com/) (一个 CSS 框架)。

### Plug: [LogRocket](http://logrocket.com) ，一款适用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)T2】](http://logrocket.com)

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *