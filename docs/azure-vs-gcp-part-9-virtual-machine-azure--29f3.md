# Azure vs GCP 第 9 部分:虚拟机(Azure)

> 原文：<https://dev.to/kenakamu/azure-vs-gcp-part-9-virtual-machine-azure--29f3>

当我部署我的应用程序时，有几个选择。

*   PaaS 应用托管(WebApps/AppEngine)
*   无服务器功能
*   带 k8s 的集装箱
*   虚拟机器

到目前为止，我部署到 Web Apps (PaaS ),这一次，我尝试将 VM 作为托管目标。我知道很多人使用 container/k8s，但是 VM 仍然是相关的，并且是不错的选择。什么时候？嗯，举个例子:

*   您有作为 Windows 服务运行的旧应用程序
*   您只是想将您的本地应用程序迁移到云
*   你的应用需要特定的执行环境等。

## 单个虚拟机与实例组进行自动扩展

当我使用虚拟机时，我首先需要考虑的是如何扩展，包括纵向扩展和横向扩展。如果我使用 MSSQL，我需要纵向扩展；如果我使用 Web 应用程序，我宁愿横向扩展。

在本文中，我首先关注单实例，看看从操作和开发人员的角度来看我有哪些选择。

# Azure 虚拟机

虽然它是微软云，但它支持非 Windows 以及 Windows 客户端和服务器。它还提供带有操作系统和软件的映像，因此您可以快速部署解决方案，而不仅仅是简单的操作系统。

### 大小

