# 微软 Power BI 嵌入了一个 WordPress 插件

> 原文：<https://dev.to/upnrunnhq/microsoft-power-bi-embedded-a-wordpress-plugin-45i>

**1。什么是 Power BI？**

Power BI 是微软提供的商业分析服务。它提供了具有自助业务智能功能的交互式可视化，最终用户可以自己创建报告和仪表板，而不必依赖信息技术人员或数据库管理员。

**2。它在真实的商业世界中多么有用**

Power BI 是一套业务分析工具，可为您的整个组织提供洞察力。连接到数百个数据源，简化数据准备，并推动即席分析。制作精美的报告，然后发布供您的组织在 web 和移动设备上使用。每个人都可以创建个性化的仪表盘，以独特的 360 度视角查看他们的业务。并在整个企业中扩展，内置治理和安全性。

**3。将分析嵌入到您自己的应用中**

借助面向应用开发人员的 Power BI Embedded，您可以将可视化分析嵌入到您的产品中，这样您的用户和客户就可以获得有价值的见解，并且您可以快速进入市场。要在您的应用程序或门户中嵌入 Power BI，您需要至少一个 Power BI Pro 帐户，该帐户将作为您的应用程序的主帐户。该主帐户将允许您生成嵌入令牌，以使您的应用程序能够访问 Power BI 仪表板、报告和可视化。

**4。将分析嵌入你的 WordPress 网站**

