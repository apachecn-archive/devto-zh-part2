# 如何安装 Prestashop 1.7

> 原文：<https://dev.to/franken/how-to-install-prestashop-17-1gh5>

Prestashop 是最流行的电子商务网站内容管理系统之一。小型企业和大型企业都使用它，并且可以通过数以千计的扩展和主题来增强。全球超过 270，000 个电子商务网站在 PrestaShop 上运行，并提供 65 种不同的语言。

[![How to install Prestashop 1.7 - Prestashop Tutorial](../Images/446252d44d57c6e200dd23095ec26431.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nw11YS4b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7fwzoqdcxwhqptokocmf.jpg)

它相当容易使用，为购物者提供了一个强有力的商店界面，提供了一套全面的功能，而且是免费的。

在本教程中，我们将讨论如何在你的主机/服务器上安装 Prestashop(我总是喜欢在 Ubuntu 服务器上安装 Prestashop 电子商务网站——它是轻量级的，易于使用)。

# 安装 Prestashop 1.7 有哪些准备步骤

```
I. Download Prestashop & Upload to Server
II. Create a Database
III. Run Prestashop 1.7 Installation Wizard
IV. Delete Install Folder & Archive file 
```

Enter fullscreen mode Exit fullscreen mode

请记住，在服务器上安装 Prestashop 比使用云托管更复杂和耗时，但回报更大:更好的性能，更大的灵活性，可以自由调整您认为合适的设置。你永远不必等待云主机支持团队为你更改 PHP 设置。

## 第一步-下载预铺&上传到服务器

从官方网站下载 PrestaShop 包，然后在您的计算机上本地解压缩该包。

为了做到这一点，请使用任何解压软件解压。例如:Winzip、Winrar、7Zip (Windows)或 Keka、Archive Utility (MacOS)...

打开包含 prestashop.zip 文件的解压缩文件夹。通过 cPanel 的文件管理器或使用 FTP 客户端将此文件上传到您的服务器。

请记住，上传的包应该位于 public_html 目录下，这是您的帐户的 web 根文件夹，或者位于它的子目录下。

一旦你上传了档案，在那里解压它。

## 第二步-创建数据库

一旦文件上传到您的服务器，您应该创建一个运行安装向导的数据库。

你可以很容易地从 cPanel -> MySQL 数据库(如果你使用 cPanel)或 Virtualmin ->编辑用户/编辑数据库(如果你使用 Virtualmin)。如果 PhpMyadmin 安装在您的服务器上，使用它创建一个新的数据库。

## 第三步-运行 Prestashop 1.7 安装向导

导航到您的-网站。该向导将引导您完成安装过程。在这一步，你将被要求选择你的商店要使用的语言。

**选择语言**:第一屏是语言设置。选择安装程序的语言，然后单击“下一步”按钮。

这也将为您的 PrestaShop 安装设置默认语言，但您以后也可以启用其他语言。

许可协议(License agreements):PrestaShop 是免费的，在一定的开源许可下发布。阅读条款和条件，并选择同意条款和条件。

(如果您不同意许可条款，您就不能使用本软件，这一步要求您明确承认这些条款)。

**系统兼容性**:快速检查所有服务器参数。如果继续，这意味着系统准备安装 Prestashop 1.7。

**店铺信息**:从哪里开始定制你的店铺:给它起个名字，注明它的主要活动和店主的个人信息。

**系统配置**:还记得第二步吗？用数据库连接信息填充所有字段:数据库服务器地址、名称、登录名、密码、表前缀(表前缀也可以保持不变)。

好了，现在测试您与数据库服务器的连接。

完成后，单击“下一步”按钮继续安装。这可能需要几分钟:请耐心等待，不要触摸您的浏览器。

该向导将创建必要的表并完成商店的设置。您将看到一个类似如下的页面:

## 第四步-删除安装文件夹&的归档文件

出于安全目的，您需要重命名或删除 Prestashop 1.7 安装文件夹& Zip 存档文件中的“install”文件夹。
决赛

哇！PrestaShop 1.7 已成功安装在您的服务器上。

现在，您可以进入新更名的“管理”文件夹，登录 PrestaShop 后台，并通过下载 [Prestashop 模块](https://www.prestasoo.com/prestashop-modules.html?utm_source=devto&utm_medium=article&utm_campaign=install_prestashop)开始修改您的商店，以改进功能&定制主题。

让我们尽情享受吧&别忘了访问我们的博客，查看更多 [Prestashop 教程](https://www.prestasoo.com/Blog/?utm_source=devto&utm_medium=article&utm_campaign=install_prestashop)！

[![Infographic - How to install Prestashop 1.7](../Images/7dfc179c8239d9e630cd4b8e9731beb9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gGJPEbBX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n1qnisabtnctvfjlswoj.jpg)