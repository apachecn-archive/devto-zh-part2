# 在 windows 主机上的 linux docker 容器中运行 GUI 应用程序

> 原文：<https://dev.to/darksmile92/run-gui-app-in-linux-docker-container-on-windows-host-4kde>

# 这是怎么回事？

Docker 有很多优点，其中之一就是能够使用隔离在 docker 容器中的 GUI 应用程序。例如你的浏览器，文本编辑器或其他东西。

不言而喻，这将使你能够在你的 windows 主机上使用 linux / macOS 软件，而不会受到一些黑客的干扰。这也将防止您的机器在删除应用程序时有剩余的依赖，因为它都被包装在 docker 容器中。

# 为什么会有人试图这么做？

我家里的私人电脑用的是 Arch Linux，工作用的是 Windows 10。我希望能够在我的 windows 机器上使用 Evolution 邮件客户端和其他方便的 linux 应用程序。

因此，有各种各样的教程介绍如何从 linux 主机和 linux 容器共享 X11 会话。但是:

# 如何从 windows 主机共享显示器？

## 安装并配置 VcXsrv

首先安装 [VcXsrv Windows X Server](https://sourceforge.net/projects/vcxsrv/) 。我们也可以使用 Xming，但 windows 的软件包自 2013 年以来就没有更新过。最简单的方法是使用 [Chocolatey](https://chocolatey.org/) ，顺便说一下，这是我最喜欢的 windows 软件包管理器！
启动 powershell 会话并运行:

```
choco install vcxsrv 
```

Enter fullscreen mode Exit fullscreen mode

然后从开始菜单运行 **Xlaunch** 并遵循初始配置步骤:
[![Setup1](../Images/a1fee14f8580aa5d668a2dc82a368136.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MCnNoPwj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g3roivsrapgy69mqkhpc.png)
[![Setup2](../Images/eb8cff06fdb2338857b0c20e43c1067f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9T2fJDCh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5l5fil0nongqswsc5qx5.png)
[![Setup3](../Images/cf0884fad68252d06a53aaa0b9f4bc9b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1fOShFRZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3eh1lry7125modpdj6a2.png)
[![Setup4](../Images/a2c22658002f6e50155383a23dbf0916.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GFylK6hC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/48tl3o3pv99vbhk06188.png)
**确保在单击完成之前保存到配置文件！**
将其保存到以下位置之一:

*   %appdata%\Xming
*   %userprofile%\Desktop
*   %用户配置文件%

## 创建 Dockerfile

举一个简单的例子，创建一个新文件夹并放置一个 *Dockerfile* ，其中包含以下内容:

```
FROM ubuntu:14.04
RUN apt-get update && apt-get install -y firefox
CMD /usr/bin/firefox 
```

Enter fullscreen mode Exit fullscreen mode

## 构建并运行容器

对于高级 docker 用户，这里的快捷命令:

```
docker build -t firefox .
set-variable -name DISPLAY -value YOUR-IP:0.0
docker run -ti --rm -e DISPLAY=$DISPLAY firefox 
```

Enter fullscreen mode Exit fullscreen mode

### 有所交代:

现在构建新的容器，并将其标记为 *firefox* :

```
docker build -t firefox . 
```

Enter fullscreen mode Exit fullscreen mode

因为容器有自己的本地主机接口，所以我们需要使用网络适配器的 IP 地址。
使用
找到您的 ip 地址

```
ipconfig 
```

Enter fullscreen mode Exit fullscreen mode

设置环境变量(用您的 IP 替换):

```
set-variable -name DISPLAY -value 10.11.128.118:0.0 
```

Enter fullscreen mode Exit fullscreen mode

运行容器:

```
docker run -ti --rm -e DISPLAY=$DISPLAY firefox 
```

Enter fullscreen mode Exit fullscreen mode

你现在应该看到一个 firefox 窗口:
[![firefox docker](../Images/0e43cdc1e601b09b7f12f0aea9a2d47a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--d_jpgRzm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zbbfl8ytpwmd9erj9hxh.png)

这将适用于大多数其他应用程序。

## 持久化数据

为了持久化数据(比如 evolution mail)，通过 docker 卷在正确的位置装载容器。

找出你的应用程序存储数据的所有位置，并安装它们:

```
docker run -v c:/docker/evolutionmail/home/user/.local/share/evolution:/root/.local/share/evolution -v c:/docker/evolutionmail/home/user/.config/evolution:/root/.config/evolution -ti --rm -e DISPLAY=$DISPLAY evolution 
```

Enter fullscreen mode Exit fullscreen mode

## evolution 邮件的示例 Dockerfile

以下是我的进化 Dockerfile 文件，以防有人需要:

```
FROM debian
# Setup enviroment variables
ENV DEBIAN_FRONTEND noninteractive

# Installing fuse filesystem is not possible in docker without elevated priviliges
# but we can fake installling it to allow packages we need to install for GNOME
RUN apt-get install libfuse2 -y && \
cd /tmp ; apt-get download fuse && \
cd /tmp ; dpkg-deb -x fuse_* . && \
cd /tmp ; dpkg-deb -e fuse_* && \
cd /tmp ; rm fuse_*.deb && \
cd /tmp ; echo -en '#!/bin/bash\nexit 0\n' > DEBIAN/postinst && \
cd /tmp ; dpkg-deb -b . /fuse.deb && \
cd /tmp ; dpkg -i /fuse.deb

# Upstart and DBus have issues inside docker.
RUN dpkg-divert --local --rename --add /sbin/initctl && ln -sf /bin/true /sbin/initctl

# Install GNOME
RUN apt-get update && apt-get install -y xorg gnome-core gnome-session-fallback
# Pull in the hack to fix keyboard shortcut bindings for GNOME 3 
ADD https://raw.githubusercontent.com/CannyComputing/Dockerfile-Ubuntu-Gnome/master/gnome-keybindings.pl /usr/local/etc/gnome-keybindings.pl
RUN chmod +x /usr/local/etc/gnome-keybindings.pl

# Add the script to fix and customise GNOME for docker
ADD https://raw.githubusercontent.com/CannyComputing/Dockerfile-Ubuntu-Gnome/master/gnome-docker-fix-and-customise.sh /usr/local/etc/gnome-docker-fix-and-customise.sh
RUN chmod +x /usr/local/etc/gnome-docker-fix-and-customise.sh

RUN apt-get update -y && apt-get install -y dbus-x11 gnome-keyring evolution evolution-data-server seahorse sudo
RUN apt-get install -y libnotify-cil-dev
CMD ["evolution"] 
```

Enter fullscreen mode Exit fullscreen mode

我很好奇你在评论里是怎么评论的！

~干杯