# SSH 文件系统

> 原文：<https://dev.to/petarov/ssh-file-system-43p1>

> 你，深！
> 
> [![XKCD Documents](img/fbf7a01fc5022ea2697ecfe944639e1c.png)T2】](https://xkcd.com/1360)

把这篇文章读给你听。

[https://api.parler.io/ss/player?url=https://www.parler.io/audio/4622312860/3248c94d87d858e69a8a669e9acb19d9b380dc85.5a352dde-baa3-4ca4-b1a6-4f303847b5bc.mp3](https://api.parler.io/ss/player?url=https://www.parler.io/audio/4622312860/3248c94d87d858e69a8a669e9acb19d9b380dc85.5a352dde-baa3-4ca4-b1a6-4f303847b5bc.mp3)

# 目录

*   [目标](#goal)
*   [SSH 文件系统](#sshfs)
*   [用户访问工作流程](#user-access)
*   [设置](#setup)
    *   [Debian Linux](#setup-debian)
    *   [苹果电脑](#setup-macos)

# 目标

几周前，我发现自己设置了一个电子书服务器，用来存储我越来越多的*本想看的书，但我很少有时间去读“*收藏。这给我带来了一个有趣的网络问题，我必须解决它。我有一个大的外部驱动器连接到我想使用的另一台机器上，但是，设置一个 Samba 服务器不是我想花几杯咖啡去做的事情，NFS 也不是我的选择。我突然想到，我应该可以使用 SSH 来实现我的目标。

# SSH 文件系统

首先，我必须说，我很喜欢这个想法。使用 SSH 作为通过非对称加密提供无缝文件 I/O 操作和访问控制的手段是我从现在开始一直考虑的事情。这背后的工具就是 [sshfs](https://github.com/libfuse/sshfs) 。它经常被[更新](https://github.com/libfuse/sshfs/blob/master/ChangeLog.rst)，你可以在他们的包管理器中找到它。

然而，有几件事需要考虑。

1.  虽然 **sshfs** 确实使用了[缓存](https://github.com/libfuse/sshfs/blob/master/cache.c)和多线程来解决这个问题，但是由于加密(当然还有网络)操作，SSH 传输预计会变慢。在任何情况下，这似乎不是一个问题，我在一个有线以太网连接的本地网络。
2.  sshfs 带来了一些非常有趣的用户管理案例，但这可能会很快变得太复杂而难以处理。我认为在复杂的访问权限场景中使用这种方法可能会很痛苦。

# 用户访问工作流

下面是一个关于幕后发生的事情的简化工作流程。

[![SSHFS User Access](img/91d931c8abd8d64e8cb6f85d134261c2.png)T2】](http://i.imgur.com/7YPQPBA.png)

*   **服务器 A** 上的爱丽丝需要访问**服务器 B** 的存储。
*   鲍勃拥有对**服务器 B** 的存储的完全访问权。
*   艾丽丝生成一个 ssh 密钥对，并使用它建立一个 SSH 连接，在**服务器 B** 上扮演*鲍勃*。
*   **alice** 在**服务器 A** 上创建一个挂载点，并可以通过已建立的 SSH 通道在存储上诱导文件读/写操作。

# 设置

我准备了两个设置用例——一个基于我目前正在使用的 Linux，另一个用于 macOS，这主要是为了研究目的。在 **Windows** 上使用 **sshfs** 是最有可能的，但是，我还没有在那个方向上做任何设置研究。

## Debian Linux

通过 aptitude 在**服务器 A** 上安装 **sshfs** :

```
sudo apt-get install sshfs 
```

通过 aptitude 安装 FUSE(用户空间的文件系统):

```
sudo apt-get install fuse 
```

您需要通过以下方式加载`fuse`模块:

```
modprobe fuse 
```

检查模块是否通过以下方式加载:

```
lsmod | grep fuse 
```

在**服务器 A** 上运行`ssh-keygen`来生成密钥对。

从**服务器 A** 复制`$HOME/.ssh/id_rsa.pub`中的公钥，粘贴到**服务器 B** 上的`$HOME/.ssh/authorized_keys`中。或者直接用`scp`把文件复制过来。

创建一个目标挂载点，并将其挂载到**服务器 A** ，例如，

```
mkdir $HOME/mountpoint
sshfs bob@server-b:/var/storage $HOME/mountpoint 
```

您现在可以访问**服务器 B** 的存储空间。要卸载 run:

```
fusermount -u $HOME/mountpoint 
```

### 通过 systemd.service 挂载

将它作为服务运行会很棒，所以如果**服务器 A** 重启，挂载点会自动设置。对于使用 **systemd** 的系统，我们可以通过以下方式实现:

在您的`$HOME`(或任何其他可访问的)目录中创建一个`mount`文件。

```
#!/bin/sh

CMD="$1"
MNT="/home/mountpoint"
TARGET="/var/storage/"

start() {
  exec sshfs bob@server-b:$TARGET $MNT
}

stop() {
  fusermount -u $MNT
}

case $1 in start|stop) "$1" ;;
esac 
```

作为`root`用户，在`/lib/systemd/system`中创建一个`bob-mount.service`文件，并在里面添加以下内容:

```
[Unit]
Description=Server B Mount Service
After=network.target

[Service]
User=alice
Group=alice
Type=forking
ExecStart=/home/alice/mount start
ExecStop=/home/alice/mount stop

[Install]
WantedBy=multi-user.target 
```

通过以下方式启动和停止服务:

```
systemctl start bob-mount
systemctl stop bob-mount 
```

允许服务在启动时运行:

```
systemctl enable bob-mount 
```

## macOS

macOS 上的安装非常相似。使用 **brew** 安装以下:

```
brew install Caskroom/cask/osxfuse
brew install Caskroom/cask/sshfs 
```

之后你应该会在你的**系统偏好设置**中看到`FUSE for macOS`。要安装共享运行，请执行以下操作:

```
mkdir $HOME/mountpoint
sshfs bob@server-b:/var/storage $HOME/mountpoint 
```

要卸载，只需运行:

```
unmount $HOME/mountpoint 
```

重启后挂载的一个简单方法是创建一个挂载脚本，并将其放在`crontab`中。