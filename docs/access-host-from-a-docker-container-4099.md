# 从 docker 容器访问主机

> 原文：<https://dev.to/bufferings/access-host-from-a-docker-container-4099>

该信息截至 2018 年 3 月 31 日，文档编号为 18.03.0-ce

我想从 docker 容器访问主机端口。

例如，一个 nginx 进程在主机上运行，端口 8888 是打开的，那么我想创建一个容器来 curl host:8888。

我知道如何发现容器到容器，但不知道如何从容器中获取主机 IP 地址。

所以，我在网上闲逛。

## 前提条件

我主要用 **Docker for Mac** ，但有时候也会用 **Linux** 。所以我希望我的容器能在两个平台上运行。

## TL；速度三角形定位法(dead reckoning)

[https://github.com/bufferings/docker-access-host](https://github.com/bufferings/docker-access-host)

## 主机联网模式？

我发现 Docker 有“主机联网模式”。

[https://docs.docker.com/network/host/](https://docs.docker.com/network/host/)

在这种模式下，容器似乎可以通过“127.0.0.1”访问主机。多简单啊！我找到了！

所以我试了一下，它在我的 Linux 机器上运行正常，但在我的 Mac 电脑上不能运行。然后我发现:

[https://docs . docker . com/network/network-tutorial-host/#先决条件](https://docs.docker.com/network/network-tutorial-host/#prerequisites)

> 主机网络驱动程序仅在 Linux 主机上运行，在 Docker for Mac、Docker for Windows 或 Docker EE for Windows Server 上不受支持。

(·ω`)好吧...请让我找另一条路。

## host.docker.internal？

我发现我可以在 Docker for Mac 的容器中将`host.docker.internal`解析为主机 IP 地址。太好了！这就是我想知道的！但是...Linux 版本的在哪里...？

(ω`)没找到...

此外，还有几个名字让我有点困惑:

*   docker.for.mac.localhost
*   docker.for.mac.host.internal
*   **host . docker . internal**<-自 18.03.0-ce-mac59

[https://docs . docker . com/docker-for-MAC/release notes](https://docs.docker.com/docker-for-mac/release-notes)

## 最后，我的解决方案是这样的。

我创建了`docker-entrypoint.sh` :

```
HOST_DOMAIN="host.docker.internal"
ping -q -c1 $HOST_DOMAIN > /dev/null 2>&1
if [ $? -ne 0 ]; then HOST_IP=$(ip route | awk 'NR==1 {print $3}')
  echo -e "$HOST_IP\t$HOST_DOMAIN" >> /etc/hosts
fi

(main entrypoint) 
```

Enter fullscreen mode Exit fullscreen mode

并创建 Dockerfile 调用这个文件作为入口点。

```
COPY docker-entrypoint.sh /usr/local/bin/

ENTRYPOINT ["/usr/local/bin/docker-entrypoint.sh"] 
```

Enter fullscreen mode Exit fullscreen mode

因此，Mac 版 Docker 什么也不做，因为它有“host.docker.internal ”,而 Linux 版将它添加到 hosts 文件中。

(∩_ _ _)

样本代码在这里:
[https://github.com/bufferings/docker-access-host](https://github.com/bufferings/docker-access-host)

我希望 host.docker.internal 能在不久的将来被添加到 Linux 中。

如果你知道更好的解决方案，请告诉我。