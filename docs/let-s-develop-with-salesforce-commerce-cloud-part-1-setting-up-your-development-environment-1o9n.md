# 让我们利用 Salesforce 商务云进行开发—第 1 部分:设置您的开发环境

> 原文：<https://dev.to/pxjohnny/let-s-develop-with-salesforce-commerce-cloud-part-1-setting-up-your-development-environment-1o9n>

<figure>[![](img/c750f90d3b6c633bc6e62a0712368c1f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--km0iwwv9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AZQC-3_5tfCIDjPteKi40yw.jpeg) 

<figcaption>感谢[https://unsplash.com/@igormiske](https://unsplash.com/@igormiske)！</figcaption>

T9】</figure>

已经有软件了？直奔[第二部](https://medium.com/@fsjohnny/lets-develop-with-salesforce-commerce-cloud-part-2-creating-a-development-storefront-720cef540413)！

最近我有机会从事(并获得认证🎊 🎉Salesforce 的电子商务平台——sales force 商务云(简称 SFCC)。我几乎立即注意到的一件事是缺乏该平台的社区文档，因此我决定记录我开发 cartridge 的道路——这是商业云数字开发架构的一个基本概念。

首先，让我们设置我们将在整个开发过程中使用的开发环境。

### 第一步:安装必要的软件

**月食**

**爪哇 JDK 8**

从[http://www . Oracle . com/tech network/Java/javase/downloads/JDK 8-downloads-2133151 . html](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)下载并安装 Java 8 JDK

**UX 工作室**

Salesforce Commerce Cloud 的开发和调试是通过一个名为 UX 工作室的 Eclipse 插件完成的。

安装 UX 工作室:

1.  在 Eclipse 中，选择*帮助* > *安装新软件*。
2.  点击*添加*。这将打开*添加存储库*对话框。
3.  输入以下详细信息:

*   **名称** : UX 工作室(或者任何你真正喜欢的东西)
*   **地点**:[https://developer . sales force . com/media/commerce cloud/UX studio/4.6](https://developer.salesforce.com/media/commercecloud/uxstudio/4.6)

1.  选中 *Salesforce Commerce Cloud* 旁边的框，然后单击*下一步。*

2.  继续完成*安装细节*对话框，直到要求您重启 Eclipse。恭喜您，您现在离开始墨盒开发又近了一步！

下面的 GIF 展示了整个安装过程:

[![](img/4db3baa1510d47e80a31330acc21bc75.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uVbZCqiq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/640/1%2ALR6x1ZR8Vt_3M_XM-AWYAg.gif)

### 第二步:连接到你的沙盒服务器

现在，您已经安装了所有必要的软件，让我们连接到我们的沙盒服务器。

1.  点击*窗口* > *透视图* > *打开透视图* > *其他*，选择*数字开发*，将 Eclipse 更改为*数字开发*透视图。
2.  点击*文件* > *新建* > *数字服务器连接*打开新建服务器连接对话框。
3.  在*新建数字服务器连接*对话框中，填写服务器*主机名*、*用户名*(与您的业务经理用户名相同)和*密码*(与您的业务经理密码相同)字段。
4.  完成后，点击*下一步。*

[![](img/42a6d7b5c75a05f265783b6643be45b3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DrkOwVJk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ANN4S5tYjwInbd7lRPzNtQw.png)

1.  在*代码暂存目录*对话框中，选择*版本 1* 为*目标版本目录*，点击*完成。*

[![](img/2502289c9b99c2ddb8636d4a846d1899.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RxmRvkHv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Afx-at44Bd0JI5sFwMei6Lw.png)

1.  此时将开始下载数字 API(您可以在 Eclipse 的右下角看到进度)。等待它完成。

恭喜🎉！现在，您已经拥有了一个完全适用于 SFCC 的开发环境。

在第 2 部分中，我们将使用我们的环境来创建一个店面(SFCC 用于在线商务体验/网站的术语),它将作为我们开发的墨盒的主机。

要了解有关 SFCC 及其功能的更多信息，请参考位于 https://documentation.demandware.com/DOC2/index.jsp

[的官方文档](https://documentation.demandware.com/DOC2/index.jsp)