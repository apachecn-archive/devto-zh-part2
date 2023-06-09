# 如何在本地运行时使用 Google 协同实验室

> 原文：<https://dev.to/ikeyasu/how-to-use-google-colaboratory-with-local-runtime-4j1p>

# TL；速度三角形定位法(dead reckoning)

*   我创建了 [docker 图像](https://hub.docker.com/r/ikeyasu/colab-local/)看起来像谷歌实验室
    *   它使用与谷歌合作实验室相同的操作系统版本(Ubuntu17.10)。
    *   它安装在与 Google colaboratory 相同的 python 库中。
*   您可以将此 docker 图像与 Google 协同实验室的“本地运行时”功能结合使用

### 步连接

1.  设置 [nvidia 坞站 2](https://github.com/nvidia/nvidia-docker/wiki/Installation-(version-2.0))
2.  `$ docker run --runtime=nvidia -it --rm -p 8081:8081 --cap-add SYS_ADMIN --device /dev/fuse --security-opt apparmor=unconfined ikeyasu/colab-local:latest`
    *   `--cap-add SYS_ADMIN`、`--device /dev/fuse`、`--security-opt`和`apparmor=unconfined`是安装 Google drive 所必需的。
    *   您需要更改端口号，请更改`-p 8081:8081`。比如`-p 8082:8081`如果要用 8082。
3.  单击协同实验室右上角的▼(“连接”按钮旁边),然后单击
4.  将后端端口输入到 docker 实例，如 8081。然后，单击连接按钮。

### 如果运行 jupyter 笔记本的主机不是本地主机

协作室需要本地主机来连接本地实例。如果你想在另一台主机上运行 jupyter notebook，你需要如下使用端口转发。

`$ ssh user@example.com -L 8081:localhost:8081`

如果你用的是 windows，可以用 PuTTY。

# 什么是利益？

谷歌实验室有很多限制，尤其是它只能运行 12 个小时。另一方面，谷歌合作实验室有利于深度学习的第一步。

因此，您可以采取以下步骤来推进您的项目。

1.  使用 Google Colaboratory 进行粗略的调查和测试。它有 12 小时的限制。
2.  如果你的模型看起来不错，启动本地运行时。它没有限制。你可以运行一个超过 12 小时的学习过程。

本文介绍的 Docker image 是一个看起来很像 Google 的合作实验室。您不需要考虑宿主运行时和本地运行时之间的环境差异。