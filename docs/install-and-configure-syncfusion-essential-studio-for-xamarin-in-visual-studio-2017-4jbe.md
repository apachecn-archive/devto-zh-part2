# 在 Visual Studio 2017 中安装和配置 sync fusion Essential Studio for xa marin

> 原文：<https://dev.to/logeshpalani98/install-and-configure-syncfusion-essential-studio-for-xamarin-in-visual-studio-2017-4jbe>

## **简介**

本文演示了如何在 Visual Studio 2017 中安装和配置 sync fusion Essential Studio for xa marin。Syncfusion 为您的 Xamarin 项目(Xamarin)提供 Visual Studio 工具箱。安卓，Xamarin。表格，Xamarin。IOS)。仅支持 Visual Studio 2015 及以上版本。在这个 Syncfusion 工具箱中，有 130 多个免费控件，无需在 Xaml 设计器中编码(拖放控件)即可使用。[更多详情](https://www.syncfusion.com/products/xamarin)

**目标用户—**初学者

开始吧！

### **第一步:**

下载用于 Xamarin 的 Syncfusion Essential Studio。

| [![](img/e4d1d14cd80c043e225eb1e00b2deeb2.png "Download") ](https://2.bp.blogspot.com/-gdUDOv95cIY/W9Cct6mWSjI/AAAAAAAALtA/T8FSjUqSa6o6uA1yuByVnMyMTSKsJ4q2wCLcBGAs/s1600/1.png) |
|下载|

首先可以下载 sync fusion Essential Studio for xa marin，然后双击打开可执行文件并运行它。

|[![](img/7d61c08b90aaea991a05df1aa9f08cfc.png)|](https://4.bp.blogspot.com/-_y0cul5RsRE/W9CcuotCyNI/AAAAAAAALtI/v32dbDezpWYNX03InYvReNwRHDwl6CaxwCLcBGAs/s1600/2.png)| |
| |继续进行

几秒钟后，将出现一个新窗口。在此窗口中，单击继续按钮；然后单击下一步和安装按钮。安装后，打开 Syncfusion Xamarin 控制面板并转到附加组件和实用程序选项卡。

| [![](img/dc7b30f215c8e6e7f12186e72669dd7e.png) ](https://1.bp.blogspot.com/-nFCSs8B4ZD0/W9Cct3xrkCI/AAAAAAAALtE/Jv2SPs9LHoQGKC6utg1GaHViunKqEt90wCLcBGAs/s1600/3.png) |
| Toolbax 安装|
|
|

接下来，运行工具栏安装程序。在新窗口中，选择您的 Visual Studio 版本，然后单击“配置”按钮。配置完成后，重新启动 Visual Studio。

|[![](img/724ebd1cd3d5976166ed465247dec1ee.png)|](https://4.bp.blogspot.com/--xAQFDAFWtk/W9CcwMBx1tI/AAAAAAAALtM/rA34qhfKVU4jpemN1E8Dv-wFgL0OFD2eQCLcBGAs/s1600/4.png)|
|【工具箱】

### **第二步:**

```
 Add Syncfusion Xamarin Controls in your project. We have two ways to add the controls to your project. The first one is creating a new project with Syncfusion Template controls and the second one is adding Syncfusion controls to your existing projects. 
```

* *步骤 2.1: ** Syncfusion Xamarin 项目模板

您可以创建一个新的 Syncfusion Xamarin 项目模板，方法是转到文件> >新建项目> > Visual C# > >跨平台> >选择 Syncfusion Xamarin 项目模板，给出应用程序名称和解决方案名称，并选择框架版本。然后单击确定。

| [![](img/adb66189bd19d67cd59d2b7c7979ae68.png) ](https://3.bp.blogspot.com/-39eihxZohQA/W9CcwYkWIOI/AAAAAAAALtQ/rsyMN79zyGMeQV7HevUvr0f6aTjTCgk5wCLcBGAs/s1600/5.png) |
|新项目|
|
|

现在，选择 Android 的最低版本和目标版本，iOS 目标设备和版本>>选择您想要为主页设计的控件的复选框，然后按下 Create。

[![](img/2e8efe1bdaff35450519fb61ebae08c4.png)T2】](https://2.bp.blogspot.com/--SV3-6nLv9Q/W9CcxePI6xI/AAAAAAAALtU/KfJRmhvQAdIuxNHG3HagPIQNwFGoC1B8ACLcBGAs/s1600/6.png)

**步骤 2.2:** 通过 Visual Studio 工具箱
添加 Syncfusion 控件
创建 Xamarin 项目。以下步骤用于通过 Visual Studio 工具箱添加 Syncfusion 控件。

*   打开 Visual Studio，选择视图菜单>>其他窗口>>选择 Syncfusion 工具箱。

| [![](img/491eb02d66d0841c001d7f7853578044.png) ](https://1.bp.blogspot.com/-1KrC6siwLLg/W9Ccy7iRN5I/AAAAAAAALtY/MR4jq4tlAjEdKwtmLROAIWzPiIP9Wyq9QCLcBGAs/s1600/7.png) |
| Syncfusion 工具箱|
|
|

|

*   打开 Syncfusion 工具箱。Syncfusion 控件仅在 Xamarin 中启用。表单设计器页面(。xaml 页面)。
*   将所需的 Syncfusion 控件拖放到。xaml 页面。自动生成丢弃的控制代码。

|[![](img/1022ba01d890bc1e8938a185efcb9634.png)](https://4.bp.blogspot.com/-9y5BCmsRTj4/W9CczRGWjWI/AAAAAAAALtc/_v6fB33oUVIBDBiK6tm5fgCL5M8k8PNzACLcBGAs/s1600/9.png)|
|
|
|

|

基于项目中的控件安装 Nuget 包。

## **结论**

我们已经了解了如何在 Visual Studio 2017 中安装和配置 Syncfusion Essential Studio