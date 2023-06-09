# 将 PaperCSS 与 ASP.NET 核心 MVC 2.1 结合使用—循序渐进

> 原文：<https://dev.to/andruhovski/using-papercss-with-aspnet-core-mvc-21--step-by-step-8k3>

## [T1】简介](#intro)

在这篇文章中，我想分享我使用 PaperCSS 框架的经验。我还想看看新的图书馆管理员在 VS 2017 中是如何工作的。PaperCSS 是一个将页面呈现为纸片的框架。正如作者所说，PaperCSS 的目标是在添加类时尽可能的小。
要了解更多关于 PaperCSS 的信息，请关注[官方网站](https://www.getpapercss.com/)。

UPD:你可以在这里找到这篇文章的第二部分。

## 先决条件

您应该已经安装了 Visual Studio 2017 v.15.8.7 和。NET Core 2.1 框架。你还需要一个像 Faker.Net 一样的假数据生成器。

## 使用 PaperCSS 创建 Web 应用程序的步骤

### 第一步:创建 ASP.NET 核心 MVC 项目

首先，你必须创建一个核心 MVC 项目。按照下面的步骤创建项目。

1.  推出 Visual Studio 2017
2.  进入**文件**菜单，选择**新建** - > **项目**；
3.  在**新建项目**的左侧面板中，展开已安装- >模板->Visual c#->Web；
4.  从中间窗格中选择 ASP.NET 核心 Web 应用程序。输入项目名称(PaperCss)并选择位置。然后按“确定”按钮；
5.  在**新 ASP.NET 核心 Web 应用**窗口中，确保选择顶部的**ASP.NET 核心 2.1**；
6.  对于这个演示，我选择 Web 应用程序(MVC)的**无认证**和**配置为 HTTPS** 。

[![New Project Screen](img/ce84622f0c918195b0efb329c7affe2d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--s0WJtVGV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8i0zr8gt4e10dv9c87gh.png)

### 第二步:替换 CSS 框架。

由于 ASP.NET 核心 Web 应用程序模板的当前版本没有更新到新版本，我们需要删除`lib`文件夹的内容并创建一个新的。我们应该这样做有两个原因:旧内容是用 Bower 调优的，我们需要将 Bootstrap 替换为 PaperCSS。

1.  转到`lib`文件夹，删除除`favicon.ico`以外的所有内容；
2.  右键点击`lib`，选择**添加**->-**客户端库**；
3.  在`Library`文本框中输入`jquery@3.3.1`并点击`Install`
4.  右击`lib`，再次选择**添加**->T3】客户端库；
5.  选择提供者`unpkg`并用`papercss@1.5.4`将`Library`文本框归档
6.  因为我们只需要 PaperCSS 中的`dist`文件夹，我们选择`Choose specific folder`选项，然后选中树中的`dist`文件夹，点击`Install`。

### 第三步:修改 _Layout.cs

现在我们可以将 PaperCSS 应用到我们的应用程序中。在这一步，我们将改变`Views/Shared/_Layout.cs`中的一个导航栏。

1.  将第 7 行和第 11 行中的链接替换为**papers cs**: