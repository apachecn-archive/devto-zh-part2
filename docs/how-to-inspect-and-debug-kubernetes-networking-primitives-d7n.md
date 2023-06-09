# 如何检查和调试 Kubernetes 网络原语

> 原文：<https://dev.to/digitalocean/how-to-inspect-and-debug-kubernetes-networking-primitives-d7n>

## 简介

Kubernetes 是一个容器编排系统，可以跨服务器节点集群管理容器化的应用程序。维护集群中所有容器之间的网络连接需要一些高级网络技术。在本文中，我们将简要介绍一些检查这种网络设置的工具和技术。

如果您正在调试连接性问题，调查网络吞吐量问题，或者探索 Kubernetes 以了解它是如何工作的，这些工具可能会很有用。

如果你想从总体上了解更多关于 Kubernetes 的信息，你可以通过阅读 DigitalOcean 的[*Kubernetes*T3 的介绍来了解基础知识。有关 Kubernetes 网络的具体概述，请阅读](https://www.digitalocean.com/community/tutorials/an-introduction-to-kubernetes) [*Kubernetes 网络引擎盖下*](https://www.digitalocean.com/community/tutorials/kubernetes-networking-under-the-hood) 。

## 入门

本教程假设您已经有一个 Kubernetes 集群，在您的本地计算机上安装了`kubectl`,并配置为连接到集群。出现的任何`kubectl`命令都是为了在您的本地机器上运行。

所有其他命令将作为根用户在 Kubernetes 节点上运行。如果您在 Kubernetes 节点上使用支持 sudo 的非 root 用户，请在必要时使用`sudo`来运行命令。

## 查找 Pod 的集群 IP

要查找 Kubernetes pod 的集群 IP 地址，请在本地机器上使用带有选项`-o wide`的`kubectl get pod`命令。此选项将列出更多信息，包括 pod 所在的节点以及 pod 的群集 IP。

```
kubectl get pod -o wide 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
NAME                           READY     STATUS    RESTARTS   AGE       IP            NODE
hello-world-5b446dd74b-7c7pk   1/1       Running   0          22m       10.244.18.4   node-one
hello-world-5b446dd74b-pxtzt   1/1       Running   0          22m       10.244.3.4    node-two 
```

Enter fullscreen mode Exit fullscreen mode

**IP** 列将包含每个 pod 的内部集群 IP 地址。

如果您没有看到您要查找的 pod，请确保您位于正确的名称空间中。您可以通过添加标志`--all-namespaces`来列出所有名称空间中的所有窗格。

## 查找服务的 IP

我们也可以使用`kubectl`找到一个服务 IP。在这种情况下，我们将列出所有名称空间中的所有服务:

```
kubectl get service --all-namespaces 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
NAMESPACE     NAME                       TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)         AGE
default       kubernetes                 ClusterIP   10.32.0.1       <none>        443/TCP         6d
kube-system   csi-attacher-doplugin      ClusterIP   10.32.159.128   <none>        12345/TCP       6d
kube-system   csi-provisioner-doplugin   ClusterIP   10.32.61.61     <none>        12345/TCP       6d
kube-system   kube-dns                   ClusterIP   10.32.0.10      <none>        53/UDP,53/TCP   6d
kube-system   kubernetes-dashboard       ClusterIP   10.32.226.209   <none>        443/TCP         6d 
```

Enter fullscreen mode Exit fullscreen mode

服务 IP 可以在**集群 IP** 列中找到。

## 查找并输入 Pod 网络名称空间

每个 Kubernetes pod 都有自己的网络名称空间。网络名称空间(或 netns)是一种 Linux 网络原语，它提供了网络设备之间的隔离。

从 pod 的 netns 中运行命令，检查 DNS 解析或一般网络连接，这可能很有用。为此，我们首先需要在 pod 中查找其中一个容器的进程 ID。对于 Docker，我们可以通过一系列两个命令来完成。首先，列出一个节点上运行的容器:

```
docker ps 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
CONTAINER ID        IMAGE                                   COMMAND                  CREATED             STATUS              PORTS               NAMES
173ee46a3926        gcr.io/google-samples/node-hello        "/bin/sh -c 'node se…"   9 days ago          Up 9 days                               k8s_hello-world_hello-world-5b446dd74b-pxtzt_default_386a9073-7e35-11e8-8a3d-bae97d2c1afd_0
11ad51cb72df        k8s.gcr.io/pause-amd64:3.1              "/pause"                 9 days ago          Up 9 days                               k8s_POD_hello-world-5b446dd74b-pxtzt_default_386a9073-7e35-11e8-8a3d-bae97d2c1afd_0
. . . 
```

Enter fullscreen mode Exit fullscreen mode

在 pod 中找到您感兴趣的任何容器的**容器 ID** 或**名称**。在上面的输出中，我们展示了两个容器:

*   第一个容器是在`hello-world`窗格中运行的`hello-world`应用
*   第二个是在`hello-world`舱中运行的*暂停*容器。这个容器的存在只是为了保存 pod 的网络名称空间

要获得任一容器的进程 ID，记下容器 ID 或名称，并在下面的`docker`命令中使用它:

```
docker inspect --format '{{ .State.Pid }}' your_container_id_or_name 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
14552 
```

Enter fullscreen mode Exit fullscreen mode

将输出一个进程 ID(或 PID)。现在我们可以使用`nsenter`程序在该进程的网络名称空间中运行命令:

```
nsenter -t your_container_pid -n ip addr 
```

Enter fullscreen mode Exit fullscreen mode

确保使用您自己的 PID，并用您希望在 pod 的网络名称空间内运行的命令替换`ip addr`。

> **注意:**使用`nsenter`在 pod 的名称空间中运行命令——与使用类似`docker exec`的东西相比——的一个优点是，您可以访问节点上可用的所有命令，而不是安装在容器中的通常有限的命令集。

## 查找 Pod 的虚拟以太网接口

每个 pod 的网络命名空间通过虚拟以太网管道与节点的根网络通信。在节点端，这个管道显示为一个设备，通常以`veth`开始，以唯一标识符结束，比如`veth77f2275`或`veth01`。在吊舱内部，该管道显示为`eth0`。

关联哪个`veth`设备与特定的 pod 配对会很有用。为此，我们将列出节点上的所有网络设备，然后列出 pod 的网络命名空间中的设备。然后，我们可以将两个列表之间的设备号关联起来，以建立连接。

首先，使用`nsenter`在 pod 的网络名称空间中运行`ip addr`。请参考上一节*查找和输入 Pod 网络名称空间*了解如何操作的详细信息:

```
nsenter -t your_container_pid -n ip addr 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
10: eth0@if11: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UP group default
    link/ether 02:42:0a:f4:03:04 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.244.3.4/24 brd 10.244.3.255 scope global eth0
       valid_lft forever preferred_lft forever 
```

Enter fullscreen mode Exit fullscreen mode

该命令将输出 pod 接口的列表。注意示例输出中`eth0@`后面的`if11`号。这意味着这个 pod 的`eth0`链接到节点的第 11 个接口。现在在节点的默认名称空间中运行`ip addr`，列出它的接口:

```
ip addr 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever

. . .

7: veth77f2275@if6: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue master docker0 state UP group default
    link/ether 26:05:99:58:0d:b9 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet6 fe80::2405:99ff:fe58:db9/64 scope link
       valid_lft forever preferred_lft forever
9: vethd36cef3@if8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue master docker0 state UP group default
    link/ether ae:05:21:a2:9a:2b brd ff:ff:ff:ff:ff:ff link-netnsid 1
    inet6 fe80::ac05:21ff:fea2:9a2b/64 scope link
       valid_lft forever preferred_lft forever
11: veth4f7342d@if10: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue master docker0 state UP group default
    link/ether e6:4d:7b:6f:56:4c brd ff:ff:ff:ff:ff:ff link-netnsid 2
    inet6 fe80::e44d:7bff:fe6f:564c/64 scope link
       valid_lft forever preferred_lft forever 
```

Enter fullscreen mode Exit fullscreen mode

在这个示例输出中，第 11 个接口是`veth4f7342d`。这是我们正在调查的 pod 的虚拟以太网管道。

## 检查 Conntrack 连接跟踪

在 1.11 版本之前，Kubernetes 使用 iptables NAT 和 conntrack 内核模块来跟踪连接。要列出当前正在跟踪的所有连接，请使用`conntrack`命令:

```
conntrack -L 
```

Enter fullscreen mode Exit fullscreen mode

要持续观察新连接，请使用`-E`标志:

```
conntrack -E 
```

Enter fullscreen mode Exit fullscreen mode

要列出 conntrack 跟踪的到特定目的地址的连接，请使用`-d`标志:

```
conntrack -L -d your_destination_address 
```

Enter fullscreen mode Exit fullscreen mode

如果您的节点在与服务建立可靠连接时遇到问题，则可能是您的连接跟踪表已满，新连接正在被丢弃。如果是这种情况，您可能会在系统日志中看到如下消息:

```
Jul 12 15:32:11 worker-528 kernel: nf_conntrack: table full, dropping packet. 
```

Enter fullscreen mode Exit fullscreen mode

sysctl 设置了要跟踪的最大连接数。您可以使用以下命令列出您的当前值:

```
sysctl net.netfilter.nf_conntrack_max 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
net.netfilter.nf_conntrack_max = 131072 
```

Enter fullscreen mode Exit fullscreen mode

要设置新值，使用`-w`标志:

```
sysctl -w net.netfilter.nf_conntrack_max=198000 
```

Enter fullscreen mode Exit fullscreen mode

要使该设置永久化，将其添加到`sysctl.conf`文件:

```
net.ipv4.netfilter.ip_conntrack_max = 198000 
```

Enter fullscreen mode Exit fullscreen mode

## 检查 Iptables 规则

在 1.11 版本之前，Kubernetes 使用 iptables NAT 来实现虚拟 IP 转换和服务 IP 的负载平衡。

要转储一个节点上的所有 iptables 规则，可以使用`iptables-save`命令:

```
iptables-save 
```

Enter fullscreen mode Exit fullscreen mode

因为输出可能很长，所以您可能希望通过管道连接到一个文件(`iptables-save > output.txt`)或一个分页器(`iptables-save | less`)来更容易地查看规则。

要列出 Kubernetes 服务 NAT 规则，使用`iptables`命令和`-L`标志来指定正确的链:

```
iptables -t nat -L KUBE-SERVICES 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
Chain KUBE-SERVICES (2 references)
target     prot opt source               destination
KUBE-SVC-TCOU7JCQXEZGVUNU  udp  --  anywhere             10.32.0.10           /* kube-system/kube-dns:dns cluster IP */ udp dpt:domain
KUBE-SVC-ERIFXISQEP7F7OF4  tcp  --  anywhere             10.32.0.10           /* kube-system/kube-dns:dns-tcp cluster IP */ tcp dpt:domain
KUBE-SVC-XGLOHA7QRQ3V22RZ  tcp  --  anywhere             10.32.226.209        /* kube-system/kubernetes-dashboard: cluster IP */ tcp dpt:https
. . . 
```

Enter fullscreen mode Exit fullscreen mode

## 查询集群 DNS

调试集群 DNS 解析的一种方法是部署一个调试容器，其中包含您需要的所有工具，然后使用`kubectl`在其上执行`nslookup`。这在[官方的 Kubernetes 文档](https://kubernetes.io/docs/tasks/administer-cluster/dns-debugging-resolution/)中有所描述。

查询集群 DNS 的另一种方式是从节点使用`dig`和`nsenter`。如果没有安装`dig`，可以在基于 Debian 的 Linux 发行版上安装`apt`:

```
apt install dnsutils 
```

Enter fullscreen mode Exit fullscreen mode

首先，找到 **kube-dns** 服务的集群 IP:

```
kubectl get service -n kube-system kube-dns 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
NAME       TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)         AGE
kube-dns   ClusterIP   10.32.0.10   <none>        53/UDP,53/TCP   15d 
```

Enter fullscreen mode Exit fullscreen mode

上面突出显示了集群 IP。接下来，我们将使用`nsenter`在容器名称空间中运行`dig`。查看章节 [*寻找并输入 Pod 网络名称空间*](#finding-and-entering-pod-network-namespaces) 以获得更多信息:

```
nsenter -t 14346 -n dig kubernetes.default.svc.cluster.local @10.32.0.10 
```

Enter fullscreen mode Exit fullscreen mode

这个`dig`命令查找服务的完整域名**Service-name . namespace . SVC . cluster . local**，并指定集群 DNS 服务 IP 的 IP ( `@10.32.0.10`)。

## 看着 IPVS 的细节

从 Kubernetes 1.11 开始，`kube-proxy`可以配置 IPVS 来处理虚拟服务 IP 到 pod IPs 的转换。可以用`ipvsadm` :
列出 IP 的翻译表

```
ipvsadm -Ln 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
IP Virtual Server version 1.2.1 (size=4096)
Prot LocalAddress:Port Scheduler Flags
  -> RemoteAddress:Port           Forward Weight ActiveConn InActConn
TCP  100.64.0.1:443 rr
  -> 178.128.226.86:443           Masq    1      0          0
TCP  100.64.0.10:53 rr
  -> 100.96.1.3:53                Masq    1      0          0
  -> 100.96.1.4:53                Masq    1      0          0
UDP  100.64.0.10:53 rr
  -> 100.96.1.3:53                Masq    1      0          0
  -> 100.96.1.4:53                Masq    1      0          0 
```

Enter fullscreen mode Exit fullscreen mode

要显示单个服务 IP，使用`-t`选项并指定所需的 IP:

```
ipvsadm -Ln -t 100.64.0.10:53 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
Prot LocalAddress:Port Scheduler Flags
  -> RemoteAddress:Port           Forward Weight ActiveConn InActConn
TCP  100.64.0.10:53 rr
  -> 100.96.1.3:53                Masq    1      0          0
  -> 100.96.1.4:53                Masq    1      0          0 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

在本文中，我们回顾了一些用于探索和检查 Kubernetes 集群网络细节的命令和技术。想了解更多关于 Kubernetes 的信息，请看一下 [DigitalOcean 的 Kubernetes 教程](https://www.digitalocean.com/community/tags/kubernetes?type=tutorials)和[Kubernetes 官方文档](https://kubernetes.io/docs/home/)。