我们已经创建了“Power BI Embedded for WordPress”(URL:[https://wordpress.org/plugins/embed-power-bi/](https://wordpress.org/plugins/embed-power-bi/))插件，它使用 Power BI REST APIs 和 JavaScript APIs，使用短代码在 WordPress 站点上嵌入 Power BI 仪表盘、报告、磁贴和各种类型的内容。该插件遵循微软在[https://docs . Microsoft . com/en-us/power-bi/developer/embedding-content](https://docs.microsoft.com/en-us/power-bi/developer/embedding-content)记录的“应用拥有数据”流程。请参见[https://docs . Microsoft . com/en-us/Power-bi/developer/embedding](https://docs.microsoft.com/en-us/power-bi/developer/embedding)了解微软关于如何支持 BI 嵌入内容的更多信息。

**5。作为插件与 WordPress 集成的步骤是什么，它是如何构建的**

转到插件网址:[https://wordpress.org/plugins/embed-power-bi/](https://wordpress.org/plugins/embed-power-bi/)

安装 WordPress 插件有不同的方法

–进入你的 WordPress 管理区，然后进入插件列表，然后点击列表顶部的添加新按钮链接，从添加插件页面

你可以在搜索框中搜索“Power BI Embedded for WordPress”插件，然后该插件将出现在搜索列表中，你可以点击“立即安装”按钮。

–另一种安装插件的方法是，你可以从 WordPress 网站下载，插件网址:[https://wordpress.org/plugins/embed-power-bi/](https://wordpress.org/plugins/embed-power-bi/)，安装在你的 WordPress 托管网站的插件目录中，并启用该插件。

**6。为您的 power BI 帐户安装插件的步骤**

一旦你安装了这个插件，你需要有一个超级商业智能帐号。在 Power BI 的 settings 选项中，您可以配置如下详细信息:

 `User Name, Password, Client ID, Client Secret.` 

添加以下详细信息后，保存设置，您应该可以看到 OAuth Status 字段的正确对勾。

配置插件后，你可以像在页面上添加短代码一样使用插件。您可以从 Power BI 菜单中添加 Power BI 项目，并添加新的帖子类型，根据需要填写详细信息并发布项目。

一个短代码将产生，将出现在项目列表页面。

例如:**【power bi id = " 13 " width = " 100% " height = " 350 px "】**

您可以在您的帖子或页面中使用此短代码来呈现 Power BI 部分。

我们添加到 power bi 插件中的另一个重要功能是管理和控制在 Azure portal 上创建的资源。

这个选项允许 wp-admin 在某种程度上控制资源，这样他们就可以使用 API 调用来调度资源**开始**和**暂停**资源。

我们增加了一个选项来保存需要在插件设置页面中配置的一些必需的详细信息，就像在*“调度电源 BI 资源”*部分下一样。有一个选项可以添加*租户 ID、订阅 ID、资源组、容量*。所有这些信息都需要使用这个插件来远程控制资源。

添加适当的设置后，您可以为每个工作日选择一个时间**启动**和**暂停**资源。

例如:*如果你想要像周一那样的时间表，资源应该在早上 8 点开始，在晚上 10 点暂停，那么你可以选择这种方式*

选择**星期一**字段的下拉菜单，选择 **8:00** 开始下拉菜单，选择 **22:00** 暂停资源。

为此，我们使用 wp-cron 本地 WordPress 调度选项来启动和暂停事件。因此，wp-cron 将在上午 8 点访问网站时触发，如果有人访问您的网站，那么资源将自动启动，暂停选项也是如此。

如果你的站点访问者较少，你的站点没有太多的访问者，这将无法正常工作，在这种情况下，你可以看到在你的托管服务器上设置 cron 作业的选项。

您可以在服务器上配置每 30 分钟运行一次的 cron 作业，这将允许更准确的事件发生，如资源启动和暂停将按时发生。

要为 WordPress 配置 cron 作业，请参见:[https://www . easycron . com/cron-job-tutorials/how-to-set-up-cron-job-for-WordPress](https://www.easycron.com/cron-job-tutorials/how-to-set-up-cron-job-for-wordpress)

如果你想监控预定的 cron 事件，你也可以安装:[https://wordpress.org/plugins/wp-crontrol/](https://wordpress.org/plugins/wp-crontrol/)，这个插件显示列出的 cron 事件，你可以在其中控制你的 power bi 资源。

同样，如果你愿意，你也可以设置每个工作日的时间。如果您不想在星期一启动和暂停资源，只需从开始下拉列表中选择**开始**选项，与**暂停**一样，从相应的下拉字段中选择暂停选项。

**7。添加了新的功能来控制 Azure 上配置的 Power BI Azure 资源**

–我们如何实现这个插件来调度资源

为了给 power bi 添加一个可以远程控制资源的新特性，他们提供了一些我们用来控制资源的 REST API 端点。

在他们的博客上，他们提到了更新[https://power bi . Microsoft . com/en-us/blog/power-bi-developer-community-November-update/](https://powerbi.microsoft.com/en-us/blog/power-bi-developer-community-november-update/)

首先，我们添加了一个选项，它是启动和暂停 API 调用时需要的 OAuth 令牌。

它是从:**[https://login.microsoftonline.com/{Azure](https://login.microsoftonline.com/%7BAzure)租户 Id}/oauth2/token**

生成的，在这里我们进行了 POST API 调用，我们已经发布了如下值，如

 `grant_type="client_credentials"
client_id={client id}
client_secret={client secret}
resource="https://management.azure.com/"` 

**注**:客户端 id 和 secret 在主设置部分。

该调用提供了 management.azure.com 资源管理 API 调用中使用的 OAuth 令牌。

所以对于**暂停**资源我们可以拥有:

 `https://management.azure.com/subscriptions/{Enter subscriptionId}/resourceGroups/{Enter resourceGroupName}/providers/Microsoft.PowerBIDedicated/capacities/{Enter dedicatedCapacityName}/suspend?api-version=2017-01-01-preview` 

在这个调用中，我们需要传递上面生成的 OAuth 管理。azure token。

同样的还有**开始**资源:

 `https://management.azure.com/subscriptions/{Enter subscriptionId\}/resourceGroups/{Enter resourceGroupName\}/providers/Microsoft.PowerBIDedicated/capacities/{Enter dedicatedCapacityName\}/resume?api-version=2017-01-01-preview` 

这个调用也需要像上面一样有 OAuth 令牌。

之后，我们添加了一个选项，允许我们从插件设置页面设置管理资源的时间表。

我们已经为所有工作日添加了新的设置，所以管理员可以从 WordPress admin 自动轻松地管理他们的资源。

我们已经想到了最好的解决方案来检查我们可以做些什么来触发自动操作以控制资源，解决方案是因为 WordPress 有一个选项来设置预定事件([https://codex . WordPress . org/Function _ Reference/WP _ schedule _ event](https://codex.wordpress.org/Function_Reference/wp_schedule_event))。

我们已经为启动和暂停资源设置了 cron 事件，为此，我们已经在 WordPress 上设置了站点时区。

注意:为了更精确地控制资源，建议在你的 WordPress 托管服务器上设置 cron 任务。

**结论**:

使用我们的插件很容易自动控制 power bi azure 资源。并且易于配置。现在你不需要手动启动/暂停 Azure portal cool 上的资源。

如果您有任何疑问，请联系我们:[https://upnrunn.com/contact/](https://upnrunn.com/contact/)

帖子[微软 Power BI 嵌入一个 WordPress 插件](https://upnrunn.com/blog/2018/08/microsoft-power-bi-embedded-a-wordpress-plugin/)最早出现在 [upnrunn | Build &部署强大应用](https://upnrunn.com)上。