# 使用代客运行 TYPO3

> 原文：<https://dev.to/smichaelsen/running-typo3-with-valet-4ocm>

> 这篇文章描述了我在 Mac 上开发 TYPO3 的经历。如果您在另一个操作系统上工作，您可能会发现这篇文章有帮助，也可能没有。

有很多方法可以为 TYPO3 创建一个本地开发环境，并且都声称设置起来超级简单快捷——这就是 Valet 赢得我的原因。

我已经开发 TYPO3 网站和扩展超过 12 年了，我声称已经使用或至少尝试了 TYPO3 的所有可能的开发设置(可能不完全正确-试试我)。

我用了很长时间的一个选项是流浪者盒子，我用 [PuPHPet](https://puphpet.com/) 配置了它。在那之后，其他基于虚拟机的解决方案接踵而至，包括 docker 和 ddev。我不喜欢在容器中运行我的 TYPO3 环境的地方是命令行访问。是的——所有这些解决方案都允许您 ssh 到容器中，做任何您想做的事情。但我不想那样。我想在我的本地机器上运行`composer install`，并通过编写脚本刷新 TYPO3 缓存或设置扩展[——例如。我总是对这种东西感到头疼。](https://github.com/TYPO3-Console/composer-auto-commands)

然后我学习了如何在多个版本中安装、维护和运行 [PHP 和 MySQL with homebrew](https://getgrav.org) ，并意识到我不一定需要 Apache 或 nginx for TYPO3，但[内置的 PHP 服务器](http://php.net/manual/en/features.commandline.webserver.php)对大多数用例都很好。

但是内置服务器有一个主要的限制。它一次只能处理一个请求，这至少有两个负面后果:

*   加载包含许多资产和 AJAX 请求(比如 TYPO3 后端)的页面很费时间，因为请求不是同时处理的。
*   有时并行请求是必要的，例如当您试图在 TYPO3 后端为 solr 索引页面内容时——后端请求向 TYPO3 前端发出一个请求，由于已经有一个活动的后端请求而停止(我最终找到了一个解决方法)。

在忽略了两三个提示后，本周我终于尝试了[的代客服务](https://github.com/laravel/valet)，当我得知它可以与我已经有的 brew PHP / MySQL 兼容时，我立刻被吸引住了。它带来的好处是 nginx 服务器在没有任何配置的情况下也能令人惊讶地工作。你只需要告诉代客一个项目的根文件夹，它就会为你的项目创建虚拟主机。`~/projects/acme/`自动成为可用的 [http://acme.test](http://acme.test) 。每个项目需要零配置。如果你喜欢别的东西，TLD `.test`可以重新配置。一句话:我花了几分钟就安装好了代客服务，并运行了我当前的项目。

它带有检测和运行包括 TYPO3 在内的许多 PHP 框架和系统的驱动程序。唯一的警告:它假设您的 TYPO3 项目中的文档根文件夹名为`web`。这并不是我所有项目的情况。我比较近的用的是`public`。我能够通过将这个自定义驱动程序放在`~/.valet/Drivers/Typo3PublicFolderValetDriver.php` :
来快速解决这个问题

```
<?php

class Typo3PublicFolderValetDriver extends Typo3ValetDriver
{
    protected $documentRoot = '/public';
} 
```

Enter fullscreen mode Exit fullscreen mode

感谢 [@dakira](https://dev.to/dakira) 在 twitter 上给了我[至关重要的提示](https://twitter.com/dakira/status/1027135773953482752)。