# 在 Java 中以编程方式操作 Microsoft Visio 文件格式

> 原文：<https://dev.to/asposediagram/manipulate-microsoft-visio-file-format-programmatically-in-java-20c9>

[Aspose。图表](https://products.aspose.com/diagram/java) API 可用于打开、读取、更新、更改、操作和构建 Microsoft Visio 文件及其组成部分，而无需安装或自动化 Microsoft Visio 应用程序。

**目的**

> 本文的目的是解释如何在 Java 中以编程方式操作 Microsoft Visio 文件格式。

**演示**

> 本文演示了如何加载**模板 Microsoft Visio 文件**并导入一些*主图形*，如矩形、椭圆、六边形、动态连接线等。从**模板微软 Visio 文件**中创建*普通形状*并用颜色和格式化文本填充它们*通过连接器*连接它们并保存为**输出微软 Visio 文件**。

# 安装 Aspose。在 Eclipse IDE 中使用 Maven 的图表

为了执行示例代码，建议您了解如何安装 Aspose。在 Eclipse IDE 中使用 Maven 的图，这在本文中已经单独讨论过。请根据您的需要浏览一遍。

*   [安装 Aspose。使用 Maven 绘制图表，并在 Eclipse IDE 中创建 Microsoft Visio 文档](https://medium.com/asposediagram/install-aspose-diagram-using-maven-and-create-microsoft-visio-document-in-eclipse-ide-b556cb0d9a5b)

# Aspose 支持的 Microsoft Visio 格式。图表

阿斯波斯。图表支持所有 Microsoft Visio 格式。下面列出了其中的一些。

*   **VSD**——*绘图*
*   **VSS** — *模版*
*   **VST**——*模板*
*   **VSDX** — *OPC/XML 绘图*
*   **VSSX** — *OPC/XML 模板*
*   **VSTX** — *OPC/XML 模板*
*   **VSDM**——*OPC/XML 绘图，启用宏*
*   **VSSM** — *OPC/XML 模板，启用宏*
*   **VSTM** — *OPC/XML 模板，启用宏*

# 输入 Microsoft Visio 文件内部使用的代码

本节介绍并解释示例代码中使用的输入 Microsoft Visio 文件。

## 模板 Microsoft Visio 文件

示例代码使用 Aspose.Diagram 操作名为 *[的**模板 Microsoft Visio 文件**templatemanchantivevisiofileusingasposediagram . vsdx](https://github.com/AsposeDiagram/AsposeDiagram-Screenshots-and-Sample-Files/blob/master/Manipulate-Microsoft-Visio-file-format-programmatically/templateManipulateVisioFileUsingAsposeDiagram.vsdx)*。请注意，您可以使用任何 Microsoft Visio 文件作为模板，代码应该工作正常。

[![Template Microsoft Visio file to be manipulated using Aspose.Diagram.](img/486ea921a8c83cf6049e1a774b1e32f7.png "Template Microsoft Visio file to be manipulated using Aspose.Diagram.")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--w1xxsRvk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/AsposeDiagram/AsposeDiagram-Screenshots-and-Sample-Files/master/Manipulate-Microsoft-Visio-file-format-programmatically/Template-Microsoft-Visio-file-manipulated-Aspose.Diagram.png)

## 包含主控形状的模具 Microsoft Visio 文件

代码使用包含示例**主控形状**的**模板 Microsoft Visio 文件**命名为[*samplemastershapes . vssx*](https://github.com/AsposeDiagram/AsposeDiagram-Screenshots-and-Sample-Files/blob/master/Manipulate-Microsoft-Visio-file-format-programmatically/SampleMasterShapes.vssx)，并使用 Aspose.Diagram 将它们导入模板文件中

*   *矩形*
*   *椭圆*
*   *六边形*
*   *动态连接器*

[![Stencil Microsoft Visio file to import Master Shapes using Aspose.Diagram.](img/0168acd6309735253cf69f8d7b98d550.png "Stencil Microsoft Visio file to import Master Shapes using Aspose.Diagram.")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--htZhz91h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/AsposeDiagram/AsposeDiagram-Screenshots-and-Sample-Files/master/Manipulate-Microsoft-Visio-file-format-programmatically/Stencil-Microsoft-Visio-Master-Shapes-Aspose.Diagram.png)

# 样本代码

请使用提供的输入 Microsoft Visio 文件执行下面的 *Java 示例代码*。该代码执行以下任务。

*   将模板 Microsoft Visio 文件加载到图表(即*com . aspose . Diagram . Diagram*)对象中。
*   访问模板 Microsoft Visio 文件中的*第 1 页*。
*   从*模具* Microsoft Visio 文件导入*主控形状*。
*   使用主控形状添加*普通形状*，并为其命名。
*   通过分配的名称访问普通形状。
*   用颜色填充普通形状，并在其中添加格式化文本。
*   使用*连接器*将正常形状相互连接。
*   将图表对象保存为 Microsoft Visio *VSDX* 格式。

> 代码用有用的注释和注释块很好地解释了，这将帮助你容易地理解它。