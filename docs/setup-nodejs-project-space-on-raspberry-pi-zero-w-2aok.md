# 在 Raspberry Pi Zero W 上设置节点 j 项目空间

> 原文：<https://dev.to/ladvien/setup-nodejs-project-space-on-raspberry-pi-zero-w-2aok>

### 设置 Arch Linux SD 卡

本文将基于上一篇文章，在上一篇文章中，我在 Raspberry Pi Zero W (rp0w)上完成了 Arch Linux 的无头安装。如果你不熟悉术语“无头设置”，本质上，我们正在谈论设置 SD 卡，这样你就不必把它插入显示器。你可以把它插在你的 rp0w 上，启动它，然后 SSH 进去。

*   [在树莓 Pi 上安装 Arch Linux，可立即进行 WiFi 访问](https://ladvien.com/installing-arch-linux-raspberry-pi-zero-w/)

现在您已经设置了 Arch Linux 卡，让我们在 rp0w 上设置一个 NodeJS 环境。幸运的是，有人比我聪明，他们已经为我们做了一些繁重的工作。

好吧，从吃你的树莓派开始。

### 运行 NodeJS 安装脚本

现在我们在 Raspberry Pi 命令提示符下，我们将运行一个脚本，该脚本将下载为 ARM 构建的最新版本的 NodeJS，并将其安装到 Raspberry Pi。

但在此之前，我们需要安装一些助手程序

在命令提示符下键入，并在出现提示时说“yes”。

```
sudo pacman -S wget 
```

Enter fullscreen mode Exit fullscreen mode

Wget 是一个允许从命令提示符直接下载互联网内容的包。

现在，我们将运行一个命令，该命令提取并运行 Internet 的 NodeJS 安装脚本。此脚本由`audstanley`编写，可在以下网址找到

*   [NodeJS Raspberry Pi Github](https://github.com/audstanley/NodeJs-Raspberry-Pi)

如果你喜欢这个脚本，你应该去买一杯咖啡——链接是 Github 页面。

在撰写本文时，该脚本为您的架构下载最新版本的 NodeJS(这是棘手的部分)，安装它，然后为 NodeJS 和 [npm](https://www.npmjs.com/) 正确工作创建适当的符号链接。

好了，序言到此为止。

要安装 NodeJS，请键入

```
sudo wget -O - https://raw.githubusercontent.com/audstanley/NodeJs-Raspberry-Pi/master/Install-Node.sh | sudo bash
node -v 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！

我们现在可以通过键入
来创建一个新的节点项目

```
mkdir my_node_project
cd my_node_project
npm init 
```

Enter fullscreen mode Exit fullscreen mode

如果你想了解更多关于 NodeJS 的知识，我推荐 Udemy 课程:

*   [完整的 NodeJS 开发者课程](https://www.udemy.com/the-complete-nodejs-developer-course-2/)

我没有被踢回来；是我以前上的课，很喜欢。我实际上使用了 Raspberry Pi 来编写他构建的代码，没有遇到任何问题。