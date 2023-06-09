# SharePoint 2016 升级:如何解决“功能在数据库中被引用，但未安装在当前服务器场中”错误

> 原文：<https://dev.to/mlisidoro/sharepoint-2016-upgrade-how-to-solve-the-feature-is-referenced-in-database-but-isnt-installed-on-the-current-farm-error-10cc>

帖子 [SharePoint 2016 升级:如何解决“功能在数据库中被引用，但没有安装在当前服务器场中”错误](https://blogit.create.pt/miguelisidoro/2018/09/16/sharepoint-farm-update-how-to-solve-the-feature-is-referenced-in-database-but-isnt-installed-on-the-current-farm-error-in-a-sharepoint-2016-farm/)首先出现在[博客 IT](https://blogit.create.pt) 上。

你好，

最近，我将一个 SharePoint 2010 服务器场迁移到 SharePoint 2016，并使用 PowerShell 和 [AutoSPInstaller](https://autospinstaller.com/) 的自动化安装过程来安装新的 SharePoint 2016 服务器场，确保应用最新的更新，使 SharePoint 2016 服务器场保持更新并正常工作(如果您想了解更多有关如何使用 PowerShell 以自动化方式安装 SharePoint 服务器场的信息，我邀请您单击[此处](https://dev.to/mlisidoro/how-to-install-a-sharepoint-2016-farm-using-powershell-and-autospinstaller-part-1-54m5)和[此处](https://dev.to/mlisidoro/how-to-install-a-sharepoint-2016-farm-using-powershell-and-autospinstaller-part-2-5e36)。

在安装了服务器场和迁移了 web 应用程序之后，服务器场中安装了新的更新(如果您想要跟上最新的 SharePoint 更新，请单击[此处](https://buildnumbers.wordpress.com/sharepoint/))但是当我运行 SharePoint 产品和配置向导以确保新的更新正确安装时(要了解在 SharePoint 服务器场更新之后运行 SharePoint 产品和配置向导的更多信息，请单击[此处](https://blogs.technet.microsoft.com/stefan_gossner)，我收到了一个错误，该错误阻止我完成更新过程。

**重要说明:**源 SharePoint 2010 和目标 SharePoint 2016 服务器场都拥有标准 SharePoint 许可证。

# 问题

安装更新并运行 SharePoint 产品和配置后，我收到以下错误:

[![](img/a6b66b5d3a3c2d7650ee51936c9992db.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4k8UyEHG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/blogit.create.pt/wp-content/uploads/2018/09/SP2016_PSConfig_Error.jpg%3Fresize%3D621%252C536%26ssl%3D1)

错误详细信息:

Microsoft 类型的异常。引发了 SharePoint . postsetupconfiguration . postsetupconfigurationtaskexception。附加异常信息:

*功能(名称= [SharePoint Portal Server 状态指示器列表模板]，Id =[065 c 78 be-5231-477 e-a972-14177 cc 5b 3c 7]，描述= [SharePoint Portal Server 状态指示器列表模板]，安装位置=****【BizAppsListTemplates】)在数据库[Content_DB]** ，**中被引用，但未安装在当前服务器场中** _* 。缺少的功能可能会导致升级失败。如有必要，请安装包含该功能的任何解决方案并重新启动升级。(事件 ID:ajxkh)_

*Feature(Name =[SharePoint Server 企业网站功能]，Id =[0806 d127-06e 6-447 a-980 e-2e 90 b 03101 b 8]，Description =[**SharePoint Server 企业许可证中包含的 Visio Services、Access Services 和 Excel Services 应用程序等功能。**，* *安装位置=**【premium web】)在数据库 Content_DB]中被引用，但没有安装在当前服务器场中*** *。缺少的功能可能会导致升级失败。如有必要，请安装包含该功能的任何解决方案并重新启动升级。(事件 ID:ajxkh)*

*功能(名称= [SharePoint Portal Server 报表库]，Id =[2510 d73f-7109-4 CCC-8a1c-314894 deeb3a]，描述= [SharePoint Portal Server 报表库]，安装位置=* ***【报表模板】)在数据库[Content_DB]中被引用，但没有安装在当前服务器场*** *。缺少的功能可能会导致升级失败。如有必要，请安装包含该功能的任何解决方案并重新启动升级。(事件 ID:ajxkh)*

*Feature(Name =[InfoPath Forms Services Support]，Id =[c 88 C4 ff 1-db F5-4649-ad9f-c6c 426 ebcbf 5]，Description =**[InfoPath Forms Services 列表和相关页面，用于在服务器上显示表单。]，安装位置=[**ipfssiteffeatures])在数据库 Content_DB]中被引用，但没有安装在当前服务器场*** *上。缺少的功能可能会导致升级失败。如有必要，请安装包含该功能的任何解决方案并重新启动升级。(事件 ID:ajxkh)*

*特性(Name =[数据连接特性]，Id =[00 bfea 71-DBD 7-4f 72-b8cb-da7ac 0440130]，Description = []，安装位置=* ***【数据连接库】)在数据库 Content_DB 中被引用，但没有安装在当前服务器场*** *。缺少的功能可能会导致升级失败。如有必要，请安装包含该功能的任何解决方案并重新启动升级。(事件 ID:ajxkh)*

*升级[SPContentDatabase Name = Content _ DB]失败。(事件 ID:an59t)*

*异常:**升级后的数据库模式与目标模式不匹配** _* ** ** (EventID:an59t)_

*由于出现异常，升级计时器作业正在退出:* _Microsoft。SharePoint . upgrade . spupgradeexception:**升级后的数据库架构与目标架构**不匹配

这里有两个主要问题:

*   内容数据库中有对 SharePoint 企业功能(InfoPath Form Services、Visio Services、Excel Services 等)的引用—我的猜测是，在此过程中的某个时候，源 SharePoint 2010 服务器场拥有企业 SharePoint 许可证，但被降级为标准许可证，但在内容数据库中留下了对企业功能的引用
*   内容数据库架构版本与服务器场中安装的 SharePoint 更新版本不匹配

确认存在升级问题的另一个症状是管理中心的升级状态页显示升级状态为失败:

[![](img/cb8185ed455a94e60fea88e509ed7915.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--85FK6JQ2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/blogit.create.pt/wp-content/uploads/2018/09/SP2016_Upgrade_Status_Error.jpg%3Fresize%3D696%252C206%26ssl%3D1)

# 解

此问题的解决方案由两部分组成，均应用于目标 SharePoint 2016 服务器场中:

*   修复内容数据库中缺失的企业功能引用
*   修复内容数据库架构版本不匹配的问题

有关该解决方案的更多详情，请点击[此处](https://blogit.create.pt/miguelisidoro/2018/09/16/sharepoint-farm-update-how-to-solve-the-feature-is-referenced-in-database-but-isnt-installed-on-the-current-farm-error-in-a-sharepoint-2016-farm/)。

分享快乐！

帖子 [SharePoint 2016 升级:如何解决“功能在数据库中被引用，但没有安装在当前服务器场中”错误](https://blogit.create.pt/miguelisidoro/2018/09/16/sharepoint-farm-update-how-to-solve-the-feature-is-referenced-in-database-but-isnt-installed-on-the-current-farm-error-in-a-sharepoint-2016-farm/)首先出现在[博客 IT](https://blogit.create.pt) 上。