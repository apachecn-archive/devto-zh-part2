# 如何安装 Prestashop 主题

> 原文：<https://dev.to/franken/how-to-install-prestashop-theme-3fo3>

[![](img/52dbc8d2c393b5bb8e625bad027a9f9b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_tEyWH9d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.prestasoo.cimg/stories/how-to-install-prestashop-theme.jpg)

然而，在你开始你的电子商务网站 Prestashop 之前，你必须知道如何安装 Prestashop，然后设置你的主题。

如今，拥有一个专业外观的网站对任何类型的企业都至关重要。你需要一个真正好的主题，因为它是你在这个无尽的数字市场上最伟大的大使。

如果它看起来不专业，不像样，人们就会避免与它互动。

本教程的目标是向您展示使用示例数据安装 Prestashop 主题所需的一切。

Prestashop 允许您为从 Prestashop 构建的商店选择不同的外观。作为初学者，你可能听说过大量免费和付费的 Prestashop 主题。你已经找到了你梦想中的网页设计，而且恰好是一个主题。

# 你会了解到

```
I. Download Theme Package to Local PC
II. Import Theme into Website
III. Activate Prestashop Theme
IV. Install Sample Data 
```

Enter fullscreen mode Exit fullscreen mode

## 第一步-下载主题包到本地电脑

首先你需要下载主题安装包。通常主题会附带安装指南，并提供几种安装方式(手动、通过插件等)，所以在开始使用主题之前，请务必查看指南。

然后，提取下载的文件。

## 第二步——将主题导入网站

```
Log into PrestaShop admin panel and go to Design -> Theme and Logo.
You would be redirected to the Theme management page. Here you need to click Add New Theme button. 
```

Enter fullscreen mode Exit fullscreen mode

从你的电脑导入:这是一个简单的方法-只需浏览你在第一步下载并解压的文件夹中的主题包，然后点击保存按钮将你的主题导入到网站中。

从 FTP 导入:这是一条艰难的道路。我们为什么需要它？默认情况下，PHP 允许最大上传 2MB 的文件，你可以找到一个简单的轻量级主题，但让我们面对它:它们是大文件。这就是为什么你不应该在主机上部署你的电子商务网站——一切都是有限的！

你可能想让你的主机提供商配置你的主机来上传一个大文件。你需要等待几个小时(或几天)才能得到答案。太疯狂了，是吧？让我们使用 KVM VPS，它只是每月 5-10 美元。今天，KVM VPS 定价比昨天低。

你在哪里找到一个好的供应商？我爱 Linode，Digitalocean，Vultr...他们是一些很好的供应商，你可以相信你的业务。这是我的建议。这不是广告。这里没有附属链接。

请记住，如果您的网站运行在托管或弱服务器上，您不能使用我们的 Prestashop 云备份模块来计划自动备份您的网站。

你知道当你在等待主机提供商的回答时的那种感觉。

不能再等了？现在，使用 FTP 客户端上传主题包到你的网站/主题。

一旦这样做了，回到设计->主题和标志->添加一个新的主题。在这里，您可能想从“选择存档”的可用主题列表中选择新上传的主题，然后按“保存”按钮。

恭喜你！主题现在已经安装好了！现在，激活主题作为默认主题。

## 第三步-激活预铺主题

现在回到设计>主题和徽标页面，找到新安装的主题。悬停主题缩略图并点击“使用此主题”来激活默认主题的主题。

## 第四步-安装样本数据

转储文件是存储所有主题设置的数据库文件。通常它被导入到数据库中，使网站看起来像你购买的主题的演示。

将 dump.sql 文件导入数据库。您应该看到以下消息:导入已成功完成，XXX 查询已执行(dump.sql)。

安装已成功完成。刷新您的 Prestashop 网站以享受结果，并开始下载 [Prestashop 模块](https://www.prestasoo.com/prestashop-modules.html?utm_source=devto&utm_medium=article&utm_campaign=install_theme)以改进您的网站功能并促进您的销售。

别忘了访问我们的博客，了解更多 [Prestashop 教程](https://www.prestasoo.com/Blog/?utm_source=devto&utm_medium=article&utm_campaign=install_theme)。

[![How to install Prestashop Theme - Infographic](img/31615fb28bea494c5b60ffc8dbebcaf7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lh28geFb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.prestasoo.cimg/stories/how-to-install-prestashop-theme-infographic.jpg)