# 让我们使用 Salesforce 商务云进行开发—第 2 部分:创建开发店面

> 原文：<https://dev.to/pxjohnny/let-s-develop-with-salesforce-commerce-cloud-part-2-creating-a-development-storefront-2pig>

<figure>[![](img/9cd1fada29c660fdbf5abc948ce86c4f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--m7Da-usF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A96XUr6cf5MGomjZLfSvHfA.jpeg) 

<figcaption>照片由[迈克·彼得鲁奇](https://unsplash.com/photos/c9FQyqIECds?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上传 [Unsplash](https://unsplash.com/search/photos/online-shopping?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

错过了第一部分？在这里赶上！

在“让我们与 Salesforce Commerce Cloud 一起发展”的这一部分中，我们将设置一个店面。店面基本上是 SFCC 用于电子商务网站的术语，但它也可以指任何应用程序，无论是本地移动应用程序还是内部员工福利门户。店面是 SFCC 开发的一个基本概念，因为你为平台开发的一切最终都托管在店面上。

### 设置站点生成

为了帮助我们快速入门，SFCC 为我们提供了一个名为 *SiteGenesis* 的完整的电子商务示例网站，其中包括各种示例对象，如 *ISML 模板*、*控制器*和*脚本。*

要安装 SiteGenesis storefront:

1.  在 Eclipse 中，进入**文件** > **新建** > **其他** *。*
2.  选择 *SiteGenesis 店面*，点击*下一步*。
3.  输入店面的根名称和位置，并选择将 SiteGenesis 附加到哪个数字服务器。点击*完成*。

[![](img/9bb8098127a0cb3f70de691e8764a3e5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0iw-It3M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/640/1%2Aq7o0xyNPoG3p9rVyt-jOLg.gif)

完成后，您将在您的*项目浏览器中看到一些新对象:*

*   一个名为 **_core** 的文件夹——包含网站的所有核心元素，如:图片、页面、css 文件等。
*   一个名为 **_controllers** 的文件夹——包含所有包含站点逻辑的 JavaScript 控制器。
*   名为 **_pipelines** 的文件夹—与控制器相同，但使用传统管道而不是 JavaScript。

❤️ **请注意** :

这些文件夹本质上是*墨盒*。卡带是商业云的一个基本概念，用于打包和部署代码。在第 3 部分中，我们将构建我们自己的插件，所以花一点时间来看看这些文件夹的内容以及它们是如何组织的。

新的墨盒现在将被上传到业务经理网站。然而，为了使用它们，我们必须首先注册它们。

### 注册 SiteGenesis 墨盒

通过 Business Manager 注册墨盒，只需将墨盒名称添加到已用墨盒列表中。

注意列表的重要性顺序是很重要的。与您预期的相反，重要性的顺序是从左到右，这意味着左边的盒式磁带优先于路径末端的盒式磁带。

要在您的站点上注册核心墨盒:

1.  在业务管理器中点击**管理>站点>管理站点**。
2.  点击**设置**选项卡。
3.  在**墨盒**下，将 **_core** 作为第一项添加到墨盒列表中。
4.  将 **_controllers** 添加到列表中(用冒号分隔)以添加 JavaScript 控制器功能，或者添加 **_pipelines** 以添加管道功能。点击**应用**保存更改。

对于控制器，最终结果应该是 **_core:_controllers** ，对于流水线，最终结果应该是 **_core:_pipelines** 。

[![](img/660cc0ae7594edc97f6e680e2f747795.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FP9OBw4F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/640/1%2AdU4caitmgR27jJooomez8Q.gif)

### 查看站点生成

现在，我们的墨盒已上传并注册，我们准备好了！前往您的网站 url，如果一切顺利，您应该会看到以下内容:

<figure>[![](img/8a4bf4d11ef268cc255666278caf89e1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Fd5v8hYN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AhNWPybpHaboZ_4rg-n7qtA.png)

<figcaption>site genesis in action</figcaption>

</figure>

现在你知道了。一个成熟的电子商务网站运行在 SFCC 等待我们的定制墨盒🎉。

在第 3 部分中，我们将开发一个定制的插件——您可以通过它向您的站点添加定制的业务逻辑和功能。

要了解有关 SFCC 及其功能的更多信息，请参考位于 https://documentation.demandware.com/DOC2/index.jsp

[的官方文档](https://documentation.demandware.com/DOC2/index.jsp)