# 在 Dynamics 365 和零售服务器中启用应用程序模式

> 原文：<https://dev.to/shanalikhan/enable-application-mode-in-dynamics-365-and-retail-server-b>

零售服务器是商业运行时的包装器。Retail Server 使用 web API 和 OData 来支持商店中的瘦客户机以及平板电脑和手机上的瘦客户机。商务运行时通过商务数据交换服务与零售总部通信。

零售服务器支持服务到服务认证，这使得服务能够与零售服务器通信，而不需要用户在屏幕前在某个时间点提供凭证。这使得当您需要创建流程来定期联系零售服务器以执行不同类型的任务时，例如，同步客户、订单、发布电子商务功能产品等。

要设置该功能，应完成以下主要步骤:

1.  在 Azure Active Directory 中创建一个应用程序，该应用程序将对应于您的守护程序应用程序-该应用程序将调用零售服务器。
2.  将应用程序的详细信息添加到 RS 的认证白名单中，这样 RS 将能够接受来自该应用程序的请求。

这些将在下面详细描述。

#### 在 Azure Active Directory 中创建应用程序

[![image 1](../Images/1faa851334174c8c230f2ed90bfcf4bc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rU7NtiYC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3txyr9medtkbf7thwmd0.png)

导航到[门户](https://manage.windowsazure.com)并选择 Azure Active Directory

选择应用程序注册并创建新的应用程序注册。

1.  输入名称和 Url。
2.  选择类型为 Web 应用程序/ Api。
3.  创建按钮

[![Image 2](../Images/11b9a0ea8bfd1cf11cbaf81b0a0772b8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NWaBK59g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qq0cx46vtgscc61npqyt.png)
[![image 3](../Images/ea42988fbaa2f22f35ee8814dfc1bd56.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--cqsuMcvL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pverzacggnznaavcmmmk.png)

打开创建的应用程序，复制应用程序 ID 并单击 Keys。

[![image 4](../Images/81eacbaa9a3174be0f0b92472bd59387.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hW9LAO72--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2esu8n09v59j27jrq6qc.png)

创建新密钥；请确保您复制了密钥，因为它将只显示密钥一次，并将其隐藏，唯一的方法是获得新的密钥(如果丢失)。

[![image 5](../Images/9aee4f019e42fcb714ea189de62a733c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DNL6AXrm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/06ul60nzbkj2e0vryk4u.png)

现在你有了应用程序/客户端 ID 和客户端密钥，它们将在应用程序和 D365 中进一步使用。

#### 将应用详细信息添加到零售服务器白名单中

登录 Dynamics 365 从(零售和商业>总部设置>参数)打开 D365 中的零售共享参数

默认情况下，租户已经存在于“身份提供者”中，否则，您需要添加您的组织租户。

选择您的租户，然后单击“依赖方”上的添加。

1.  输入客户 ID
2.  选择机密类型
3.  选择用户类型作为应用程序

在“服务器资源 id”中添加零售服务器 URL

[![image 5](../Images/d3cb4e6685b94e53b7ed16db038ba1b0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4zoF1niz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/elicyb6x3wtqwtsc92uy.png)

转到-零售和商业>零售 IT >分销计划

运行作业 1110(全局配置)。等待作业完成其工作，此外，如果您不想等到缓存过期，并且如果这不是生产环境，您可以执行 iisreset。

[![image 6](../Images/99b1c88ea116a1c53fd2acac12e74989.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XOOpNoWV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hijbojny15l4fm0s3p4s.png)

#### 连接从代码

你需要安装 nuget 包“微软。identity model . clients . activedirectory "进行连接

这些是我用来创建零售服务器认证上下文的几个术语。

1.  D365Url:这是 D365 示例的零售服务器 URL:[https://testServerret.cloud.onebox.dynamics.com/](https://testServerret.cloud.onebox.dynamics.com/)
2.  retailServerUri:这是零售服务器元数据 URL 示例:[https://testServerret.cloudax.dynamics.com/Commerce/](https://testServerret.cloudax.dynamics.com/Commerce/)
3.  Client_Id:这是在 azure portal 中创建的应用程序的客户端 Id
4.  Client_Secret:这是在 azure portal 中创建的应用程序的客户端密钥
5.  Azure_Active_Directory:这是 D365 的租户。格式:[https://sts.windows.net/TENANT_ID](https://sts.windows.net/TENANT_ID)；例如:[https://STS . windows . net/7yu 653 w-65d 3-4u 96-a135-8u i4 EC 1672 E3/](https://sts.windows.net/7yu653w-65d3-4u96-a135-8ui4ec1672e3/)
6.  OperatingUnitNumber:在线商店编号

```
AuthenticationContext authenticationContext = new AuthenticationContext(azureActiveDirectory, false);
AuthenticationResult authResult = null;
authResult = await authenticationContext.AcquireTokenAsync(D365Url, new ClientCredential(clientId, clientSecret));
ClientCredentialsToken clientCredentialsToken = new ClientCredentialsToken(authResult.AccessToken);
RetailServerContext retailServerContext = RetailServerContext.Create(this.retailServerUri, this.operatingUnitNumber, clientCredentialsToken);
return ManagerFactory.Create(retailServerContext); 
```