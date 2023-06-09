# 与 React Native 中的调试和发布版本不同的 iOS 构建配置

> 原文：<https://dev.to/brightdevs/different-ios-build-configurations-than-debug-and-release-in-react-native-1hm7>

默认的 iOS 构建配置， **Debug** 和 **Release** 对于开始来说可能已经足够了，但是迟早你可能需要在你的应用中增加构建配置的数量。虽然这在本机开发中非常简单，但不幸的是，在 React Native 中却不是这样。

## 构建配置

要管理构建配置，请转到 Xcode 项目设置([项目]→信息):

[![image](img/e794a3c2301b21512beea1a812762ba1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6W7cG2ax--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/edih81a8se7fhi0l5km1.png)

您可以通过**复制现有的**来轻松添加新的构建配置。为开发复制调试配置，为测试飞行和应用商店构建发布配置。您也可以通过快速双击来重命名任何配置。

[![image](img/ae62782f68f712c71a9191b96fda65d5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bnX7t7GV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zl9edwvzh9k4ftjy6o4d.png)

#### 调试 vs 发布

调试配置构建完整的符号调试信息，无需任何代码优化，以便于调试应用程序。在发布配置中，代码执行得到优化，并且在构建中不包含符号信息，这导致最终可执行文件的大小更小。发布版本将执行得更快，尽管由于编译器优化，您可能会看到内存布局或初始化方面的差异，这可能会导致所谓的“仅发布”错误。

#### 运行构建配置

上述步骤足以在本地开发中建立新的构建配置。要在构建配置中运行应用程序，请打开**方案**并更改运行构建配置:

[![image](img/aebc26c33cdc5f90a759d40d247a3399.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AvSaVedF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/739ak83xkzy3ndeyn5yw.png)

## 反应自然中的问题

要使用 React Native 中指定的构建配置启动 iOS 应用程序，只需运行:

`react-native run-ios --configuration Stage`

不幸的是，这对于任何基于发布的配置都将失败，并显示一条错误消息，指出它在 AppDelegate 中找不到`RCTBundleURLProvider`:

> AppDelegate.m:2:9:未找到“React/RCTBundleURLProvider.h”文件

我们需要为新的发布构建配置在目标构建设置([目标]→构建设置)中添加缺少的**头搜索路径**和**库搜索路径**。

[![image](img/5374d81d5ac3dca48388a6002ca3308e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_Hb4nRw1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n9mxgzg5nqtiabh5lamz.png)

为每个新的发布版本配置在**标题搜索路径**中添加一个条目:

$(构建目录)/发布-$(平台名称)/包含

[![image](img/1dd7fab8fd9ac26710bc965b7a1a06e5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NwK3MEyi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mbq1065ou78vt7aickdo.png)

为每个新的发布版本配置在**库搜索路径**中添加一个条目:

$(构建目录)/发布$(有效平台名称)

[![image](img/634fa3e6ef25a13ae82c7196c8b25db0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Of18ssi1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f57t1ju2jhhv53lbaknb.png)

**接下来，对所有测试目标**进行同样的操作，否则命令仍然会失败。

最后，您可以使用指定的构建配置来启动应用程序:

`react-native run-ios --configuration Stage`

它应该运行没有任何问题。

有人可能想知道，当我们只改变构建配置名称，并且它已经为发布工作时，为什么还需要这样做。 **React Native 支持调试和发布开箱即用的构建配置**，而其他任何配置都需要手动设置。

## 乌云背后总有一线希望

在没有任何经验的情况下，在 React Native 中定义不同于 Debug 和 Release 的 iOS 构建配置确实会令人沮丧。然而，只要稍加指导，这可能是一个快速而无痛的过程。

如果你对如何为每个构建配置设置不同的 Google 配置感兴趣，我推荐你阅读这个教程。虽然它是为本地开发而写的，但是你可以**在 React Native** 中设置完全相同的运行脚本。

最初发布于 [brightinventions.pl](https://brightinventions.pl/blog/)

作者 Mateusz Klimczak,《推动事物前进》@光明的发明

[推特](https://twitter.com/Klimczak_M)，[邮件](mateusz.klimczak@brightinventions.pl)