有关详细信息，请参见 Windows 和 [Linux](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 的文章[，但是有许多面向目的的大小选择可用，例如计算优化、内存优化、GPU 等。](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### 地区和价格

有大量的地点可供选择，还有许多价格选项可以优化成本。由于这不是营销博客，我不解释细节，但有[开发/测试价格](https://azure.microsoft.com/en-us/pricing/dev-test/)可用，这对开发者来说是好事。

### 其他特性

我真的不能列出所有的功能，但你应该找到你最需要的功能，例如:

*   （同 solid-statedisk）固态（磁）盘
*   永久磁盘
*   建立工作关系网
*   安全性
*   InternationalAssociationofMachinists 国际机械师协会
*   虚拟机管理
*   监视
*   与其他服务的集成
*   自动化
*   工具
*   定制等。

微软还提供了混合云，因此集成 OnPrem 资产应该也很容易，这对企业开发人员来说是件好事。

# 让我们试试

在本文中，我使用 Windows Server 2016 来托管我的 web C# Full。NET 4.6 Web API 运行在 IIS 上。(出于测试目的，否则我使用 Web 应用程序！)

### 调配虚拟机

首先创建虚拟机。创建虚拟机的方法有很多。从门户、CLI、SDK、ARM 模板等。使用 Visual Studio 进行“发布”有几个前提条件。

*   安装 IIS(带管理控制台)
*   安装 ASP.NET 4.6
*   Web 管理服务
*   Web 部署 3.6
*   打开防火墙
*   配置 DNS 名称

Azure 提供了易于部署的方法(ARM 模板)，所以你可以简单地点击下面的按钮来部署上面所有的。

[![Create ASP.NET VM in Azure](img/49d6d46f4f74b49cb2fa18e6c4ec6be2.png)T2】](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Faspnet%2FTooling%2FAspNetVMs%2FVMSetup%2FASPNet-ARMTemplate.json)

如果您已经启用了要进行 Web 部署的虚拟机，请参考[修补现有虚拟机](https://github.com/aspnet/Tooling/blob/AspNetVMs/docs/create-asp-net-vm-with-webdeploy.md#patch-an-existing-vm)

我懒得用单击部署，结果就是这样。

[![portal](img/9b1ea150658a239d6e923b0208403e84.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yBIHe5eQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1t75daxr50usd1gf7tqq.PNG)

*   虚拟机:虚拟机
*   磁盘:这是连接到虚拟机的 SSD 磁盘
*   存储帐户:存储帐户包含上面的磁盘
*   公共 IP 地址:正如它所说，虚拟机的公共 IP
*   网络接口:虚拟机的网卡
*   网络安全组:定义安全相关设置，如防火墙规则
*   虚拟网络:虚拟机的本地网络

### 创建应用并部署。

1.打开 Visual Studio 2017 并创建新项目。选择 ASP.NET Web 应用程序(。NET 框架)。

[![app](img/f9f8e6821a122ccf61cb982c8db54b92.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XVHC9Sf---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m01atwtmjjot347os83a.PNG)

2.选择 Web API 模板，然后单击确定。这个模板已经包含了 API，所以我只是使用它。

[![app](img/4f412464ca0ee49a88ea09ecc0bbad94.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9bt5KOBc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r8bxnsg97sormo76xard.PNG)

3.右键单击项目，然后单击“发布”。

[![app](img/ee8c580be57961d0d9c267c13c9756b9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qnf9IziR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q4bqhkcgova80hv4g43u.PNG)

4.选择“Microsoft Azure 虚拟机”，然后单击“浏览”。

[![app](img/b6af40408cf3d205a9070002e02dfffb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fqHZFixo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t9euao22elv0uilfmqli.PNG)

5.选择要部署到的虚拟机，然后单击“确定”。

[![app](img/5f24ad4116bb7b878e7dfda489ffaace.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xUCYo-di--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/20m8bvujzm8jrhfiip4j.PNG)

6.单击“发布”并输入虚拟机管理员的用户名和密码。

[![app](img/95c92fd135512ff7a7482475e46decaa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GpA9Dx6A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v2hg4259zbs53zq2rmmf.PNG)

7.您可能会看到证书错误。点击“接受”。

[![app](img/8002bb17021fd3540afd3f853d7ec10e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Pf22msWU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vd845o24wkod64ezf8v6.PNG)

8.等待部署完成。完成后，转到“/api/values”查看它返回的结果。

[![app](img/c3cfbecec63db9abd33454d1596bd80c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cm8Xp_cg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gc9n8ia47a2pwbufaf0a.PNG)

# 提示

在下一篇文章进行扩展之前，这里有一些提示。

### 自动关机

作为开发人员，我不想花太多钱测试，但是关闭 VM 很容易忘记。这就是自动关机拯救你的地方。

转到虚拟机，选择“自动关机”并应用设置。

[![portal](img/516f0b5f1efed5bf3bf0d069d43775d7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mewrU7zu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/isrwjiz7h0ylo9v4kdyz.PNG)

### 备份

您也可以创建备份。我知道您可能不会在容器环境中这样做，但是对于 VM 环境来说，知道这一点是有好处的。

[![portal](img/3c93ec73649d052ad25b1e4e5cb02639.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VqKoYRpw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hmz59q1x8oeoxdzwky3e.PNG)

### SSL

1.将 URL 从 http 更改为 https。应该是这样失败的。为什么？这是因为防火墙阻止了端口 443。

[![vm](img/0db04f76e28dae6c4d7eb34e756313d7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Rb2SMWgO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cutlm146dj2j9q73eb2s.PNG)

2.返回 Azure Portal 并选择虚拟机，然后选择网络。正如您所看到的，端口 80 是打开的，但 443 不是。

[![vm](img/565a4e64b6fe22bf9c219effcd1bdf44.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PY_2V0JK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hxmrgfzbnz13tl4ktmby.PNG)

3.单击“添加入站端口规则”并添加端口 443。您可以通过选择 HTTPS 服务来实现。

[![vm](img/a307ca49114b25e30006a5379a31a48b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xYKlY_wz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/28zgs36lk473u5q3zykh.PNG)

4.刷新浏览器，你仍然有同样的问题。为什么？因为 IIS 没有针对 SSL 的绑定。远程登录虚拟机并检查 IIS 设置

[![vm](img/0d6e2ebf2d50eb24c150845d4f061f61.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--54ZzO96i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sm8u4kt5kvqecubdjsk2.PNG)

5.现在你看到问题了。您需要有 SSL 证书才能使用 SSL。有两种方法可以做到这一点。

*   使用公共证书:在这种情况下，为了匹配主机名，您需要为 VM 使用自定义 DNS 名称。如果你愿意，可以使用 Azure DNS。详情见[此处](https://azure.microsoft.com/documentation/services/dns/)。
*   使用自签名证书:当然浏览器会警告你证书不是自签名的。详细步骤见[此处](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/tutorial-secure-web-server)。

在下一篇文章中，我将研究如何扩展虚拟机环境

肯恩(男名)