# 扩展 Dynamics 365 包部署器

> 原文：<https://dev.to/mehmetseckin/extending-dynamics-365-package-deployer-3i7f>

# 扩展 Dynamics 365 包部署器

2018 年 6 月 25 日，英国温莎

Dynamics 365 Package Deployer 是一个非常棒的工具，它允许您在不同环境之间批量移动您的定制和数据。

它导入您的解决方案和数据，并提供一个 API 来将您的定制代码在某些点上与该流程挂钩。这些挂钩如下:

[![Custom code hooks for Dynamics 365 Package Deployer process](img/d1e7277357864b829d2b45e5ef799c7f.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--Rt8c3mYD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://mehmetseckin.cimg/dynamics-365-package-deployer-hooks.png) *为 Dynamics 365 包部署器流程定制代码挂钩*

| 方法名称 | 执行时间 |
| --- | --- |
| `InitializeCustomExtension` | 初始化包部署器扩展之后，解决方案导入过程之前。 |
| `PreSolutionImport` | 在导入每个解决方案之前。 |
| `RunSolutionUpgradeMigrationStep` | 在导入每个解决方案之前，仅当要导入的解决方案已经存在于目标 Dynamics 365 实例中时。 |
| `BeforeImportStage` | 在解决方案导入完成之前，如果正在导入示例数据或平面文件。 |
| `AfterPrimaryImport` | 解决方案和数据导入完成后。 |

在这些方法中，有一些特殊的参数和属性可以设置。可以设置这些参数和属性来改变部署行为，例如禁用启用插件和工作流、在数据导入期间覆盖安全检查等。

还有一些很酷的方法可以让你干预用户界面，比如`CreateProgressItem`、`RaiseUpdateEvent`、`RaiseFailEvent`方法。这些方法使您能够在进度项窗格中创建自己的进度项，但是它们似乎只有在解决方案导入过程开始后才有效。

通过 Dynamics 365 Package Deployer API 提供的另一个很酷的特性是`RootControlDispatcher`。此属性允许您调用包部署者的主 UI 线程中的逻辑。这意味着，您可以在包部署期间使用您的自定义 WPF 用户界面。

Package Deployer 还支持定制的欢迎和结束 HTML 页面，用于许可协议、发行说明等等。它也支持 javascript，但是我还没有尝试在上面运行一个完整的单页面应用程序，我相信 javascript 支持会有一些限制。

[![A JavaScript alert from a custom welcome page in Dynamics 365 deployment package](img/c2780489997f9e114d6d95dd2697688b.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--RtoAfVwZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://mehmetseckin.cimg/dynamics-365-package-deployer-script-alert.png) *来自 Dynamics 365 部署包中自定义欢迎页面的 JavaScript 警报*

如果您想要定制、增强和自动化您的 Dynamics 365 应用程序的部署过程，可以使用这些扩展点完成许多巧妙的事情。