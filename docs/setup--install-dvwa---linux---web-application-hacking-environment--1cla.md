# 设置和安装 DVWA -创建您的道德黑客环境

> 原文：<https://dev.to/sinxloud/setup--install-dvwa---linux---web-application-hacking-environment--1cla>

在本教程中，我将向您展示如何在本地主机上安装易受攻击的 Web 应用程序(DVWA)以及 Apache、MySQL 和 PHP。新手总是关心他们应该在哪里练习和探索漏洞。如果你是其中的一员，DVWA 会让你自己去发现。我将帮助你在你的 Linux 发行版中创建一个黑客环境来练习和测试你的技能。

如果你是网络安全新手，想在网络安全领域领先。我建议您阅读'[Kickstart Your Web Application Security Career](https://sinxloud.com/beginner-web-security-researcher/)'，您将从所提供的技巧和资源中受益匪浅。

[![DVWA Beginner](img/78e9b1189dc9decf7b2823dfdfe1a5cf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MhEL75IW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://sinxloud.com/wp-content/uploads/2018/08/DVWA-website-Screenshot-1024x491.png.webp)

**DVWA 中涵盖的攻击**

*   蛮力
*   命令执行
*   CSRF
*   文件包含
*   SQL 注入
*   SQL 注入(盲)
*   Shell 上传
*   XSS(反射)
*   XSS(已存储)

**先决条件:**

**虚拟机** : VirtualBox

Linux 发行版:Kali Linux、Parrot Security OS 或 elementaryOS(或任何其他 Linux 发行版)

我更喜欢使用 elementaryOS，它是基于轻量级 Linux 的发行版，但是你可以遵循 Kali Linux 的相同说明，它旨在进行高级渗透测试和安全审计。请记住，我们需要使用虚拟机，而不是连接的服务器，因为 DVWA 非常容易受到攻击，只能安装在带有 NAT 的虚拟机上。话虽如此，让我们开始吧。

## 安装&将 DVWA 安装到您的 Linux 发行版中

DVWA 是用 PHP 和 MySQL 为安全专业人士或有抱负的安全专业人士制作的，以发现尽可能多的问题，并利用 web 平台的一些最常见的漏洞，如 SQL 注入、跨站脚本(XSS)、跨站请求伪造(CSRF)等。

### 第一步。安装 Web 服务器(安装 Apache)

*   要安装 Apache，请打开您的终端并键入以下内容:

`sudo apt install apache2`

完成后，在浏览器中键入 127.0.0.1，您将看到默认的 Apache 2 网页，如下所示:

[![Apache 2 Installation](img/1e3dfcdc34bdaba76aa89904489b49da.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Vwujgihc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://sinxloud.com/wp-content/uploads/2018/08/Apache2.png)

查看完该测试页面后，您可以通过键入以下命令来删除它:

`sudo rm /var/www/html/info.html`

### 第二步。下载 DVWA

我们需要从 Github 下载 DVWA 的档案。

*   要安装 Git，请键入以下命令:

`sudo apt-get install git`

*   转到 apache2 文件夹。

`cd /var/www/html/`

*   从 Github 克隆 DVWA，键入以下命令:

`sudo git clone https://github.com/ethicalhack3r/DVWA.git`

完成后，在浏览器中键入 127.0.0.1/DVWA/

[![DVWA Installation](img/86b783134f8ee5993a12d75da6f40a5f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RSNbtGUu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://sinxloud.com/wp-content/uploads/2018/08/DVWA-Apache-Page--1024x538.png.webp)

*   更改 DVWA 的权限

`sudo chmod -R 777 /var/www/html/DVWA/`

### 第三步。安装 MySQL

设置 DVWA 的下一步是安装 MySQL。

*   要安装 MySQL，请键入以下内容:

`sudo apt install mysql-server`

请注意，安装程序可能会要求您为 MySQL root 用户创建一个新密码。一旦您完成了所有必需的步骤，您的 MySQL 安装就应该完成了。让我们仔细检查一下我们新的 MySQL 服务器是否正在运行。键入以下命令:

`mysql -u root -p`

输入您在安装软件包时为 MySQL 创建的 root 密码。进入后，通过下面的内容可以获得服务器状态、版本信息和更多信息:

`status`

这是确保您已经安装了 MySQL 并准备好进一步配置的好方法。

*   重新启动 Apache 服务器

`sudo service apache2 restart`

- **创建数据库和用户**

要创建 MySQL 数据库和用户，请按照以下步骤操作:

在命令行中，键入以下内容:

`mysql -u root -p`

键入 MySQL root 密码，然后按 Enter 键。

要创建数据库，请键入以下命令:

`CREATE DATABASE dvwadb;`

要创建数据库用户，请键入以下命令。将 dvwausr 替换为您想要创建的用户，并将 dvwa@123 替换为该用户的密码:

`CREATE USER 'dvwausr'@'127.0.0.1' IDENTIFIED BY 'dvwar@123';`

授予权限，请键入以下命令:

`GRANT ALL PRIVILEGES ON dvwadb.* TO 'dvwausr'@'localhost' IDENTIFIED BY 'dvwa@123';`

完成后，通过键入以下命令之一退出应用程序:

`\q`(或)`exit`

### 第四步。安装 PHP5

对于 DVWA 安装中的最后一个组件，我们将设置并安装 PHP。在你的虚拟机上安装这个很容易。

*   要安装 PHP，只需输入以下命令:

`sudo apt install php5`或`sudo apt install php5.6`

同意安装，PHP 5 将安装在您的服务器上。

*   重新启动 Apache 服务器

`sudo service apache2 restart`

现在，让我们花一点时间来测试您刚刚安装的 PHP 软件。移动到您的公共 web 目录:

`cd /var/www/html`

在那里，通过键入以下命令，使用文本编辑器创建一个名为 info.php 的文件:

`sudo vim info.php`

这个命令将使用命令行编辑器 vim 打开一个新的同名的空白文件。在该文件中，键入以下内容:

*   在该文件中，复制粘贴以下内容:

`<?php phpinfo(); ?>`

*   通过输入以下内容保存您的更改:

`:wq!`

完成后，打开 web 浏览器并在浏览器中键入您的本地主机 IP 地址。

(示例 IP 地址)[http://127 . 0 . 0 . 1/info . PHP](http://127.0.0.1/info.php)

您将看到默认的 PHP 信息页面，如下所示:

[![PHP Installation](img/fef6037a3251cdf9b3b36a4a5e96d275.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CDPqpJyO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://sinxloud.com/wp-content/uploads/2018/08/PHP-Information-Page.png)

查看完这个测试 PHP 页面后，如果需要，您可以通过键入以下命令来删除该文件:

`sudo rm /var/www/html/info.php`

*   安装 PHP 的 MySQL 扩展。

要安装 MySQL Extension for PHP 支持，请键入以下内容:

`sudo apt install php5-mysql`

一旦完成，您就完成了 DVWA 所需的 PHP 安装。

*   安装 PHP-GD

DVWA 需要一个 PHP 模块，但该模块没有安装到 Kali Linux 或 elementaryOS 中。所以我们需要为 APT 添加一个 Debian 源码。

`sudo add-apt-repository 'http://ftp.de.debian.org/debian sid main'`

`sudo apt update`

`sudo apt install php5-gd`

一旦完成，您就完成了 DVWA 的 PHP 安装。

### Step5。配置 DVWA

现在，我们准备编辑 PHP 配置文件的源代码，以确保您的 web 应用程序连接到数据库，并拥有一个有效的验证码。您可以通过点击此处从您的 Google 帐户获取 reCaptcha 密钥。

我们将使用文本编辑器编辑配置，键入以下命令:

`sudo vim /var/www/html/dvwa/config/config.inc.php.dist`

添加 MySQL 数据库的数据库名称、用户和密码。

**输入 reCaptcha 键。**

这里有一个截屏，显示了你的文件在编辑后的样子。

[![PHP Installation](img/e624989ead757458844e9ed333822643.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uXitXGvJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://sinxloud.com/wp-content/uploads/2018/08/Edit-DVWA-Config-File-1024x591.png.webp)

完成后，我们需要编辑 apache2 的主配置(php.ini)文件，默认情况下，它不会被 DVWA 正确覆盖。

`sudo vim /etc/php5/apache2/php.ini`

*   启用允许 _url_fopen
*   启用允许 url 包含

这是利用文件上传漏洞所必需的。下面是 php.ini 修改后的截图。

[![Jump to line 821 in php.ini](img/0cb5d82355a1434c8ee896075c963d0f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--B5jVOJll--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://sinxloud.com/wp-content/uploads/2018/08/Edit-Php.ini-for-DVWA-1024x645.png.webp)

保存对 php.ini 的更改后，我们需要遵循更多的步骤。

*   安装 Iceweasel

`sudo apt install iceweasel`

*   重启 Apache

`sudo /etc/init.d/apache2 restart`

*   重启 MySQL 服务

`sudo /etc/init.d/mysql restart`

完成后，您就完成了 DVWA 所需的配置。

*   测试 DVWA 安装

`iceweasel http://127.0.0.1/DVWA/setup.php`

[![Test DVWA Installation](img/a42b08c0cc9ee7debf81628ff5052944.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hQE9GByq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://sinxloud.com/wp-content/uploads/2018/08/Setup-DVWA-1024x834.png.webp)

您将被重定向到网络浏览器，类似的页面将出现在您面前。

当你看完这个 DVWA 设置页面，你可以点击创建/重置数据库按钮。您将被重定向到登录页面。

*   使用 MySQL 用户和密码登录

[![DVWA Login](img/978a6b33f248555c74e28833a591ee7b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--J4EOydYz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://sinxloud.com/wp-content/uploads/2018/08/DVWA-Install-Complete-Login-Page-1024x485.png.webp)

现在，通过点击“DVWA 安全”来登录以改变漏洞的强度。

#### 针对攻击的 DVWA 安全选项:

*   低级别的:低级别的安全性给了你利用所有已知漏洞的自由，意味着在一个给定的框架中没有安全性，因此如果你是第一次使用它，你可以尝试所有的攻击。

*   中级:中级安全将有所有入门级的验证和过滤，可以阻止任何脚本小子利用现有的漏洞。

*   高水平:高水平是一种零日环境，如果你能突破它，那么这意味着你在成为 VAPT 专家的正确道路上。

**从低级开始。**

##### 大功告成。🍻

因此，我们在本地主机上设置了一个简单的易受攻击的 web 应用程序。您现在可以探索 DVWA 界面。

如果您在安装过程中遇到任何错误或有任何疑问，请在下面的评论中告诉我们！

##### 快乐黑客！