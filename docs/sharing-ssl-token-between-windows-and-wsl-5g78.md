# 在 Windows 和 WSL 之间共享 SSL 令牌

> 原文：<https://dev.to/nordyj/sharing-ssl-token-between-windows-and-wsl-5g78>

随着 WSL(用于 Linux 的 Windows 子系统)的引入，Windows 用户现在可以随意在 cmd.exe、PowerShell 和 Bash 之间来回切换(当然也可以完全不切换)。也就是说，我选择使用 Bash shell 作为 VSCode 中的终端。

这一切都很好……直到您需要将代码提交到远程 git 存储库。我将我的 github 帐户设置为使用 SSH 令牌进行身份验证，并且最初在 Windows 端通过 git-bash 设置了令牌。现在，我想在 Bash 中使用相同的 SSH 令牌。由于 Linux 世界和 Windows 世界大部分是用 WSL 相互隔离的，我需要弄清楚如何让它们与相同的令牌对话。

我试着从窗户上复制令牌。ssh 文件夹到 Linux。ssh 文件夹中，但由于令牌的签名方式，这并不起作用。我还尝试在 Linux 环境中创建一个全新的令牌，但是也没有成功。当我试图从远程存储库推/拉代码时，它不起作用。

我绝不认为自己是 Linux 专家。我知道如何使用 **ls** 命令，以及 **nano** 文本编辑器。句号。所以我需要我们的朋友谷歌的一点帮助。这是我用来让这一切工作的过程。

首先，我想分享。Windows 和 Linux 之间的 ssh 令牌。因为在一天结束时，Linux 运行时是在 Windows 文件夹层次结构中的一组文件夹中运行的，所以这应该不是问题。所以，我创造了一个象征性的链接。在 Linux 上。ssh 文件夹通常直接位于根目录下:~/.ssh。ssh_old(所以我没有丢失已经存在的任何东西)，然后运行以下命令来创建符号链接:

**ln-s/mnt/c/Users/{ my username }/。ssh ~/。ssh**
T5】我在这里找到了做这个的资料:[https://baptiste-wi cht . com/posts/2012/09/Linux-symbol-links-hard-links . html](https://baptiste-wicht.com/posts/2012/09/linux-symbolic-links-hard-links.html)

现在，当我打开 bash 并尝试执行 git pull 时，我得到了类似下面的错误:

```
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@ WARNING: UNPROTECTED PRIVATE KEY FILE! @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
Permissions 0777 for 'privkey.pem' are too open.
It is required that your private key files are NOT accessible by others.
This private key will be ignored.
Load key "privkey.pem": bad permissions
Permission denied (publickey). 
```

Enter fullscreen mode Exit fullscreen mode

发生这种情况是因为 Windows 不支持(见图)Linux 权限，Bash 认为 SSH 令牌上的权限过于开放，使其不安全。它们默认为 777，我相信这意味着“任何人都可以在任何时间任何地点对这个文件做任何事情”。我在一对搜索结果中找到了这个问题的答案。

首先，您需要“打开”WSL 跟踪 Linux 风格权限的能力。您可以通过创建/etc/wsl.conf 文件并添加一个自动挂载设置来实现，如下所述:[https://superuser.com/a/1343737](https://superuser.com/a/1343737)。其次，您需要将 SSH 令牌的权限设置为 600，如下所述:【https://superuser.com/a/215506.】T2 为此，将 cd 放入~/。ssh 目录，然后运行下面的命令: **chmod 600 id_rsa** 。这将使用适当的 Linux 权限保护令牌。

这项工作的好处是，现在无论您是在 cmd.exe、PowerShell 还是 Bash 中运行，都可以使用相同的 SSH 令牌。我对此进行了测试，并且能够从所有这 3 个环境中进行 git 拉取。

不过，我一直在学习。如果你有更好的方法，请在评论中告诉我，我会相应地调整我的策略！