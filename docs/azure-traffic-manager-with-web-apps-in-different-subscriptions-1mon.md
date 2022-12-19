# Azure 流量管理器，带有不同订阅的 Web 应用

> 原文：<https://dev.to/jpda/azure-traffic-manager-with-web-apps-in-different-subscriptions-1mon>

我们在 WTF 总部收到一些奇怪而有趣的请求，今天也不例外。来自同事:

> 我的一个客户在一个子网站有 web 应用程序，并希望在不同地区的其他子网站也有一个灾难恢复[网站]。似乎我们不能将 TM 与 diff 订阅一起使用。还有别的办法吗？

的确，医生说 [**这不支持**](https://docs.microsoft.com/en-us/azure/traffic-manager/traffic-manager-faqs#traffic-manager-endpoints) 。你现在也许应该停止阅读，回到你之前做的事情，但是谁能拒绝一个有趣的挑战呢？

来自[文档](https://docs.microsoft.com/en-us/azure/traffic-manager/traffic-manager-faqs#traffic-manager-endpoints):

> Azure Web Apps 无法使用来自多个订阅的端点。Azure Web Apps 要求与 Web Apps 一起使用的任何自定义域名只能在单个订阅中使用。不能使用具有相同域名的多个订阅的 Web 应用程序。

这份声明中有许多需要解释的内容。我认为其中一些是不准确的，至少在当前的实现中是如此——无论订阅与否(甚至是租户),验证同一个主机名并将其与多个 web 应用相关联是非常容易的。)使用验证域的 *awverify* 方法。

但是，让我们重新开始，尝试打破这个坚果。我们需要:

*   两份各种口味的天蓝色订阅
*   两个 Azure 应用服务(web 应用)，每个订阅一个。请确保您的应用服务计划是共享的或更高版本，以便使用自定义域。如果你需要 TLS/SSL，基本或更高(你应该知道，现在是 2018 年)
*   任一子目录中的流量管理器名称空间
*   自定义域以及该域对您的 DNS 的访问

我的资源名为…

*   web app 1 是**test-multi-sub-1.azurewebsites.net**
*   web app 2 是**test-multi-sub-2.azurewebsites.net**
*   流量经理的 DNS 名称是**test-multi-sub.trafficmanager.net**
*   用户访问应用程序的目标主机名:**test-multi-sub.eastus3.com**

### 第一个 web app

如果您的第一个 web 应用程序和您的流量管理器实例在同一个订阅中，请像往常一样使用 Azure 端点，并选择该订阅中的 web 应用程序。

[![You’ll note only one of our web apps is available for selection, since TM can’t traverse subscription boundaries.](../Images/62755053bcbf536cefbd02b66970289a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XFEMShrX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jpda.dev/img/1%2520__BzyL4j2MpO5Q5fy4SWw__%2520BA.png) 你会注意到我们的网络应用程序中只有一个可供选择，因为 TM 不能穿越订阅边界。

对于另一个订阅中的第二个 web 应用程序，我们将使用一个[外部端点](https://docs.microsoft.com/en-us/azure/traffic-manager/traffic-manager-endpoint-types#external-endpoints)；外部端点就是这样一个 FQDN 或 IP，它指向你的 web 应用所在的位置，不管它是托管在 Azure、AWS、本地还是其他地方。

让我们将第二个 web 应用程序作为外部端点添加到我们的流量管理器实例中。这非常简单—选择外部并填写一些字段:

[![External endpoint, FQDN of our app, done.](../Images/7d39d78df09a4475ef2465a5e9b28653.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--h-g3nKaV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jpda.dev/img/1__DxcqgGtNfD2yWmKPech7OQ.png) 外部端点，我们 app 的 FQDN，搞定了。

### DNS、自定义主机名和 awverify

现在我们已经在 Traffic Manager 中获得了两个端点，我们可以继续为我们的单个 web 应用程序配置自定义主机名。这很重要，因为应用服务是由主机头驱动的。如果您的两个 web 应用程序没有添加相同的主机名，它将不会响应请求。

在我的例子中，用户用来连接我的网络应用的最终名字是[http://test-multi-sub.eastus3.com](http://test-multi-sub.eastus3.com)——我们需要

*   将`test-multi-sub.eastus3.com`作为自定义主机名添加到每个 web 应用程序中
*   将`test-multi-sub.trafficmanager.net`作为自定义主机名添加到我们的外部 webapp 端点
*   在 DNS 中，为指向`**test-multi-sub.trafficmanager.net**`的`**test-multi-sub.eastus3.com**`添加一个 CNAME。
*   例如`test-multi-sub IN CNAME test-multi-sub.trafficmanager.net`

这也很简单，但有点像骗局。要在 Azure Web 应用中验证自定义主机名，您需要添加一些 DNS 记录来验证您拥有自定义主机名。有两种方法可以做到这一点，我们将使用`awverify`方法，这让我们可以验证一个域，而无需实际将域本身指向应用服务。相反，我们用一些相关的信息创建 TXT 记录。

### 验证自定义主机名

打开你的 DNS 控制台——Azure DNS，Route 53，GoDaddy，随便什么——为你的第一个 web 应用程序添加一个 TXT 记录，使用以下语法:

*   主机名— `awverify.targethostname.yourdomain.com` —在我的例子中，`awverify.test-multi-sub.eastus3.com`您可能不需要在您的记录创建中添加完整的根域名(例如，eastus3.com)，但这取决于您的 DNS 提供商的接口。
*   设置类型= TXT
*   值= `<yourwebappname>.azurewebsites.net`
*   例如`test-multi-sub-1.azurewebsites.net`
*   TTL —短。比如几秒或几分钟。

这是 Azure DNS 控制台中的样子:

[![Set a short TTL — we’ll be changing this shortly.](../Images/e3ee24f8c5bbe5bb1f6223cff7cff118.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JmFVr7Fr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jpda.dev/img/1__qSt9TrLEWmOM7SdFSNIC6Q.png) 设置一个短的 TTL——我们很快会改变这个。

保持 DNS 窗口打开，因为我们一会儿还会用到它。打开一个新窗口，我们将使用新的主机名配置我们的 Web 应用程序。

### 向 webapp 添加主机名

打开你的网络应用程序，进入导航栏中的自定义域。这是非常标准的，你将如何添加一个自定义域到任何 web 应用程序。

[![a](../Images/edff90cb7ac6961a6a97df0f30c4df9d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EtgNhNKt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jpda.dev/img/1__FNZOEGkh6pYMVzdCU7Xzhg.png)

接下来，我们将添加主机名

[![a](../Images/9905c8bd0cc635036d5533e720ca85d6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8FkM-q5q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jpda.dev/img/1__QoyGD4cFUbVU80w03b4zyA.png)

输入用户将用来访问您的应用程序的目标主机名。请记住，我们需要 web 应用程序来响应该主机名上的请求，因为该主机名是我们将与 traffic manager 一起使用的主机名。

如果您输入的 awverify DNS 记录是正确的并且已经复制，您应该会在底部看到两个漂亮的绿色复选框:

[![You can ignore the ‘test-multi-sub.trafficmanager.net’ entry in the image below. We’ll get to that in a minute :)](../Images/c0856e53eda30b290e3b5f538df9de36.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1h8g-27T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jpda.dev/img/1%2520__VenTP1voiAQnoA__%2520vpZxqGA.png) 可以忽略下图中的‘test-multi-sub . traffic manager . net’条目。我们一会儿就会谈到这一点:)

如果一切正常，您可以在验证面板中点按“添加主机名”。回到主视图，您应该会看到新的主机名分配。

[![yay](../Images/28160c40c741a422d39bb6724a0f9a18.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EyeX4XDI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jpda.dev/img/1%2520__o__%2520VU4Zq1jx7ygVZyfP4WDA.png) 耶

### 重新配置 DNS 和我们的第二个 webapp

首先，让我们重新配置 DNS，让我们的`awverify`记录指向我们的第二个 webapp。在我的例子中，我们将使用`test-multi-sub-2.azurewebsites.net`，而不是`test-multi-sub-1.azurewebsites.net`。

[![Note the subtle difference — test-multi-sub-2 instead of test-multi-sub-1](../Images/fef582e96e029ac096c2c738f6b51b9f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AAZjqGWJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jpda.dev/img/1%2520__24Ws7a7E8L36F__%2520l%2520__oUc__%2520oA.png) 注意细微的区别——把测试-多-子-2 改为测试-多-子-1

保存 DNS 记录后，让我们转到第二个 webapp 的自定义域。这在很大程度上将是相同的过程，*然而，*您不会在门户中获得您期望的 visula 反馈。我认为，这主要是因为这是一个不受支持的场景，无论如何你都不应该这样做。

[![Add the same target domain as the custom hostname here too.](../Images/90d24ec2cec1192028b23c8b9621ee36.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lmvKGOUA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jpda.dev/img/1%2520__TTjM__%2520zIaYFzeSiOQpeQfOA.png) 在这里也添加与自定义主机名相同的目标域。

请记住，使用与之前相同的 DNS 主机名，即您的用户将用来实际访问应用程序的名称。就我而言，`test-multi-sub.eastus3.com`。你会注意到你再次得到验证绿色复选框；您还会收到一个成功的“添加主机名”通知。

[![a](../Images/097f5b18e2a0fa6d48d6f5918598c438.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8emD2OtL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jpda.dev/img/1%2520____%2520yWSThttkTictZwcWUbbXg.png)

你在 UI 中看不到的是你的主机名，至少最初看不到。我唯一的假设(你知道他们对假设怎么说的)是这没有通过一些验证，但不足以阻止实际提交变更。

在这里，继续添加您的流量管理器 DNS 名称作为自定义主机名。同样的事情应该会发生——您会得到一系列绿色的复选标记，但是您的自定义主机名可能不会出现。他们没有为我，直到一段时间后，在一个完整的页面刷新。

[![They may not show up, but…](../Images/9cae9e826b2328d6699ceafcc6867b8d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NbSKc-lI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jpda.dev/img/1__Er2DbQjdSdZPxNC3ELuYgA.png) 他们可能不会出现，但是…

我们可以使用`Get-AzureRmWebApp -Name web-app-name`或者在 CLI 中使用`az webapp show --resource-group <your rg> --name web-app-name`来验证它们是否真的被添加到 PowerShell 中并且可用

[![Powershell, like if C# had a goofy syntax](../Images/9997841bc34415aadb4af870d7cd96c0.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--ZB6zsrF1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jpda.dev/img/1__3P2cR8d777kMpwfeDGU4oQ.png) Powershell，好像 C#有一个傻乎乎的语法 [![Web app 2, for reference](../Images/dce5116c1e4302a7f861814c08bb7f4f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hFuMhGOC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jpda.dev/img/1__cXjFiKZT7eSYEx0HlyFHRg.png) Web app 2，供参考 [![CLI, for when all your friends on twitter make fun of you for using powershell](../Images/1c1a12a1ee15342b778ffddbb0d3e7d6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PbDmJVRE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jpda.dev/img/1__IVbFaSvyox49di4OLNjOvg.png) CLI，供当你所有的朋友在 twitter 上取笑你使用 Powershell 时使用

### 全部检查完毕

假设我们做到了这一步，我们应该能够通过用户自定义主机名([http://test-multi-sub.eastus3.com](http://test-multi-sub.eastus3.com))导航到我们的站点，并访问我们的一个 web 应用程序。此时，流量管理器可以控制通过该主机名的流量。

[![I have simple webapp deployed to both apps that just has a string of the name of the host](../Images/f6f6034930e38c240f0ae21c1a696e10.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--605vq6bV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jpda.dev/img/1__yEYv4vlVnlERvQx8HQepMA.png) 我有一个简单的 webapp 部署到两个应用程序，只有一个主机名称字符串 [![This tab was before an ipconfig /flushdns and a shift-reload on the browser](../Images/da12d998f55470c05c8c46f0732f889d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CluIZj1G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jpda.dev/img/1__KPpNTDNzKl2MzB__9vzkddg.png) 这个标签在浏览器上的 ipconfig /flushdns 和 shift-reload 之前

### 大功告成！

导航到您的站点，您应该能够通过流量管理器名称访问您的两个端点。您可能想要尝试使用权重并刷新 DNS 解析器缓存，以查看两个站点从相同的端点名称进行解析。

### 完事了？

差不多了。我们可以看到主机名被绑定到我们的 web 应用程序，这很好，这意味着当流量被发送到具有该主机名的 web 应用程序时，我们的 web 应用程序实例会做出响应。但也是 2018 年了，我们需要 TLS/SSL。

回到门户，让我们在两个 webapps 上配置 TLS/SSL 绑定。如果你的应用服务计划是共享或免费的，你必须升级到 SSL 的基本或更高版本。

转到每个 web 应用程序中的 SSL 设置，我们将上传并绑定我们的证书。现在我们完成了！

[![Upload your cert!](../Images/a9ea73894ea0e0a145c2d2f3ca8677e6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IrL_3QMg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jpda.dev/img/1__KlDUgESRgmHEUrU6EO7i0g.png) 上传您的证书！ [![Bind it up to both sites!](../Images/ac24e86d01b08f4e70810d6adb061898.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QxxPVBkY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jpda.dev/img/1__H1d__p4sAYWBCNP0BSoNFJA.png) 把它绑到两个地点！[T7](https://res.cloudinary.com/practicaldev/image/fetch/s--1vcEm8z---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jpda.dev/img/1__oDPZL8rr9bgGp2VVlOp__xA.png)耶