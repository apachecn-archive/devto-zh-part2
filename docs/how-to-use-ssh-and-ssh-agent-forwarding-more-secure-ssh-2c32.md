# SSH 代理转发如何正确使用 SSH，什么是 SSH 代理转发

> 原文：<https://dev.to/levivm/how-to-use-ssh-and-ssh-agent-forwarding-more-secure-ssh-2c32>

SSH(安全外壳)被广泛用于提供对远程系统的安全访问，我们没有多少方法可以做到这一点。基本上，每个使用它的人都知道常规的密码访问。但是，这种方法存在一些问题，主要是它允许暴力破解密码猜测。

SSH 提供了更好的认证过程。我们将回顾 ssh 的基本方法，并查看更多高级选项，以便能够在外部服务器中使用我们的 SSH 密钥，而无需将它们直接放在那里。

第一种方法是在本地机器和远程机器之间使用私有/公共密钥。

### 使用 SSH 公钥访问

众所周知，如果我们尝试使用基本的 SSH 连接到服务器。它会提示交互式 shell 询问我们密码。为了避免每次我们想要连接时服务器提示输入密码。我们创建一对公钥和私钥。

1.  启动密钥生成程序

```
$ ssh-keygen -o -a 100 -t ed25519 
```

Enter fullscreen mode Exit fullscreen mode

1.  输入保存密钥的文件的路径。

```
$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key(/home/johndoe/.ssh/id_rsa): 
```

Enter fullscreen mode Exit fullscreen mode

您应该输入保存密钥的文件的路径，默认情况下是您的`.ssh`目录中的`id_rsa`。

这将创建两个文件，一个私钥和一个公钥。公钥的名称后面会追加`.pub`。

1.  输入使用您的密钥的密码。

这是用于加密您的密钥的密码。它将被请求进行进一步的连接。您可以将其留空，但强烈建议**不要填写**。

```
Enter passphrase(empty for no passphrase):  <Type the passphrase> 
```

Enter fullscreen mode Exit fullscreen mode

1.  重新输入您的密码

```
Enter same passphrase again: <Type the passphrase>
Your identification has been saved in /home/johndoe/.ssh/id_rsa.
Your public key has been saved in /home/johndoe/.ssh/id_rsa.pub.
The key fingerprint is:
0e:fb:3d:57:71:73:bf:58:b8:eb:f3:a3:aa:df:e0:d1 johndoe@myLocalHost 
```

Enter fullscreen mode Exit fullscreen mode

1.  核实结果

在您选择的目录中应该有两个密钥。如果一切顺利，我们准备使用我们的钥匙。

1.  复制远程主机上的公钥。

我们需要在我们的远程主机上添加我们的公钥`id_rsa`，路径是`$HOME/.ssh/authorized_keys`

1.  使用我们的密钥通过 SSH 连接

```
ssh -i ~/.ssh/id_rsa user@our_host_ip 
```

Enter fullscreen mode Exit fullscreen mode

### SSH 代理

我们已经知道如何使用密钥来通过安全 Shell 进行连接，但是，有一个问题，它需要在每次连接时使用一个秘密密码来解锁私钥。

为了避免这种情况，我们需要使用`ssh-agent`，这是一个在后台运行并将您的密钥存储在内存中的程序。

1.  启用 ssh 代理

```
# start the ssh-agent in the background
$ eval "$(ssh-agent -s)"
Agent pid 69599 
```

Enter fullscreen mode Exit fullscreen mode

1.  将 SSH 密钥添加到 ssh-agent

```
$ ssh-add ~/.ssh/id_rsa 
```

Enter fullscreen mode Exit fullscreen mode

如果您用不同的方式命名您的密钥，您需要用您的密钥名替换`id_rsa`。

现在您可以连接到主机，它将只要求密码一次。您的下一次连接将是无密码的。

```
ssh -i ~/.ssh/id_rsa user@our_host_ip 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下 ssh 使用 id_rsa 密钥，所以我们可以避免使用`-i ~/.ssh/id_rsa`所以，我们可以使用

```
ssh user@our_host_ip 
```

Enter fullscreen mode Exit fullscreen mode

### SSH 代理转发

前面的方法看起来是完美的解决方案，但是如果我们想要连接到任何需要 ssh 验证的服务，会发生什么呢？

比方说，我们需要使用 github 密钥在远程主机中提取一个 repo。我们需要在主机上重复前面的过程。如果任何人授权访问我们的主机，这将是危险的，因为他可以检索我们的私钥，并可以访问私有配置的服务。

为了避免前面的场景，我们将使用我们最后的资源:SSH 代理转发。

> SSH 代理转发可用于简化服务器部署。它允许您使用本地 SSH 密钥，而不是留下密钥(没有密码短语！)坐在你的服务器上。

让我们使用 github 作为远程服务来配置和测试 SSH 转发，以将我们的代码拉入主机。

#### 设置 SSH 代理转发

请记住，我们需要已经拥有与任何 github repo 相关联的密钥。

那么，让我们来配置:

1.  在`~/.ssh/config`创建或打开文件
2.  输入以下文本，用我们的服务器域名或 IP 替换 myhost.com

```
Host myhost.com
  ForwardAgent yes 
```

Enter fullscreen mode Exit fullscreen mode

存档它有一个快捷方式，如果我们不想创建一个配置文件，我们有另一个选择，在`ssh`命令中使用`-A`标志。

```
ssh -A user@myhost.com 
```

Enter fullscreen mode Exit fullscreen mode

> -启用身份验证代理连接转发的选项。
> 
> 这意味着，它将您的 SSH 验证模式转发到远程主机。因此，您可以在那里使用 SSH，就像在本地机器上一样。

#### 测试 SSH 代理转发

为了测试我们的代理转发是否工作正常，让我们通过 ssh 连接到我们的远程主机并进行测试。

```
$ ssh app@myhost.com
app@debian:~$ ssh -T git@github.com
Hi levivm! You've successfully authenticated, but GitHub does not provide shell access. 
```

Enter fullscreen mode Exit fullscreen mode

它工作了。现在，我们可以拉/推任何我们有权访问的 github 回购。

如果有什么问题，请告诉我。

注意:你不转发密钥本身，你转发的是代理，所以基本上，你想加多少密钥都可以。以防您的 github repo 使用不同的密钥。

你可以在这里查看如何做
[https://super user . com/questions/1140830/ssh-agent-forwarding-using-different-username-and-different-keys](https://superuser.com/questions/1140830/ssh-agent-forwarding-using-different-usernames-and-different-keys)

> 如果你喜欢我的内容或者对你有帮助，你可以通过[请我喝咖啡](https://www.buymeacoffee.com/R3i9IQJ)来激励我写更多的内容