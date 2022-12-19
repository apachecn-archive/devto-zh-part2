# 使用 LiveData 监控 GPS 和位置许可检查(第 1 部分和第 2 部分)

> 原文：<https://dev.to/wahibhaq/monitoring-gps-and-location-permission-checks-using-livedata-part-1--2-2251>

在我们的上一个项目中，我们开发了一个智能手机应用程序，通过了解用户的需求，为车主提供关于他们驾驶的实时数据，并提供一种比较合适电动汽车的机制，来消除电动汽车焦虑。为了实现这一点，通过应用程序跟踪驾驶行为是该项目的一个重要方面。这意味着该应用程序应该能够在用户开车的过程中不断地在后台收听位置坐标。

我们必须执行检查，以确保设备上的 **GPS** 已启用，并且用户授予了**位置许可**。需要强调的是，这两者对于确保应用程序能够在所有驾驶过程中执行位置跟踪至关重要。

### 用例🚙

> 想象一个场景，当用户安装应用程序，通过 onboarding，可能授予位置许可，但忽略了提示启用 GPS 的对话框。更糟糕的是，用户没有授予访问权限或者后来因为任何原因撤销了访问权限。现在可以肯定的是，用户甚至会忘记新安装的应用程序，预期的目标不会实现。

### 更新的用例🚙

> 想象一个场景，当用户安装应用程序，通过 onboarding 和授予位置许可，并在提示时启用 GPS。用户仍有可能在将来的任何时候出于任何原因撤销许可。更糟糕的是，用户开始开车，但没有启用 GPS。这些驱动器将永远不会被跟踪，应用程序将无法执行其预期目标。

我们最好的办法是在应用程序处于**前台或后台**时发出视觉通知，以鼓励用户满足其中一个或两个要求。只有当 UI(活动/片段)和服务可以监听 GPS 和运行时权限的变化并做出相应的反应时，这才是可能的。

在这样做的时候，我面临了几个挑战，并决定分享我的经验和解决方案。这不是有史以来最好的解决方案，但目前是可行的，我将非常高兴和感谢听到以更好的方式做这件事的反馈，✌️

## 示例应用程序 UI

[![](../Images/30301c8b6a4f9a6520b3ca68891753ac.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--O5CEfoKs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/273389/47611112-c152a500-da5e-11e8-85dd-73dbd1cd1e7c.png)

[![](../Images/97b06663d17849ed820156455d91751e.png)T2】](https://i.giphy.com/media/xWfplzYLnWSF3zivrm/giphy.gif)

[![](../Images/67e3e312563c4c0a92cf97dbc99c3ef4.png)T3】](https://i.giphy.com/media/1ynT3NyWY0daNA8XEk/giphy.gif)

### 履行

在我进入实现部分之前，我想提一下，我们的团队已经决定使用[架构组件](https://developer.android.com/topic/libraries/architecture/)，所以选择 [LiveData](https://developer.android.com/topic/libraries/architecture/livedata) 来实现 GPS 和权限监听器是一个自然的选择。

在本教程的第一部分中，我们可以看到如何使用 LiveData 实现检查，并在 UI 级别进行观察。

在本教程的第二部分中，我们可以看到如何使用 LiveData 实现检查，并在后台服务级别进行观察。

* * *

可以看看 [GitHub](https://github.com/ki-labs/gps-permission-checks-livedata/) 上的代码。