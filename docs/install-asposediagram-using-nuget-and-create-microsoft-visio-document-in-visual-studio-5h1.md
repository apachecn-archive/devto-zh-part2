# 安装 Aspose。在 Visual Studio 中使用 NuGet 和创建 Microsoft Visio 文档的图表

> 原文：<https://dev.to/asposediagram/install-asposediagram-using-nuget-and-create-microsoft-visio-document-in-visual-studio-5h1>

[Aspose。Diagram](https://products.aspose.com/diagram) API 帮助开发人员处理 Microsoft Visio 文件。他们可以在自己的应用程序中创建、操作和转换 Visio 文件格式，而无需安装 Microsoft Visio。阿斯波斯。Diagram 是一个轻量级 API，适合客户端和服务器端使用。该 API 是可扩展的、易于使用的、紧凑的，并且提供了通用的功能，使得开发者在执行通用任务时必须编写更少的代码。

**支持的平台**

> 阿斯波斯。图表支持两者。NET 和 Java 平台。请参见 [Aspose。的图表。NET](https://products.aspose.com/diagram/net) 和 [Aspose。图为 Java](https://products.aspose.com/diagram/java) 供大家参考。

**文章描述**

> 在本文中，我们将安装一个 Aspose。图表，并在 Visual Studio 中使用它创建 Microsoft Visio 文档。

# 阿斯波斯。图表获取下载链接

您总是可以找到 Aspose 的最新版本或早期版本。图来自下面的 NuGet 链接。请查看下面给出的快照。

*   [https://www.nuget.org/packages/Aspose.Diagram/](https://www.nuget.org/packages/Aspose.Diagram/)

[![Snapshot of Aspose.Diagram NuGet Download Link.](img/04f2ccd3913d26ca3a6cfceeb62a4f38.png "Snapshot of Aspose.Diagram NuGet Download Link.")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JU4phEqA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/AsposeDiagram/AsposeDiagram-Screenshots-and-Sample-Files/master/Install-Aspose.Diagram-using-NuGet-and-Create-Microsoft-Visio-Document-in-Visual-Studio/Snapshot-of-Aspose.Diagram-NuGet-Download-Link.png)

# 安装 Aspose。在 Visual Studio 中使用 NuGet Pacakage Manager 的图表

在本节中，我们将创建一个控制台应用程序项目并安装 Aspose。使用 NuGet 包管理器的图表。为了方便起见，我们将用截图来说明所有涉及的步骤。

## 创建控制台应用程序项目

*   打开 Visual Studio。下面的截图展示了 Visual Studio 2017 但是你可以使用任何 Visual Studio。

[![Open Visual Studio 2017 or any other.](img/3911a34f8611bb7f885b5e001b17ddea.png "Open Visual Studio 2017 or any other.")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--H_pSJbIZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/AsposeDiagram/AsposeDiagram-Screenshots-and-Sample-Files/master/Install-Aspose.Diagram-using-NuGet-and-Create-Microsoft-Visio-Document-in-Visual-Studio/Open-Visual-Studio-2017-or-any-other.png)

*   点击*新建>项目*。

[![Click New > Project.](img/53370c26029e11c8b5c71ca8066ace88.png "Click New > Project.")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---PJlq56g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/AsposeDiagram/AsposeDiagram-Screenshots-and-Sample-Files/master/Install-Aspose.Diagram-using-NuGet-and-Create-Microsoft-Visio-Document-in-Visual-Studio/Click-New-Project.png)

*   选择*控制台应用程序(。NET Framework)* 并将其命名为*testaposediagram*然后点击 *OK* 。

[![Select Console App (.NET Framework) and give it name TestAsposeDiagram.](img/5017cf7e53aab7140190f548748d5612.png "Select Console App (.NET Framework) and give it name TestAsposeDiagram.")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tx4O00HX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/AsposeDiagram/AsposeDiagram-Screenshots-and-Sample-Files/master/Install-Aspose.Diagram-using-NuGet-and-Create-Microsoft-Visio-Document-in-Visual-Studio/Select-Console-App-%28.NET-Framework%29.png)

*   名为*testaposediagram*的控制台应用程序项目将在 Visual Studio 中创建。

[![TestAsposeDiagram console application project created.](img/c083d95da5f0db3f11ae0f372c9d8db7.png "TestAsposeDiagram console application project created.")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--R49oJ3Yg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/AsposeDiagram/AsposeDiagram-Screenshots-and-Sample-Files/master/Install-Aspose.Diagram-using-NuGet-and-Create-Microsoft-Visio-Document-in-Visual-Studio/TestAsposeDiagram-console-application-project-created.png)

## 安装 Aspose。使用 NuGet Pacakage Manager 的图表

*   右键单击*解决方案浏览器*中的项目，然后单击*管理 NuGet 包…*

[![Click Manage NuGet Packages inside the Solution Explorer.](img/56f653e58e926f2554e19d9d92ba58fe.png "Click Manage NuGet Packages inside the Solution Explorer.")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZWmyAefM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/AsposeDiagram/AsposeDiagram-Screenshots-and-Sample-Files/master/Install-Aspose.Diagram-using-NuGet-and-Create-Microsoft-Visio-Document-in-Visual-Studio/Click-Manage-NuGet-Packages-inside-the-Solution-Explorer.png)

*   点击*浏览*，键入*作为建议。图*，你会看到一个蜘蛛。图表列出，选择它并点击*安装*按钮。

[![Click Install button when Aspose.Diagram is listed in the NuGet Packages.](img/3431e04d8b9a32205f982704cc918714.png "Click Install button when Aspose.Diagram is listed in the NuGet Packages.")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IlP6D_cC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/AsposeDiagram/AsposeDiagram-Screenshots-and-Sample-Files/master/Install-Aspose.Diagram-using-NuGet-and-Create-Microsoft-Visio-Document-in-Visual-Studio/Click-Install-Aspose.Diagram-listed-in-NuGet-Packages.png)

*   将出现*预览修改*对话框。单击确定。

[![Preview Changes dialog box will appear. Click OK.](img/d19077c6e020eb9b856470843a2487b4.png "Preview Changes dialog box will appear. Click OK.")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RvUquETt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/AsposeDiagram/AsposeDiagram-Screenshots-and-Sample-Files/master/Install-Aspose.Diagram-using-NuGet-and-Create-Microsoft-Visio-Document-in-Visual-Studio/Preview-Changes-dialog-box-will-appear-Click-OK.png)

*   将出现*许可接受*对话框。您可以查看许可证并接受它以继续操作。

[![License Acceptance dialog box appears, please view and accept the license of Aspose.Diagram.](img/fb8844a0dab33d59cd5fd37e4fbef29d.png "License Acceptance dialog box appears, please view and accept the license of Aspose.Diagram.")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fBOCcO4S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/AsposeDiagram/AsposeDiagram-Screenshots-and-Sample-Files/master/Install-Aspose.Diagram-using-NuGet-and-Create-Microsoft-Visio-Document-in-Visual-Studio/License-Acceptance-dialog-accept-license-Aspose.Diagram.png)

*   NuGet package Manager 安装 Aspose。并在*输出*窗口中显示进度信息。

[![NuGet Package Manager installs Aspose.Diagram and shows progress in Output window.](img/de8d9a9d8c08e3ab3e787f9ff36451d4.png "NuGet Package Manager installs Aspose.Diagram and shows progress in Output window.")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jNH-PwjK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/AsposeDiagram/AsposeDiagram-Screenshots-and-Sample-Files/master/Install-Aspose.Diagram-using-NuGet-and-Create-Microsoft-Visio-Document-in-Visual-Studio/NuGet-installs-Aspose.Diagram-shows-progress-Output-window.png)

*   在*解决方案浏览器*中查看*引用*，你会发现，*是一个 pose。如该截图所示，图*出现在那里。

[![Aspose.Diagram is present inside References in Solution Explorer.](img/01432e3a64f878fb3888d09eb2094483.png "Aspose.Diagram is present inside References in Solution Explorer.")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cHa0d1Gu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/AsposeDiagram/AsposeDiagram-Screenshots-and-Sample-Files/master/Install-Aspose.Diagram-using-NuGet-and-Create-Microsoft-Visio-Document-in-Visual-Studio/Aspose.Diagram-present-in-References-in-Solution-Explorer.png)

*   将鼠标指针悬停在*内的*路径*上。图引用属性*找到 DLL 的路径并检查*版本*号。

[![Path and Version of Aspose.Diagram shown inside assembly Properties inside the Solution Explorer.](img/8af8480ed60c096d7638cc52ffa4c25d.png "Path and Version of Aspose.Diagram shown inside assembly Properties inside the Solution Explorer.")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--v7IC5A7K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/AsposeDiagram/AsposeDiagram-Screenshots-and-Sample-Files/master/Install-Aspose.Diagram-using-NuGet-and-Create-Microsoft-Visio-Document-in-Visual-Studio/Path-Version-Aspose.Diagram-Properties-Solution-Explorer.png)

*   查看显示 Aspose 的 *packages.config* 文件。图表获取包及其版本。您可以稍后修改它，以尝试 Aspose.Diagram 的其他版本

[![View packages.config file that displays the Aspose.Diagram NuGet package and its version.](img/8bd65f7f8ffff0a0fb922856a86a67cd.png "View packages.config file that displays the Aspose.Diagram NuGet package and its version.")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ojWrp7Jd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/AsposeDiagram/AsposeDiagram-Screenshots-and-Sample-Files/master/Install-Aspose.Diagram-using-NuGet-and-Create-Microsoft-Visio-Document-in-Visual-Studio/View-packages.config-displays-Aspose.Diagram-NuGet-package.png)

# 使用 Aspose 创建 Microsoft Visio 文档。图表

*   请首先使用指令添加下面的 **C#。**

```
using Aspose.Diagram; 
```

*   现在在*主*方法中添加下面的 **C#代码**，然后按 *F5* 或 *Ctrl + F5* 运行它。

> 代码创建了一个名为 *Aspose 的对象。Diagram.Diagram* 类并将其保存为*outputbysposediagram . vsdx*。请注意， *VSDX* 是代表 XOPC/XML 绘图的 Microsoft Visio 格式。