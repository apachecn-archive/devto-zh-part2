# Sitecore 部署者

> 原文：<https://dev.to/akuryan/sitecore-deployer-38el>

在使用基于 ARM 和 PowerShell 脚本的 Sitecore 部署时，我注意到我们有一个问题:这些脚本是在每个项目的基础上使用的，一旦实现，它们就开始老化。因为手动更新每个项目的脚本是一项枯燥而繁琐的任务，所以通常不会执行。因此，我决定构建一个 [VSTS 发布任务](https://marketplace.visualstudio.com/items?itemName=anton-kuryan.SitecoreAzureVstsDeployer)，它可以在我们的每个项目中重用，并使得总是为某个 Sitecore 版本提供最新版本的 ARM 和 PowerShell 脚本变得更加容易。

## 工作原理

您可以在 [my repository](https://github.com/akuryan/vsts.extensions/tree/master/SitecoreDeployer) 查看 PowerShell 脚本。

[![Sitecore Deployer setup](../Images/b50d1c20f47f70357da83840b5ae7221.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XM9C9s2z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dobryak.org/wp-content/uploads/2018/11/colours-deployer-600x335.png)

该扩展使用 VSTS 连接端点针对 Azure 进行授权，并将接受以下输入:

*   **ARM 模板路径**
    工件源发布的 ARM 模板。通常，它是一个构件。
    ARM 参数路径
    以 ARM 参数文件为模板，由工件源发布。它们将在运行时被解析，在“部署”部署类型的情况下，与“基础设施”部署类型的输出相结合，并馈入模板。

*   **资源组名称**
    资源将驻留的目标资源组的名称。如果部署类型是“基础设施”并且资源组不存在，脚本将尝试创建它，但是对于部署类型“部署”，它将抛出一个错误。

*   **位置**
    下拉菜单，显示您的资源的可用位置。

*   **部署类型**
    部署类型之间有几个不同之处:“基础设施”部署类型将尝试创建资源组(如果它不存在)并提供基础设施，而“部署”部署类型将获得“基础设施”部署类型的输出，并将其与模板参数文件中的参数相结合来部署应用程序。

*   **生成服务协议？**
    如果此项设置为 Yes，那么脚本将尝试为 SCWDP 包生成一个短期的 SAS，在参数中传递。存储帐户必须属于我们要部署到的同一订阅。如果无法生成服务协议，它将继续静默运行。如果必须从 URL 中检索 license.xml 文件，并且该文件存储在属于当前订阅的存储帐户上，则脚本也会尝试为其生成一个 SAS。

*   **许可证位置**
    下拉菜单，允许您选择如何将许可证传递给 Sitecore 安装。它可以是 URL，也可以是内联指定的(例如，从实际文件复制粘贴到字段中，或者，更安全的方法是，将值存储在受保护的构建变量中，并在此处添加对它的引用)。但是它也可以是对文件系统的引用(作为构建的工件发布)，最后，您可以说您的 license.xml 文件作为参数被粘贴到您的模板参数文件中。

### 章节“安全”

[建议](https://doc.sitecore.net/sitecore_experience_platform/setting_up_and_maintaining/sitecore_on_azure/analytics/securing_microsoft_azure_resources_for_a_sitecore_deployment)拒绝所有人访问处理角色，只允许 Azure 访问报告角色。此外，我检查了 Sitecore，默认情况下，您不需要为这些角色部署任何自定义代码，这可以大大加快您的部署。因此，只有当您没有在处理和报告角色上部署自己的定制代码，并且没有在 ase 中运行时，才需要这些复选框(因为 ASE 允许在您的 web 应用程序前面部署额外的防火墙)。

*   **通过 IP 限制对中国角色的访问**
    这是一个“吸引人”的设置。如果您检查从 8.2 到 9.0.1 的 Sitecore 模板和 SCWDP 包，您会注意到在所有成熟的部署中，您都可以设置客户端 IP 和客户端 IP 掩码来限制对 CM 和 PRC 实例的访问。MsDeploy (SCWDP)包将有一个字段。默认情况下，它是 0.0.0.0/0.0.0.0。问题是，这个参数允许您指定一个，并且只能指定一个 IP，这很奇怪，因为您至少需要两个:127.0.0.1 以允许 applicationInitialization，由 Web 应用程序本身触发以正确完成初始化，以及您自己的 IP 以访问 CM。在相关参数中指定的 IP 和掩码将被传递到 web.config，并被写入节中。这里又出现了一个问题，因为该部分定义如下:“”，这个“清除”节点删除了 Azure web app 的 Networking 部分中在 Web app 上定义的任何规则。因此，保护仅部署了默认 SCWDP 包(如 PRC role + modules)的 PRC web app 的唯一可能的解决方案是注入参数 IP 127.0.0.1 和掩码 255.255.255.255。这将有效地在 CM web 应用程序上部署相同的限制，但是最终，通常您会在 CM 实例上部署自己的配置文件集。

*   **通过 IP 限制对代表角色的访问**
    根据 Sitecore 最佳实践，建议通过 IP 限制对报告角色的访问，仅允许 Azure IPs。然而，我是这样看的:我们需要允许从已部署解决方案的其他角色(CM、CD、PRC)访问报告角色，可能的话，从我们自己的 IP 访问报告角色。启用此复选框将:
    a)显示在逗号分隔的字符串中输入 IP/掩码的额外控制
    b)在执行脚本时–其他角色的所有传出 IP 地址将被添加到允许列表(以及 127.0.0.1 和在额外控制中输入的任何内容)
    由于 REP SCWDP 包没有嵌入 ipSecurity，因此这可以完美地工作:基于 IP 的限制将部署在 web 应用程序级别(您可以在 Azure web 应用程序的网络部分中看到它)。

这就是我想描述的关于这个新扩展的全部内容。如有其他问题，请随时[联系我](//mailto:dobryak.sng@gmail.com)，推送 PRs 并在存储库上讨论。