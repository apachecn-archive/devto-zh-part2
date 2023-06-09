# Unity -游戏开发的大局观。

> 原文：<https://dev.to/hbibzdeploy/unity---a-big-picture-on-game-development--59m5>

# [T3】](//#Unity---A-big-picture-on-game-development.---Getting-started)

[![Unity Logo](img/2bcd378c32c8f4e6aca197fddf356289.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9e6MqkJN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://unity3d.com/profiles/unity3d/themes/uniimg/company/brand/logos/primary/unity-master-black.svg)

## 简介:[T3】](#Intro:)

你好，不管你是想从事游戏开发还是想发现如何创建游戏的过程，学习令人惊叹的引擎统一都是一个好的开始，它绝对有很多好处。实际上，现在的游戏开发包括开发这种能力来创建 2D/3D 游戏，并且更加关注概念和设计而不是物理引擎等...

在本文中，您将了解使用 Unity 是多么容易，以及使用这个游戏开发平台开始一个项目的要点。过程链将以更多的实践方式呈现，每个任务回答一个给定的问题。

### 如何创建空白项目？[T3】](#How-to-create-a-blank-project?)

下载 Unity 后，当您第一次启动它时，会提示您启动向导，让您能够创建一个新项目或浏览以前的项目和下载教程。要创建新项目:

1.  点击*【新建】*
2.  为您的项目选择一个名称
3.  为您的项目选择一个位置，所有资源和所需文件都将存放在该位置。
4.  点击*“创建项目”*完成后，您需要等待 Unity 生成开始新项目所需的文件。A ###如何打开一个项目？[T3】](#How-to-open-a-project?)

从您的启动向导中，您可以选择“打开”,以便使用文件资源管理器浏览所需的现有项目。然后你可以选择“在磁盘上”来浏览你电脑中的本地文件或者“在云中”

### 项目文件的层次结构？[T3】](#Project-files'-hierarchy?)

[![](img/f25faf64a6750ef4fc4d37385f1253a8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--P2li4yEv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://unity3d.com/sites/default/files/styles/original/public/learn/VZfolderstructure0img1.png%3Fitok%3DVfFkx2IQ)

为了在 Unity 中设置我们的文件夹结构，从我们的项目窗口(所有资源的位置和我们添加对象到场景视图的位置)中，我们右键单击鼠标并点击 Create >> Folder，然后给它一个名称。现在，我们为资产正式创建的这个文件夹是在我们为项目选择的路径下创建的。确切位置:{项目路径}/资产/{创建的文件夹}

**注意，项目经理对文件夹结构的每次更改都会影响项目文件夹，反之亦然**

### 如何导入包？[T3】](#How-to-import-packages?)

Unity asset store 和已经完成的 Unity 项目提供了 Unity 包，这是你在构建自己的项目时可能需要的东西。为了导入这样的包(***【UAS】***):

1.  选择*窗口* > > *资产商店*。
2.  在搜索栏中键入您需要的资产类型，您可以使用过滤功能来查找最适合您需求的资产。
3.  选择所需的软件包后，点击按钮*下载*并同意服务条款。![](img/04338e4e4d6f72b829270ff144c0e891.png)
4.  一旦下载的包准备好了，您就可以使用 *import* 按钮导入它。![](img/06548f4f1d359e7353653ecc6a84c4ed.png)
5.  完成上一步后，您会注意到项目窗口中的资产。要浏览它们，您可以双击添加的文件夹。

**要导入一个特定的安装包，需要在项目管理器区右键点击> > *【导入包】*>>{想要的资产文件夹名称}，之后同样的步骤也适用**

### 什么是 3D 资产？[T3】](#What-are-3D-assets?)

创建游戏时，你实际上需要添加一些资产和环境，比如占位符对象。这可以通过以下方式实现:

1.  从*层级*面板中选择*创建*。
2.  选择【3D 对象】> > {一个对象} ![](img/ff9439233f29155083d6f24bf0331556.png)
3.  选择好想要的形状后，可以使用*缩放工具*拖动彩色立方体来修改尺寸。![](img/5ab45361df3f0e8c4363e962da89e3cb.png)

一旦有了边界(地板)，就可以添加角色。制作游戏原型时的一个标准是插入胶囊(3D 图元)来代替/代表临时玩家，而不是 3D 建模角色的形状并将其 UVing，并使用 PhotoShop 之类的东西来创建最终的外观，并将一切作为资产(动画剪辑)导入。

### *如何导入它们？

设置 3D 模型，并确保其符合 Unity 比例，然后将轴点导出到 fbx 中 Unity 项目的资产/网格文件夹位置...在 FBX 管道中，如果模型是动画的，请确保选中动画选项。确保“高级选项”下的“缩放”设置为 1.0，这必须在“网格”文件夹中添加一个游戏对象，当选择该对象时，通过将其拖动到场景来添加导入选项。现在检查器窗口已经出现，您可以修改导入对象的属性，如比例因子。

我希望你喜欢第一篇文章，这是穆罕默德·哈比卜·贾瓦迪。一如既往，继续问！！