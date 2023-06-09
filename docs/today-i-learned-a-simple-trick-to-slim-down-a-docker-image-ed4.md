# 今天我学到了:一个简单的减肥方法

> 原文：<https://dev.to/shriharshmishra/today-i-learned-a-simple-trick-to-slim-down-a-docker-image-ed4>

您可能希望减少图像占用的空间。如果你是在 Ubuntu 映像的基础上构建的，在构建过程中，你会调用`apt-get update`(你肯定是在安装最新版本！)然后你可以删除一些 apt-get 下载的以后用不到的文件。下面的 done 文件片段显示了应该如何做。注意，RUN 指令做了 3 件事，更新包缓存、安装 ant 和删除缓存。这是以这样的方式完成的，Docker 在图像的单个层中完成所有这些。如果这些步骤被分割成不同的运行指令，那么你的 Docker 主机和/或注册表将会使用更多的空间。

```
 FROM jenkins/jenkins:lts

#Change user to root and install ant version 1.9.9-1 (available in Ubuntu packages)
USER root

#
RUN apt-get update && \
        apt-get install -y ant=1.9.9-1 && \
        rm -rf /var/lib/apt/lists/* # remove the cached files. 
```

Enter fullscreen mode Exit fullscreen mode

对我来说，它节省了大约 138 米的空间。然而，YMMV。

对了，**你删了什么**？您只是使用`apt-get install`或`apt-get upgrade`删除了关于可供下载的最新软件包和版本的信息。`apt-get`缓存这些信息以备后用。当您运行基于这个映像的容器时，很可能不需要这些信息。在运行容器的同时安装软件是一个很大的错误！。

类似地，缓存文件也会存在于其他 Linux 发行版中。

我希望这能帮助你精简你的 ubuntu 图片。