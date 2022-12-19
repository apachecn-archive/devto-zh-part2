# 在 Windows 上打开 SSH

> 原文：<https://dev.to/anandmukundan/open-ssh-on-windows-2i5h>

一两周前我有一个场景，我想从一台 Mac 连接到一台 Windows 机器，并运行一些 PS 脚本。(关于为什么不使用 PowerShell Core 的讨论将在另一篇文章中进行)。

所以最简单的方法就是做 SSH。我需要连接的 windows 机器是 Win 7 机器，所以没有内置 SSH。所以我只是下载了 powershell 团队已经移植到 Windows 的 Open SSH 并部署它。然后从 Mac 连接，没有问题，一切都很好。

现在我想把它集成到一个脚本中，并且不想输入用户名/密码组合，SSH 有一种方法使用公钥/私钥对来支持这一点。如果您在互联网上搜索，有数百篇文章介绍如何生成密钥对并将公钥复制到服务器以允许客户端连接。问题是我找不到在 SSH 的 Windows 服务器端做什么，因为它使用的目录结构与 UNIX 版本略有不同。

所以一些调试和日志分析发现 Windows 版本使用了。ssh(即 C:\Users\xxx)目录中找到 pub 密钥文件(文件应该命名为 authorized_keys)。唯一的问题是用户目录是通过 SSH 登录的用户。我已经设置了一个本地用户，SSH 脚本将使用该用户登录，我最初试图将文件放入我的用户目录，但它需要放在“尝试登录用户”的用户目录中。授权密钥文件只是一个包含所有公钥的文本文件。因此，如果您有一个以上的公钥，只需附加到您的授权密钥文件。

一旦文件在正确的位置，您仍然需要运行 PS 团队提供的 powershell 脚本来修复主机和用户文件权限(这些可以在您安装 OpenSSH 的目录中找到)。这两种方法都很容易操作，应该能让你很快上手。

[原帖](http://feedproxy.google.com/~r/DotNetFromIndia/~3/lBZOQKtHGvA/open-ssh-on-windows.html)