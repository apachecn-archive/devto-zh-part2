# SSH 代理深度

> 原文：<https://dev.to/samuyi/ssh-agents-in-depth-4116>

[![Figure showing how SSH agent work](../Images/f47537709d074c745878ec95a9a0cf4e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jWh_oT7k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t74c6r06ugqdh1sijxe1.gif)

在之前的[帖子](https://dev.to/samuyi/using-ssh-agent-to-simplify-your-ssh-experience--1in8)中，我们讨论了 ssh 密钥管理的最佳实践，其中涉及到使用 ssh-agent 来存储解密的私有密钥，以实现 SSH 客户端身份验证的自动化。在这篇文章中，我们将深入探讨 SSH 代理是如何工作的，包括它使用中的一些边缘案例。

## 启动代理

SSH 代理有一些令人费解的行为，即使是最有经验的系统管理员也会感到惊讶。运行代理并不只有一种命令语法，每种方法都有相关的注意事项。

有两种方法可以启动代理:

*   单壳法；在单个终端上使用您当前的登录 shell
*   Subshell 方法；派生一个子 shell 来使用一些继承的环境变量

在第一种方法中，代理在您当前的登录 shell 中运行。这是通过运行命令:
来完成的

```
$ ssh-agent 
```

运行上述命令后打印到 shell 的变量需要设置为环境变量。该命令在后台派生 ssh-agent，从终端分离并向您返回提示。请注意，在每次 SSH 会话终止后使用之后，即使您注销，代理也不会被终止，除非您显式终止代理。因此，如果您启动另一个终端会话并再次运行 ssh-agent 和 ssh-add，另一个进程将在后台分叉。很快你就有一系列的代理在后台运行，什么也不做。要避免这种情况，请使用第二种方法。

第二种方法从您当前的 shell 生成一个子 shell。您所需要做的就是提供一个 shell 或 shell 脚本的路径，如下所示:

```
$ ssh-agent  $SHELL 
```

这一次，ssh-agent 在前台运行，生成一个子 shell 并设置必要的环境变量，而不是派生一个后台进程。登录会话的其余部分在子 shell 中运行，当您注销时，代理进程终止。也可以通过运行命令
来终止代理

```
$ ssh-agent -k  #this won’t work if you started ssh-agent with the first approach 
```

一旦代理启动并运行，我们就向它添加密钥，这是通过 ssh-add 命令完成的，该命令将身份文件的路径(私有密钥)作为参数给出。

```
$ssh-add  /home/you/.ssh/id_rsa 
```

ssh-add 会请求您输入一次密码，以加载私钥。有许多命令行选项，包括使用 ssh-add 命令在 ssh-agent 中列出和删除密钥。在您最喜欢的发行版上查找 ssh-add 的手册页，查看命令行选项的完整列表。

[![figure showing how SSH agent forwarding works](../Images/12e8ad1737c9c21b772c2bc51d9711a4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HRB_hcZ0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w7p1geq5kn5bf1ktp74l.gif)

## 代理转发

代理特别有用的一个场景是，假设我想将一个文件从远程服务器 shell.isp.com 复制到另一个远程服务器 other.host.net，这两个服务器上都安装了 SSH，并且它们都使用公钥身份验证，我该如何实现呢？我可以从远程服务器 shell.isp.com 拷贝到我的本地系统，然后从我的系统拷贝到远程服务器 other.host.net，但这是浪费时间。相反，由于我在两个远程服务器上都有一个帐户，我们可以用这样的命令将文件从 shell.isp.com 服务器直接复制到 other.host.net 服务器:

```
$ scp pat@shell.isp.com:print-me psmith@other.host.net:imprime-moi 
```

但是，如果您运行上面的命令，它会失败，因为当它试图运行 scp 来实际复制文件 other.host.net 时，没有 tty 来输入密码以解密 shell.isp.com 主机上的 other.host.net 的私钥。所有这些都假设你有 shell.isp.net other.host.net 的私人钥匙。把你的私人密钥储存在服务器上是一个非常糟糕的主意，一般来说，你的私人密钥存放在越少可以上网的地方就越安全。如果您必须备份您的私钥，请将其保存在一个便携式磁盘上，不要让其他人知道。另一个与开发人员有关的用例是将代码从 github 上的存储库拉到远程服务器。如果存储库是私有的，并且已经为 github 帐户设置了 ssh 访问，那么使用代理会使事情变得容易得多，而不是将 github 私有密钥复制到远程服务器。

幸运的是，ssh-agent 很容易解决上述所有问题。远程 scp 只是联系您本地机器上的 ssh-agent 进行认证，您的私钥永远不会离开您的本地机器，假设您已经将 other.host.net 的私钥加载到 ssh-agent 中。类似地，如果您已经在本地 ssh-agent 上加载了 github 帐户的私钥，那么身份验证将在您的机器上进行。

要使用代理转发，客户端和服务器都必须允许。ssh-agent 打开的套接字负责服务器和客户机之间的通信。环境变量 SSH_AUTH_SOCK 指向套接字，该变量是在代理初始化时创建的，套接字通常存储在/tmp 目录中。要在服务器上启用代理转发，必须在 SSH 服务器配置文件中将变量“AllowAgentForwarding”设置为 yes，同样，也必须在 SSH 客户端配置文件中将变量“ForwardAgent”设置为 yes。

## 结论

SSH 代理是一个非常有用的特性。它们允许自动化并提高系统的安全性。代理转发可以从一台机器跳到另一台机器，因为代理转发关系本质上是可传递的。只要每个中间服务器都启用了代理转发，身份验证将始终在客户端进行。此功能在公司防火墙网络中特别有用，在这种网络中，有一个面向互联网的堡垒服务器阻止对服务器专用网络的访问。使用 SSH 代理转发，如果服务器上安装了 SSH 并启用了转发，则可以到达防火墙后面并访问服务器。

SSH 代理消耗大量的计算资源，所以最好确保没有空闲进程在后台运行。

当使用 SSH 代理从一台机器跳到另一台机器时，每个中间主机都不能受到危害，这一点很重要，因为这会将您的密钥暴露给入侵者。