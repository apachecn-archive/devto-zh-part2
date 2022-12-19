# 如何修复 SharePoint 2013 服务器场中降级的搜索索引分区

> 原文：<https://dev.to/mlisidoro/how-to-fix-a-degraded-search-index-partition-in-a-sharepoint-2013-farm-3i4a>

帖子[如何修复 SharePoint 2013 服务器场中降级的搜索索引分区](https://blogit.create.pt/miguelisidoro/2018/10/07/how-to-fix-a-degraded-search-index-partition-in-a-sharepoint-2013-farm/)首先出现在[博客 IT](https://blogit.create.pt) 上。

你好，

最近，一位客户报告了 SharePoint 2013 服务器场中的搜索问题。没有显示搜索结果，而是显示了以下错误:

[![](../Images/2d874bd831c3e9393e33253e31a815f7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JXO2KzJN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/blogit.create.pt/wp-content/uploads/2018/10/SP2013_Search_Problem.png%3Fresize%3D512%252C66%26ssl%3D1)

# 问题

为了诊断该问题，我打开了管理中心，搜索服务应用程序页面显示搜索服务应用程序拓扑中的索引分区组件有问题。

[![](../Images/014a009c335d253d8635e50e6c6109b3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nDk-M4Ih--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/blogit.create.pt/wp-content/uploads/2018/10/Central_Admin_Index_Partition_Degraded.png%3Fresize%3D696%252C77%26ssl%3D1)

为了进一步诊断这个问题，我打开了一个 PowerShell 窗口(使用“以管理员身份运行”选项来确保适当的权限)并执行了以下命令:

$ SSA = Get-SPServiceApplication-Name "搜索服务应用"

Get-spenterprisearchstatus-Search Application $ SSA-Detailed-Text

执行后，结果确认了索引分区的问题，显示了处于降级状态的索引组件。

[![](../Images/dd0b1c78d4393230d24a9cd4319a695b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--W-51SrPg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/blogit.create.pt/wp-content/uploads/2018/10/PowerShell_Index_Partition_Degraded.png%3Fresize%3D679%252C343%26ssl%3D1)

# 解

该解决方案由两个步骤组成:

*   重置搜索索引
*   对搜索内容源执行完全爬网

## 重置搜索索引

要重置搜索索引，我转到管理中心的搜索服务应用程序中的重置索引页面，并单击**“立即重置”**按钮。

[![](../Images/cdf6313352b30a0af2b831595aaea739.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--t6YQFnj6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/blogit.create.pt/wp-content/uploads/2018/10/SP2013_Search_Index_Reset.png%3Fresize%3D696%252C197%26ssl%3D1)

为了确认索引重置，我点击了弹出消息中的**“OK”**。

[![](../Images/eda1d983b890863d8b67dfcbbd8b3607.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yCgYVdAb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/blogit.create.pt/wp-content/uploads/2018/10/SP2013_Search_Index_Reset_Confirmation.png%3Fresize%3D416%252C170%26ssl%3D1)

## 对搜索内容源执行完全爬网

重置索引后，搜索索引数据被删除，为了再次显示搜索结果，我们需要对服务器场中的所有内容源执行完全爬网。为此，我们必须转到管理中心的 Search Service 应用程序中的“内容源”页面，并为每个可用的内容源选择**“开始完全爬网”**。

[![](../Images/0dc4bcf907aede92586900fec4e5214e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Wp1vYRc1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/blogit.create.pt/wp-content/uploads/2018/10/SP2013_Content_Search_Search_Full_Crawl.png%3Fresize%3D696%252C285%26ssl%3D1)

**注意:**如果您有多个内容源，请先完成对前一个内容源的完全爬网，然后再开始对下一个内容源进行完全爬网。

完成对所有内容源的完全爬网后，我返回到管理中心和搜索服务应用程序页面，以检查搜索服务应用程序的状态。该页面现在显示所有搜索组件的健康状态。

[![](../Images/5575dae674607f4ac91b879bc226b7ac.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--S7xBcOYu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/blogit.create.pt/wp-content/uploads/2018/10/Central_Admin_Index_Partition_OK.png%3Fresize%3D696%252C77%26ssl%3D1)

为了确认问题已经解决，我运行了之前在 PowerShell 窗口中执行的相同命令。

$ SSA = Get-SPServiceApplication-Name "搜索服务应用"

Get-spenterprisearchstatus-Search Application $ SSA-Detailed-Text

执行之后，结果确认索引部分的问题已经解决，在索引组件中显示一个激活状态。

[![](../Images/3dbf5174bf4dd354c5af307d11e2a5de.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PO4tMQmd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/blogit.create.pt/wp-content/uploads/2018/10/PowerShell_Index_Partition_OK.png%3Fresize%3D677%252C346%26ssl%3D1)

完成上述步骤后，SharePoint 网站再次开始返回搜索结果！

**注意:**此解决方案也应对 SharePoint 2016 和 SharePoint 2019 有效。如果您想了解 SharePoint Server 新版本的所有新功能，请点击[此处](https://dev.to/mlisidoro/sharepoint-server-gets-modern-sharepoint-2019-is-here-194f)。

要找到我最近在 SharePoint 2016 服务器场中发现的搜索爬行问题的解决方案，请单击[此处](https://dev.to/mlisidoro/sharepoint-2016-problems-crawling-content-and-the-the-crawler-could-not-communicate-with-the-server-error-message-1pjl)。

如果您是 SharePoint 开发人员或管理员，并希望了解更多有关如何使用 PowerShell 以自动化方式安装 SharePoint 2016 服务器场的信息，请单击[此处](https://dev.to/mlisidoro/how-to-install-a-sharepoint-2016-farm-using-powershell-and-autospinstaller-part-1-54m5)和[此处](https://dev.to/mlisidoro/how-to-install-a-sharepoint-2016-farm-using-powershell-and-autospinstaller-part-2-5e36)。

希望这能帮助到有同样问题的人！

分享快乐！

帖子[如何修复 SharePoint 2013 服务器场中降级的搜索索引分区](https://blogit.create.pt/miguelisidoro/2018/10/07/how-to-fix-a-degraded-search-index-partition-in-a-sharepoint-2013-farm/)首先出现在[博客 IT](https://blogit.create.pt) 上。