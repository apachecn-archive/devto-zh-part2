# 在开发过程中重写 appSettings

> 原文：<https://dev.to/ankitvijay/override-appsettings-during-development-3khb>

在开发过程中，中不同开发人员的本地计算机配置设置并不相同，并且可能与源代码管理中的默认值不匹配，这是一种相当常见的情况。

SQL server 连接字符串就是这样一个例子。一些开发人员可能安装了 SQL Express，而另一些开发人员可能在本地安装了命名实例。然而，`web.config`或`app.config`对于该设置只能有一个值。

人们(*至少“我”习惯于*)处理这种情况的方式通常是在开发期间更改本地机器上的`config`值，但在提交时丢弃那些更改。但是，如果您不小心忘记将设置改回原始设置，那么就有可能破坏其他开发人员的本地机器上的构建或代码。

## 覆盖您的 appSettings

最近我同事(感谢詹姆斯！)给我看了一个很好的小技巧处理这个。我必须说，我有点不好意思不知道这件事。

您可以通过使用`file`属性来覆盖在`config`的`appSettings`元素下定义的键值对。

。要点表{ margin-bottom:0；}

文件`localAppSettings.config`不是您的源代码控制的一部分。如果您的本地设置与`web.config,`完全匹配，那么您不需要在本地保存这个文件。否则，您可以使用该文件来覆盖与`web.config`不同的 _ 设置。

。要点表{ margin-bottom:0；}

当您在代码中使用应用程序设置时，会得到以下值:

| **键->值** |
| `dbConnectionString --> localMachineConnectionString` |
| `someRandomApplicationSetting --> sourceControlApplicationSetting` |

> **重要提示:**如果您使用`connectionStrings`元素来存储您的数据源连接字符串，那么，您可以使用 configSource 属性来代替。然而，这两个属性并不等同。你可以在这里阅读更多关于差异[的信息。](https://stackoverflow.com/questions/6940004/asp-net-web-config-configsource-vs-file-attributes#6940086)

希望这个漂亮的小技巧有助于你的发展。[![🙂](../Images/1bb31e891282bfa40812655c9c9ace9e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DvXCaIjD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s.w.org/images/core/emoji/11/72x72/1f642.png)

帖子[在开发过程中覆盖 appSettings】最早出现在](https://ankitvijay.net/2018/04/21/override-appsettings-during-development/)[嗨，我是 Ankit](https://ankitvijay.net) 。