# 你如何部署东西？你用的工具是什么？

> 原文：<https://dev.to/mnivoliez/how-do-you-deploy-stuff-and-what-is-the-tool-you-use-for-4g2e>

你好！我想就如何部署以及为此使用的工具展开讨论。

作为背景，我曾在一家公司工作，该公司使用“人工制品”回购来存储 CI 产生的人工制品。然后，部署服务器被告知有一个新的人工制品，他们得到了它们。就个人而言，我使用 gitlab runner 通过 rsync 命令来部署我的博客(但老实说，这感觉很脏，但很有效)。我也知道“git push as deploy”，但我也不相信，因为你不想要完整的回购协议，而只是一个打包解决方案。

那么你如何部署东西呢？