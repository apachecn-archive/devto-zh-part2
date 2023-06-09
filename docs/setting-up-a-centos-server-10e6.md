# 设置 CentOS 服务器

> 原文：<https://dev.to/rpalo/setting-up-a-centos-server-10e6>

我慢慢发现我喜欢系统的东西。我一直在我的服务器上使用 Ubuntu 作为 Linux 的版本，但我对 Ubuntu 和 CentOS 之间的差异很好奇，所以我查阅了一些指南和教程，并用新安装的 CentOS 制作了一个新的[数字海洋水滴](https://m.do.co/c/2e87eb578ad9)。我想分享我所学到的东西，以及第一次设置时需要考虑的所有事情。我写这个也是为了当未来的我忘记了一个步骤，记不起命令。

我假设您现在已经掌握了命令行的工作知识:更改目录、编辑文件和设置文件权限。我会试着解释比这更奇特的东西。如果你还没有完全做到，但你仍然想学习，请[联系](https://assertnotmagic.com/about/)，我很乐意帮你完成(和/或为此写另一篇文章)。这也是 CentOS 的一个指南，因为那就是我正在做的。因此，所有的命令都是特定于 Centos(可能还有 RHEL)的。不过，这个过程和理论对于其他版本的 Linux 应该是一样的。

## 初次接触

我不知道其他提供商是如何做到的，但一旦你在数字海洋上创建了一个 droplet，并且你的新服务器全部打开，他们就会给你发送一封带有`root`密码的电子邮件。您将能够使用这些凭证`ssh`进入您的服务器。如果你在 Mac 电脑上，你已经安装了`ssh`，并且可以通过你选择的终端应用程序访问。如果你使用的是 Windows，你应该研究一下`ssh`客户端。需要的时候我会用[油灰](https://www.chiark.greenend.org.uk/~sgtatham/putty/)。你的主机提供商也会给你提供一个 IP 地址。

```
ssh root@<your_server_ip> 
```

Enter fullscreen mode Exit fullscreen mode

从现在开始，如果你看到像这样的尖括号中的东西，就假设我的意思是，“用你自己喜欢的值填充尖括号中的占位符。”它会要求你输入密码。粘贴并输入，你就可以开始摇滚了！

## 检查您的权限

现在你以`root`的身份登录。这太棒了！这也是不好的。当你以 root 用户身份登录时，输入错误和小错误会导致大问题。一般来说，最好登录一个特权较少的账户，只在需要的时候调用`sudo`来做特权的事情。这样，如果你不小心试图将自己从存在中删除，你将不得不在成功之前多努力一点。因此，我们需要创建这个日常用户。

```
adduser <username> && passwd <username> 
```

Enter fullscreen mode Exit fullscreen mode

这将要求您为新用户输入新密码。做一个好的。

接下来，让我们确保您的新用户实际上可以`sudo`。

```
usermod -aG wheel <username> 
```

Enter fullscreen mode Exit fullscreen mode

我们正在将我们的新用户添加到`wheel`组，这意味着(只要你在 CentOS 7 或更高版本上)我们将能够顺利地`sudo`。

最后，有时会创建一个默认用户:`centos`。我不认为我们需要这个用户做任何事情。用
删除它

```
deluser centos 
```

Enter fullscreen mode Exit fullscreen mode

## 转移按键

接下来，我们将通过设置私钥/公钥身份验证来增强安全性。这样做的另一个好处是，如果你不想再记住密码，你就不需要记住了。临时切换用户，以便您作为新用户操作。

```
su <username> 
```

Enter fullscreen mode Exit fullscreen mode

为了使用密钥进行身份验证，您需要一个地方来存放您的公钥。让我们创建`.ssh`目录。

```
mkdir ~/.ssh 
```

Enter fullscreen mode Exit fullscreen mode

我们也不希望除了我们之外的任何人能够随意摆弄这个目录。

```
sudo chmod 700 ~/.ssh/ 
```

Enter fullscreen mode Exit fullscreen mode

太好了！我们来调一把钥匙。退出`ssh`或者在你的本地机器上打开一个新的终端。如果您还没有私钥/公钥对，现在就生成一个。

```
ssh-keygen

# Generating public/private rsa key pair.
# Enter file in which to save the key (/Users/localuser/.ssh/id_rsa): 
```

Enter fullscreen mode Exit fullscreen mode

只需按回车键接受默认的文件位置为您的密钥。这将创建两个文件。`id_rsa`是您的私钥。这是*非常秘密的*。永远不要和任何人分享这个，除非你把生命托付给他们。或者至少是您的服务器。我甚至会说，不要把它放在云服务或闪存驱动器上，因为它可能会被黑客攻击、窃取、丢失或炸毁。`id_rsa.pub`是你的公钥，这是一个你可以与人分享，以证明你是你所说的人。这是我们希望与新服务器的新用户共享的文件。有几种方法可以做到这一点:

```
# Via ssh-copy-id
ssh-copy-id <username>@<server-ip>

# Via scp
scp ~/.ssh/id_rsa.pub <username>@<server-ip>:~/.ssh/authorized_keys

# Manually via good ole' fashioned copy/paste
cat ~/.ssh/id_rsa.pub

# You'll see something like:
# ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAmLmwkzQDjEOW1Rj3TP5NldVDqUODVH9xuYrkeaSkxtdP

# Copy the whole thing.  Then ssh back into your server like normal and create a new file at ~/.ssh/authorized_keys and paste in in.
nano ~/.ssh/authorized_keys

# Or use emacs or vim or whatever editor you like best.  Whatever.  Shut up. 
```

Enter fullscreen mode Exit fullscreen mode

现在，下次你以你的用户身份登录时，它不会要求你输入密码。

同样，确保该文件的权限尽可能严格。

```
# Once again logged into your server
sudo chmod 600 ~/.ssh/authorized_keys 
```

Enter fullscreen mode Exit fullscreen mode

## 但是我们可以走得更安全

*“但是等等，难道我们不应该也为我们的`root`用户制作一个公钥，这样我们就不用输入密码了吗？”*你问。答案是否定的。实际上，最安全的方式是如果你的`root`用户甚至根本不能从外部登录！我们现在要编辑我们的`ssh`守护进程(简称为`sshd`)的配置，它控制我们的服务器如何接受`ssh`连接。大多数配置位于`/etc`目录中，这也不例外。

```
sudo nano /etc/ssh/sshd_config 
```

Enter fullscreen mode Exit fullscreen mode

寻找这样的一行:

```
#PermitRootLogin yes 
```

Enter fullscreen mode Exit fullscreen mode

您需要取消注释，并将其设置为“no”。

```
PermitRootLogin no 
```

Enter fullscreen mode Exit fullscreen mode

*旁注:我一直觉得多加一行很搞笑。*

```
# PermitKennyLogin DANGER ZONE! 
```

Enter fullscreen mode Exit fullscreen mode

我的妻子是一名教师，并不热衷于 sshd 配置，但她不同意。

作为额外的步骤，还要修改以下几行:

```
PasswordAuthentication no

# If you connect via IPv4: AddressFamily inet
# If you connect via IPv6: AddressFamily inet6 
```

Enter fullscreen mode Exit fullscreen mode

第一行关闭所有密码登录。如果没有这个，你的用户帐户仍然可以用密码登录，即使没有你的私钥，别人也可以这样做。其他线路通过拒绝为那些不像你这样沟通的人服务，减少了你必须处理的恶作剧的数量。如果你不打算通过 IPv6 连接，为什么要让一些僵尸网络四处窥探呢？

最后，一旦我们重新配置了一个服务，我们需要重新加载它。

```
sudo systemctl reload sshd 
```

Enter fullscreen mode Exit fullscreen mode

我们应该可以出发了！

## 迁入并定居下来

我们已经基本完成了安保工作。现在，你应该很安全，感觉很安全。现在，我们将专注于将这台服务器变成一个愉快的工作场所。

```
yum update && sudo yum upgrade 
```

Enter fullscreen mode Exit fullscreen mode

看着你的服务器自己更新。

### ProTip: Yum 错误

如果你被打断，或者，假设，你的狗跳到你的腿上，在这种情况下，只是设法捣碎正确的密钥来中止升级而不清理，并且你开始看到像“yum lock”或“sqlite3 database lock”这样的错误，不要惊慌。

查看是否仍有一个`yum`进程处于活动状态。

```
ps aux | grep yum 
```

Enter fullscreen mode Exit fullscreen mode

如果您看到一个不应该处于活动状态的进程，请尝试终止它(注意上述命令输出的第二列中的进程 ID (PID))。

```
kill <pid>
# Or, if you're feeling feisty and it's not working:
kill -9 <pid> 
```

Enter fullscreen mode Exit fullscreen mode

Willy 很抱歉他导致了 90 分钟的疯狂搜索错误信息。

[![Sad Willy](img/14b410a76e78018c785e9689e9f1d7ce.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--caTAiw0q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://assertnotmagic.com/img/sad-willy.jpg)

### 回吧

现在是安装任何你不能没有的软件包的好时机:Zsh、oh-my-zsh、Ruby、Vim 和 git 是首先想到的。我只给你看第一个，因为你可能会遇到一个小问题。如果你喜欢鱼或其他贝壳，它应该很可能是相似的。

## 安装 Zsh

第一部分应该说得通。

```
sudo yum install zsh 
```

Enter fullscreen mode Exit fullscreen mode

查看可执行文件的位置。

```
where zsh
# /usr/bin/zsh 
```

Enter fullscreen mode Exit fullscreen mode

这里重要的是确保这个位置在您的`/etc/shells`文件中，这是一个批准的 shells 列表。

```
sudo nano /etc/shells 
```

Enter fullscreen mode Exit fullscreen mode

```
# List of acceptable shells for chpass(1).
# Ftpd will not allow users to connect who are not using
# one of these shells. 
/bin/bash
/bin/csh
/bin/ksh
/bin/sh
/bin/tcsh
/bin/zsh
/usr/local/bin/pwsh
/usr/bin/zsh # << Here's the one we're adding. 
```

Enter fullscreen mode Exit fullscreen mode

一旦 zsh 可执行文件被批准，您就可以将自己的默认 shell 设置为 zsh。

```
chsh -s $(where zsh) 
```

Enter fullscreen mode Exit fullscreen mode

## 奖励:带入点文件

如果你和我一样，你可能会为你慢慢增加的“点文件”而感到自豪。但是你如何快速、健康、可重复地将它们从你的电脑上传到服务器上呢？用 Git。我发现了尼古拉·保鲁奇的这篇文章，我认为它非常精彩。我们将在本地主目录中使用修改过的 bare Git 存储库！

### 设置

在您的本地机器上:

```
git init --bare $HOME/.dotfiles
alias dot='/usr/bin/git --git-dir=$HOME/.dotfiles --work-tree=$HOME'
dot config --local status.showUntrackedFiles no
echo alias dot='/usr/bin/git --git-dir=$HOME/.dotfiles --work-tree=$HOME' >> ~/.bashrc 
```

Enter fullscreen mode Exit fullscreen mode

我们在主目录中创建一个裸 repo 来跟踪我们的点文件，我们创建了`dot`命令(或者你想叫它什么都行),它的功能就像`git`命令一样，但是只是针对我们的点文件。我们将它配置为只显示我们跟踪的文件是否改变，然后我们保存`dot`命令以备后用。

现在，我们要做的就是开始跟踪一些点文件！

```
dot status
dot add .zshrc
dot commit -m "Add zshrc"
dot remote add origin https://github.com/<you>/dotfiles.git
dot push -u origin master 
```

Enter fullscreen mode Exit fullscreen mode

您将获得 Git 的所有好处！您可以分支、编辑、回滚、区分更改等等。

### 安装到我们的服务器上

在服务器上，将相同的`dot`命令添加到`.zshrc`文件中。

```
echo alias dot='/usr/bin/git --git-dir=$HOME/.dotfiles --work-tree=$HOME' >> ~/.zshrc
. ~/.zshrc 
```

Enter fullscreen mode Exit fullscreen mode

如果有任何库存文件可能与您要导入的点文件冲突，要么删除它们，要么(更好)将它们复制到备份目录中。

```
mkdir .dotfile-backup
mv .bashrc .dotfile-backup
mv .zshrc .dotfile-backup 
```

Enter fullscreen mode Exit fullscreen mode

现在我们准备好了:

```
git clone --bare <dotfile repo url> $HOME/.dotfiles
echo ".dotfiles/" >> .gitignore
dot checkout
# Just in case:
dot config status.showUntrackedFiles no 
```

Enter fullscreen mode Exit fullscreen mode

现在，您的服务器应该正在变得舒适、惬意和实用！

## 奖励奖励:今日消息

我不知道你怎么想，但我相信我学习编程的 98%的原因是为了让计算机打印出有趣的信息。为此，我相应地设定了我的每日信息。

```
sudo vim /etc/motd 
```

Enter fullscreen mode Exit fullscreen mode

```
==================================
You are a gentleman and a scholar.
================================== 
```

Enter fullscreen mode Exit fullscreen mode

如果你想不出任何一句俏皮话来写在你的 MOTD 里，看看我为一个推特机器人策划的用来刺激我弟弟的[赞美列表。](https://github.com/rpalo/fanbot/blob/master/fanbot/compliments.py)

现在，无论你什么时候登录，你的服务器都会给你一条振奋人心的信息！😁

## 总结起来

我知道在系统管理领域我还有很多东西要学。我开始储备要读的书和要看的视频。我错过了什么重要的事情吗？你有额外的保护吗？有什么好的学习资源吗？让我了解他们！感谢阅读！

* * *

*原帖 [`assert_not magic?`](https://assertnotmagic.com/2018/05/14/setting-up-centos/)*