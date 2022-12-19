# 在 Linode 上设置 Nginx

> 原文：<https://dev.to/ladvien/setting-up-nginx-on-linode-c0l>

我用 Jekyll 创建了我的网站。许多繁重的工作由迈克尔·罗斯以他创造的名为 Hpstr 的 Jekyll 主题的形式完成。

*   [Hpstr](https://mademistakes.com/work/hpstr-jekyll-theme/)

非常尊重。

但是，设置对我来说相当痛苦。我对网站一无所知，更不用说创建一个静态页面的网站了。我决定着手记录我遇到的许多细微差别。试着为某人节省一些时间。或者，出问题的时候给自己留点时间。

这些文章不会出现在 CSS、JavaScript 或 HTML 上。在摆弄了 20 年计算机之后，我仍然不擅长 CSS 和 HTML——不，在这方面有更好的资源。

实际上，我建议花 30 美元参加以下 Udemy 课程。它们是很棒的课程，会让你获得竞争力所需的一切。

*   [用 HTML5 和 CSS3 构建响应迅速的真实世界网站](https://www.udemy.com/design-and-develop-a-killer-website-with-html5-and-css3)
*   [完整的 JavaScript 课程 2018:搭建真实的项目！](https://www.udemy.com/the-complete-javascript-course/)
*   [精通 Linux】](https://www.udemy.com/linux-mastery/)

(注意，一定要让它们打折。第二点，它们经常打折。)

我没有被 Udemy 踢回来，我列出这些课程是因为它们是我参加过的课程，我保证它们是这个指南系列的好课程。

## 1。方向

很多其他文章会推荐在本地建立 Jekyll，把你的网站建设得尽善尽美，然后在你有时间的时候租一台服务器。我*不*建议走这条路线。

在某种程度上，在部署 Jekyll 之前先了解一下它是有意义的。你在学习的时候没有付钱。但是，在本地构建一个 Jekyll 站点，加上所有的附加功能，可能会导致部署它时出现很多问题。是第 5 块宝石还是第 12 块宝石出了问题？不，我发现最好孤注一掷，开始在网上建立网站。

比较工作步骤

| 通用工作流程 | 我的工作流程 |
| --- | --- |
| 本地设置 Jekyll | 获取服务器 |
| 在本地部署网站 | 安装服务器 |
| 改善 | 在服务器上安装 Jekyll |
| 在本地部署网站 | 本地设置 Jekyll |
| 改善 | 将网站部署到服务器 |
| 在本地部署网站 | 改善 |
| 获取服务器 | 将网站部署到服务器 |
| 安装服务器 | 改善 |
| 在服务器上安装 Jekyll | 将网站部署到服务器 |
| 将网站部署到服务器 | 啤酒 |
| 啤酒 | 第二杯啤酒 |

我喜欢我的工作流程的几个原因。

首先，心理上的回报不会发生，直到那些恶心的东西都消失了。设置服务器端是单调乏味的。但是，你的网站必须在你自己的服务器上运行。回报是当你在缅因州的朋友可以访问你的网站时，他们可以看到你建的超赞的网站。

如果你把荣誉和温暖的模糊放在开始，意思是，你在本地部署你的站点并告诉自己它看起来有多棒，它剥夺了你在服务器端设置中跋涉所需的动力。科学！

其次，在您的本地机器和服务器之间有许多不同的变量需要考虑。例如，如果你从一台 Windows 机器上构建 Jekyll 并在 Ubuntu 上运行它，通常会有依赖差异，你必须排除这些差异。最好现在就开始做(见第一点)。

好吧，我说服你了吗？没有吗？那你为什么还在读书？哈！

此外，你需要设置服务器端的一件事是本指南，我没有做。我计划在写这些文章的时候建立一个新的网站，以确保这个指南是相关的。但是如果我错过了什么，我可以在评论中提供帮助。这让我的一天，以节省一些开发时间。

## 2。选择提供的服务器

以前租过服务器吗？我也没有。

这是我的建议表，里面装满了我的意见。

#### a .不要浮华

我不建议用华而不实的名字。例如，GoDaddy、HostGator 等。一般来说，如果他们急于推销，他们可能不是一个可靠的选择。

目前两个可靠的选择是
* [数字海洋](https://www.digitalocean.com/)
* [利诺德](https://www.linode.com/)

#### b .用 Linux 去

哦！用 Linux 吧！

我曾经有一位首席执行官，他强迫我在我们的服务器上使用 Windows。伙计，这是个失败。

首先，Windows 后端在网上没有很好的记录。他们花费更多。免费工具比较少。你知道吗，让我给你介绍一下其他人的咆哮。

*   【Linux 在服务器领域击败 Windows 的五大理由
*   [Linux 基金会发现企业版 Linux 正在以 Windows 为代价发展](https://www.zdnet.com/article/linux-foundation-finds-enterprise-linux-growing-at-windows-expense/)

80%(大约 2014 年)的服务器部署使用 Linux 是有原因的，只是“说说而已”。

#### c .走小规模

如果您选择 Digital Ocean 或 Linode，它们都有合理的启动服务器，可以进行扩展。这意味着，您可以在以后为额外的服务器资源支付更多费用，而不必完全重建您的服务器。

好吧！在这篇文章中，我将使用 Linode。我喜欢他们。他们是我的合作伙伴，对他们的质量和可靠性非常满意。

## 3。获取服务器

前往

里诺德

和`Sign Up`

[![](../Images/0bda3f9d1e824d44f9e2a212a1c0155d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nm7P0qWp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.com/images/sign_up_lindode.png)

登录，然后转到`Add Linode`。在这里选择尽可能小的尺寸。我刚开始的时候，小型服务器每月 5 美元，但现在看起来价格上涨了。我的猜测是，你可以偶尔在打折时找到它们。

[![](../Images/6d6ea2cb1a1828bb77d66eea478fce8e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--k9gAqPCk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.com/images/smallest_linode_server.png)

你不必选择最小的——但我认为这对一个 Jekyll 博客来说已经足够了。

一旦您选择了服务器的大小，滚动到底部，并选择一个位置中心，你的观众。如果没有，那就选择离你最近的地方。

然后选择`Add this Linode!`

[![](../Images/db052841c0cf1ecba642336aef82bd0a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--R0F5-J1g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.com/images/set_location_add_linode.png)

一旦您添加了您的 Linode，您将被重定向到您的 Li node 仪表盘

注意，`IP Address`是您的第一台服务器的 IP 地址！哇哦！

[![](../Images/43d2ba55cd93fa2f56a9b4af2e9dc7e4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vRAKvxt3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.com/images/linode_dashboard.png)

这需要一点时间，但是你的 linode 的状态应该从`Being created`变为`Brand new`，当它发生时，你就可以开始玩了！

## 6。安装 Linux

让我们在您的机器上安装 Linux。点击你的名字。

[![](../Images/39590e0a3663af363083a2e23254a476.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--s3nnLiP4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.com/images/your_linode_name.png)

这将为您的服务器*加载服务器仪表板。看起来像这样。*

[![](../Images/28158ebc4a4b1faffad06938ccc4ac73.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Rsa2tvyP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.com/images/your_server_dashboard.png)

不要惊慌。这里发生了很多事情，但是我们会一步一步来。别担心，我抓住你了。

首先，让我们告诉管理您的服务器的计算机在其上安装 Linux。您可以通过前往`Deploy an Image`来完成此操作

[![](../Images/7f0bf058597dd3aa03d340c3b461e237.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1vwYAnZ1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.com/images/deploy_image.png)

#### **当心叶的死记硬背！**

> stackscript 是一个 Linux 脚本，适用于新安装了 Linux 的机器。该脚本告诉机器做一些自动设置工作，让机器为特定任务做好准备。在我们的例子中，准备我们的机器作为服务器。在本演练中，我不打算展示如何使用它们。有几个原因。我们将学习更多自己设置的东西，因此，将能够维护它。此外，我还没有找到一个栈是专门为哲基尔。他们中的大多数都有很多我们不需要的额外的东西。

好了，继续工作。让我们填写我们的设置请求

[![](../Images/799efb92e9a1ae3f6b5194cf15cef540.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lBHyeoGR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.com/images/install_linux.png)

请务必将您的密码保存在某个地方！没有很多方法可以恢复。一旦一切都被选中，点击`Deploy`

你的服务器将很快被格式化并安装一个全新的 Ubuntu 16.04 LTS 版本。哦，我还没有提到

## 5。嘘

SSH 代表安全外壳访问。Shell 是 Linux 所基于的命令提示符环境。这将是我们与服务器交互的主要方式。这可能感觉简洁和不人道，但是我强烈建议您使用命令行。如果你这样做了，Linux 的力量将会免费属于你。

此外，我正在围绕它写这个教程，所以你一定要坚持下去。

好，让我们启动你的机器。打开 Linode 仪表盘，点击你的 linode 的名字。在右上方有一个名为`Server Status`的框，它可能是`Powered Off`。让我们按下`Boot`按钮打开它。

[![](../Images/fd5afd7f59be48fd0451698a8dc29b0f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0vd2hCI9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.com/images/boot_up_server.png)

等到下面的状态显示你的 linode 已经完全启动。

现在，我假设您使用 Linux 或 Mac 作为本地操作系统。在任一种情况下，打开终端并键入

```
ssh root@your.ip.number.here 
```

Enter fullscreen mode Exit fullscreen mode

然后按回车键。

您应该会看到类似
的内容

```
[ladvien@ladvien ladvien.github.io]$ ssh root@your.ip.number.here
The authenticity of host 'your.ip.number.here (your.ip.number.here)' can't be established.
ECDSA key fingerprint is SHA256:ee2BPBSeaZAFbVdpWFj1oHLxdPdGoxCaSRl3lu6u2Fc.
Are you sure you want to continue connecting (yes/no)? 
```

Enter fullscreen mode Exit fullscreen mode

键入`yes`并按回车键。

然后，系统会提示您在 Linode Manager 的设置阶段输入密码`root password`。

## 6\. Nginx Setup

你现在在你的服务器上。你觉得自己有点像机器人先生吗？活出感觉。不要让任何人因为你是个菜鸟而为难你。拥抱贝壳。

我不打算详细讨论 Linux 的东西。请参考更深入的教程。网上到处都是。但是，我要指出的是，`Tab`键是自动完成的。这是在 shell 中工作的一个最重要的花絮。不必键入一个长文件名，只需键入前两个字母，然后按 tab 键。它会试着帮你填满它。

让我们开始我们的服务器设置。

你的服务器只是一台电脑。但是，我们将在你的计算机上安装一个程序，它将使任何人在浏览器中访问你的 IP 地址时看到你的文件系统的一部分。访问者的浏览器从您的文件系统中加载信息，如果文件使用浏览器理解的语言，则为访问者呈现这些信息。这些文件将是由 Jekyll 制作的 HTML 和 CSS 格式。

好的。我们将使用的服务器程序叫做`nginx`。它不是最古老也不是最常见的。但是我发现它的使用非常简单，而且看起来也非常快。

但首先，让我们更新 Linux 系统。在服务器的命令行中键入。

```
sudo apt-get update 
```

Enter fullscreen mode Exit fullscreen mode

然后回车。这将导致所有存储库链接被更新。存储库链接是互联网地址的库，告诉你的计算机什么时候可以找到免费的东西！Linux 上的一切都是赃物。

在开始安装`nginx`之前，让我们花点时间检查一下。打开任何一个浏览器，在浏览器地址栏输入你的地址，然后回车。很有可能，什么都不会发生。浏览器正在尝试与您的服务器联系，但是您的服务器上没有安装任何程序来*为浏览器提供*网站服务。这就是`nginx`要做的。

现在就来下载`nginx`吧

```
sudo apt-get install nginx 
```

Enter fullscreen mode Exit fullscreen mode

它会问你是否要安装`nginx`说是。

一旦安装完毕，让我们测试一下，确保它能正常工作。

类型

```
nginx 
```

Enter fullscreen mode Exit fullscreen mode

它应该用
来响应

```
nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address already in use)
nginx: [emerg] bind() to [::]:80 failed (98: Address already in use)
nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address already in use)
nginx: [emerg] bind() to [::]:80 failed (98: Address already in use)
nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address already in use)
nginx: [emerg] bind() to [::]:80 failed (98: Address already in use)
nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address already in use)
nginx: [emerg] bind() to [::]:80 failed (98: Address already in use)
nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address already in use)
nginx: [emerg] bind() to [::]:80 failed (98: Address already in use)
nginx: [emerg] still could not bind() 
```

Enter fullscreen mode Exit fullscreen mode

太好了！这意味着它已经安装并正在工作。我们只需要设置`nginx`在我们的服务器地址而不是`0.0.0.0:80`上提供我们的文件。

此外，打开浏览器，再次输入服务器的 IP 地址。按回车键。这一次你应该看到:

[![](../Images/4dc6c2c4ee67c5d7e57598ea6004acfb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kDS85i9g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.com/images/welcome_nginx.png)

哇，你现在是在为世界服务，为任何访问你网站的人服务。很酷，是吧？我也这么认为

想看些很酷的东西吗？

式(注意，*不包括*这里的`sudo`)

```
nano /var/www/html/index.nginx-debian.html 
```

Enter fullscreen mode Exit fullscreen mode

您应该看到由`nginx`提供的 html 文件的内容。

```
<!DOCTYPE html>
<html>
<head>
Welcome to nginx!
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>
<p>For online documentation and support please refer to
[nginx.org](http://nginx.org/).Commercial support is available at
[nginx.org](http://nginx.org/).</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

改变

```
<h1>Welcome to nginx!</h1> 
```

Enter fullscreen mode Exit fullscreen mode

至

```
<h1>Welcome to the Jungle, baby!</h1> 
```

Enter fullscreen mode Exit fullscreen mode

然后点击`CTRL + O`，这应该会保存文件。然后点击`CTRL + X`退出`nano`编辑器。

现在，切换回你的浏览器，回到你的网站的 IP 地址，点击刷新。你应该看看。

[![](../Images/b9b647485ee42c96c680eaa80e761ac3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8_HF0KcY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.com/images/welcome_t0_nginx_jungle.png)

没看到吗？你没把`<h1>`换成``吧？问我怎么知道的...

太棒了。让我们继续设置 Nginx，这样您就可以为自己的网站提供服务了。

Linode 实际上有一个很棒的关于设置 Nginx 的演练。

*   [Nginx Setup](https://www.linode.com/docs/web-servers/nginx/how-to-configure-nginx/)

但是，现在，我们将坚持基本的`nginx`设置。在本系列的其他文章中，我将展示如何编辑`nginx`以使网站变得更好。

## 7。吉基尔博士

让我们在本地设置 Jekyll。为了利用 Jekyll，我们需要下载并安装以下程序。

#### 红宝石

[Ruby](https://en.wikipedia.org/wiki/Ruby_(programming_language)) 是一个编程环境，它包含一个我们会经常用到的叫做`[gem](https://en.wikipedia.org/wiki/RubyGems)`的包管理器。例如，当我们键入`gem install cool-program`时，是`ruby`环境从互联网上下载`cool-program`并安装到您的机器上。

#### 捆绑者

Bundler 是一个程序，它帮助将运行一个程序所需的所有依赖项拉在一起。正如他们在自述文件中所说，“Bundler 确保 Ruby 应用程序在每台机器上运行相同的代码。”

#### 去吧

[Git](https://en.wikipedia.org/wiki/Git) 是版本控制程序。它也有能力将源代码拉离网络。我们打算先用它来制作一个离线主题，但最终，我们会用它来管理你的网站 Jekyll 源代码。

#### 家酿(仅限 Mac)

[家酿](https://brew.sh/)，通常被称为 sa Brew，是一个类似于 Linux 的`apt`程序。它是一个命令行工具，可以让你从互联网上下载程序并安装在本地。

好，我们走吧

在本地计算机的终端键入:

#### Linux

```
sudo apt-get install ruby
gem install jekyll 
```

Enter fullscreen mode Exit fullscreen mode

#### Mac

为了在 Mac 上正确设置 Ruby，我们将为 Mac 安装一个名为 [brewed](https://brew.sh/) 的命令行包管理器。这相当于 Linux 中的`apt`。

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew install ruby
gem install jekyll
gem install bundler 
```

Enter fullscreen mode Exit fullscreen mode

## 8。吃一份 Jekyll 开胃菜

Jekyll 非常适合创建网站，但是有很多样板文件。我发现克隆别人的 Jekyll starter 网站要比自己从头开始做容易得多。

*   [杰基尔主题](http://jekyllthemes.org/)

在这个系列中，我们将使用 [Neo-HPSTR](http://aronbordin.com/neo-hpstr-jekyll-theme/) 主题。

打开终端，选择一个目录，你想把你的网站的副本。对我来说，我是 Linux，将使用主目录。

现在，让我们下载我们的主题。

```
git clone https://github.com/aron-bordin/neo-hpstr-jekyll-theme 
```

Enter fullscreen mode Exit fullscreen mode

Git 从互联网上克隆了`neo-HPSTR`主题，并将其放在一个名为`/neo-hpstr-jekyll-theme`的目录中，您可以随意将该目录重命名为您网站的名称。例如，我的目录名为`ladvien.com`,我们即将把这个网站上线，只需要再多几个步骤。

## 9。构建 Jekyll 主题

打开你的网站目录

```
cd neo-hpstr-jekyll-theme 
```

Enter fullscreen mode Exit fullscreen mode

并输入

```
bundler install 
```

Enter fullscreen mode Exit fullscreen mode

这将拉所有需要的程序，使这个主题建立在您的计算机上。请注意，您可能需要输入密码才能访问文件。

好了，关键时刻到了。类型

```
bundle exec jekyll build 
```

Enter fullscreen mode Exit fullscreen mode

您应该会看到类似于
的响应

```
Configuration file: /home/ladvien/neo-hpstr-jekyll-theme/_config.yml
       Deprecation: The 'gems' configuration option has been renamed to 'plugins'. Please update your config file accordingly.
            Source: /home/ladvien/neo-hpstr-jekyll-theme
       Destination: /home/ladvien/neo-hpstr-jekyll-theme/_site
 Incremental build: disabled. Enable with --incremental
      Generating...
                    done in 1.103 seconds.
 Auto-regeneration: disabled. Use --watch to enable. 
```

Enter fullscreen mode Exit fullscreen mode

但是，如果你没有得到任何错误，你应该很好。

分解一下，我们使用`bundler`程序来执行`jekyll`程序。我们将`build`命令传递给`jekyll`程序，它告诉`jekyll`获取你所有的 jekyll 文件并编译到你的网站上。`bundler`程序确保`jekyll`拥有正确编译所需的一切。

在您的文件浏览器中，导航到您的网站目录并进入`_site`目录。这个目录包含编译后的整个网站。

[![jekyll_site_folder](../Images/a58a586dcfb6341772a699eccc240e43.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MGX4036t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.com/images/the_site_folder.png)

打开这个文件夹，然后双击文件`index.html`。这将在浏览器中打开您的网站。

[![jekyll_site_locally](../Images/8326ba637e65a158b4d2464f608873a3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lT-QYvtg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.com/images/local_jekyll.png)

但这不是我们想要的。让我们把它放到我们设置的网络服务器上。

打开命令提示符，将目录切换到您网站的主目录。然后，键入

```
scp -r _site/*  root@your.website.ip.address:/var/www/html/ 
```

Enter fullscreen mode Exit fullscreen mode

这将会把所有编译好的网站文件复制到你的网站上。转到你的网站地址，你应该看到网站在线！Booyah！

## 10。就这样吗？

这是最基本的设置。以下是我计划在本系列中解决的问题。

*   编辑`_config.yml`文件来定制你的主题
*   在 Github 上设置您的代码
*   添加`SSL`加密
*   在将资产发送给观众之前，调整服务器以压缩资产
*   让服务器更加安全——这叫做加固
*   创建一个自动编译 Jekyll 的脚本，发送给`Github`，然后将编译好的文件复制到你的网站。