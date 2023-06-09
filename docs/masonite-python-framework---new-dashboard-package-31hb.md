# Masonite Python 框架-新的仪表板包！

> 原文：<https://dev.to/masonite/masonite-python-framework---new-dashboard-package-31hb>

# 简介

我想到了一个新的 Masonite 包，叫做 Masonite Dashboard。这个仪表板本身完全是准系统，实际上只包含一个欢迎消息，但允许使用严格的服务提供商来扩展它。

这意味着您安装的任何软件包都能够创建自己的管理仪表板模块。也许你的下一个条纹支付集成包包含一个仪表板模块来管理订阅或发送发票的权利从管理面板。或者下一个 API 包包含一个仪表板模块来监控 API 的使用。

这就是这个新仪表板包的作用。您可以阅读有关安装 [Masonite Dashboard](https://docs.masoniteproject.com/official-packages/masonite-dashboard) 的更多信息，但本文档将讲述如何轻松地将其扩展为用户管理系统，您可以:

*   查看用户
*   添加用户
*   搜索用户
*   删除用户
*   作为用户登录(坏蛋)

如果你想继续，那么你必须在这里阅读如何安装[mason ite Dashboard](https://docs.masoniteproject.com/official-packages/masonite-dashboard)并回到这篇文章。如果你只是想看看它有多棒，那就继续读下去。

安装 Masonite 仪表板后，我们看到一个新的仪表板，如下所示:

[![](img/8b47cd633a02d5c0b076b6f4fa1a1350.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OzeshRW9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hff7bq9ue337bsscaydn.png)

# 配置

这里配置很小。一旦我们安装了 Masonite Dashboard，并根据文档成功登录，我们只需要为每个附加组件执行一些配置步骤。我们将在这里扩展内置的仪表板模块，并添加一个用户管理系统。

## 服务商

首先，我们需要在`config/providers.py` :
的`PROVIDERS`列表中添加一个服务提供商

```
...
from dashboard.providers import UserManagementProvider
...

PROVIDERS = [
    ...
    DashboardProvider,
    UserManagementProvider,
] 
```

Enter fullscreen mode Exit fullscreen mode

对服务提供商来说就是这样。让我们继续向我们的应用程序添加一些必需的路线。

## 路线

只需导入所需路线并将其添加到您的`ROUTES`列表中。

```
from dashboard.routes import management_routes
...

ROUTES = [
    ...
    management_routes(),
    ...
] 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！现在，让我们打开控制面板，我们将看到一个新的导航链接:

[![](img/9d3e74f5dcbf75a37dd05f73299a66bc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_1Q7eS4O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ckmit74zq4dnfxarhskw.png)

如果我们单击该链接，我们将被带到新模块，在那里我们可以添加、删除和作为其他用户登录:

[![](img/58ea8268a841e5a0405ab0ae8923cfaa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GXQEqp34--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lhtjsrahcm4acu6ekdgo.png)

就这么简单！

* * *

如果你想为软件包的开发做贡献或者对 Masonite 的开发感兴趣，那么一定要加入 [Slack](http://slack.masoniteproject.com) 频道或者在 [GitHub](https://github.com/MasoniteFramework/masonite) 上启动回购。谢谢！