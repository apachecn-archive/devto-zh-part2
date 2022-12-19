# 在 Chromebook、Termux 和 Neovim 上开发 WordPress

> 原文：<https://dev.to/tnolte/wordpress-development-on-a-chromebook-termux-neovim-1a3p>

<figure>[![Neovim - Editing WordPress Plugin File](../Images/34586e41ca7a5484d7a3f419d3c5d79a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8mZk-Xzx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/www.timnolte.com/wp-content/uploads/sites/4/2018/06/Screenshot-2018-06-13-at-12.10.49-AM-1024x521.png%3Fresize%3D720%252C366%26ssl%3D1)

<figcaption>Neovim——编辑 WordPress 插件文件</figcaption>

</figure>

所以大约 6 个月前，我决定对我的个人发展环境做一些改变。大约从 2013 年开始，我一直使用一台 Chromebook 作为我的主要机器。我第一次买了一台宏碁 C710 给我妻子使用，但与我们的 Macbook Pro 相比，它对她来说是一次令人沮丧和不合格的体验，更不用说她是一个重度 Adobe 套件用户。由于共享 Macbook Pro 具有挑战性，我开始将 Chromebook 用作我的开发机器。最初我选择了 T2 面包丁。拥有一个完整的 Linux 环境和 Chrome 操作系统是一件非常美好的事情。然后我运行了一个完整的[灯](https://en.wikipedia.org/wiki/LAMP_(software_bundle))环境，用 [PhpStorm](https://www.jetbrains.com/phpstorm/) 作为我的 IDE。这工作正常，我有时有性能和稳定性问题。为了解决一些性能问题，并屏蔽 realestate，我拿起了一台[东芝 Chromebook 2](https://www.google.com/intl/en_us/chromebook/find/toshiba-chromebook-2/) ，并再次从一个油炸面包丁环境开始。在不得不几次重建我的油炸面包丁环境，并且仍然经历随机破碎的油炸面包丁的东西之后，我决定转移到基于 web 的 IDE。

<figure>[![CodeAnywhere - Web-based IDE](../Images/fad98a9b9fea0276b31aadb1b94694a0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---WbaKW4i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/www.timnolte.com/wp-content/uploads/sites/4/2018/06/Screenshot-2018-06-13-at-12.22.12-AM-1024x524.png%3Fresize%3D720%252C368%26ssl%3D1)

<figcaption>CodeAnywhere–基于网络的 IDE</figcaption>

</figure>

我尝试了各种基于网络的 ide，包括[cloud 9](http://c9.io)&[CodeAnywhere](https://codeanywhere.com/)，最终在很长一段时间内选择了 code anywhere。在那段时间里，我实际上拿起了一台[惠普 Chromebook G1](https://store.hp.com/us/en/pdp/hp-chromebook-13-g1-(energy-star)-p-w0t00ut-aba--1) ，这是我在 [Sprint、](https://www.sprint.com/)时用来远程控制我的工作虚拟桌面的，这是一台非常坚固的机器。至于 CodeAnywhere，我实际上支付了一个多年的计划，但在看到功能上几乎没有进展，以及当事情不工作时严重缺乏客户服务后，我开始寻找替代方案。我在考虑回到油炸面包丁，但不喜欢把我的 Chromebook 放回开发者模式。我确实检查了 [Codenvy](https://codenvy.com/) ，这是一个托管版本的 [Eclipse Che](https://www.eclipse.org/che/) 一种基于 web 的 Eclipse 版本。大约在那个时候，我的 Chromebook 正式支持 Android 应用程序。

[![Development Setup](../Images/a9a8c20e36e86a27c2e957f3bf88dba1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aVv6BPU---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/www.timnolte.com/wp-content/uploads/sites/4/2018/01/IMG_20180123_151543032_LL-1024x576.jpg%3Fresize%3D720%252C405%26ssl%3D1)

当我在 Chromebook 上进行全面开发时，我再次查看了这个场景，我看到了一篇有趣的文章。这个概念是使用 Chrome OS 上的 Android 环境和应用程序 [Termux](https://termux.com/) 作为开发环境。这让我更加想做一点复古，使用 [vim](https://www.vim.org/) ，更确切地说是 [Neovim](https://neovim.io/) 作为我的 IDE。

因为我最近刚刚恢复了我的 Neovim 设置，时间会证明这是否是我的长期解决方案。我最近刚刚完成了一个 WooCommerce 插件的一些改动，我需要为一个客户网站做这些改动。一旦翻译文件被更新，我希望我的请求能被接受，我的修改能被包含在官方插件中。另外，我求助于使用 GitHub 更新插件来安装我的版本，以及我的修改分支，直到我的修改正式发布。今年六月晚些时候，我将在[世界大急流城](https://2018.grandrapids.wordcamp.org/)的[演讲](https://2018.grandrapids.wordcamp.org/session/wordpress-devops/)中深入探讨这个问题。

Chromebook 上的帖子 [WordPress Development，& Neovim](https://www.ndigitals.com/articles/wordpress-development-on-a-chromebook-termux-neovim/) 最先出现在 [Nolte Digital Solutions](https://www.ndigitals.com) 上。