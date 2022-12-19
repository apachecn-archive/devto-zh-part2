# 阅读 Kubernetes 日志

> 原文：<https://dev.to/chuck_ha/reading-kubernetes-logs-315k>

```
Date: July 8th, 2018
Kubernetes Version: v1.11.0 
```

Enter fullscreen mode Exit fullscreen mode

读取日志是调试 Kubernetes 集群所需的基本工具包的一部分。

## 立方测井

查找 Kubernetes 日志一般只需要两种方法，systemd 和 docker。您必须确定是什么在管理您感兴趣的服务，然后知道如何从那个管理器中提取日志。例如，如果 kubelet 由 systemd 管理，您需要知道如何读取 systemd 日志。

### systemd 日志

在 [systemd](https://www.freedesktop.org/wiki/Software/systemd/) 上运行的服务的日志可以用 [journalctl](http://0pointer.de/blog/projects/journalctl.html) 查看。下面是一个阅读 kubelet 日志的例子，这个服务通常通过 systemd:
运行

```
journalctl --unit kubelet 
```

Enter fullscreen mode Exit fullscreen mode

如果您使用不同的服务管理器，请查阅文档，了解如何提取特定服务管理器的日志。

### Docker 日志

[控制平面组件](https://kubernetes.io/docs/concepts/overview/components/#master-components)可以由 kubelet 使用[静态吊舱](https://kubernetes.io/docs/tasks/administer-cluster/static-pod/)进行管理。您可以通过`kubectl logs <podname>`获得他们的日志，或者如果您在运行 static pod 的节点上，您可以直接访问 docker 日志:

```
docker logs <container_id> 
```

Enter fullscreen mode Exit fullscreen mode

这对于 kube-apiserver 不工作，因此`kubectl`命令也不工作的情况很好。

## 学习阅读日志

### 找到你的方向

通过阅读功能齐全的 Kubernetes 集群中的日志来确定您的方位。您将大致了解组件正在做什么，并熟悉可以忽略的日志行，这将有助于您注意到可能包含有用调试信息的行。

不要害怕钻研源代码，看看每一行日志来自哪里。 [Kubernetes 代码搜索](https://cs.k8s.io)在这方面真的很好。复制并粘贴日志行的一部分，直到你得到[一些点击](https://cs.k8s.io/?q=Unable%20to%20create%20storage%20backend&i=nope&files=&repos=kubernetes)，并阅读周围的代码。您甚至可以使用这些日志行作为我在[学习 Kubernetes 代码库](https://dev.to/chuck_ha/learning-the-kubernetes-codebase-1324)的帖子中概述的策略的起点，以获得对组件的非常深入的理解。

一旦你习惯了阅读一个运行中的 Kubernetes 集群的日志，就该打破常规了。关掉 etcd，看日志。重新打开它，看看组件如何响应。对其他组件也这样做，看看会发生什么。您将开始了解哪些组件与其他组件通信，这将帮助您更快地从症状中识别故障。

您将看到的三个最常见的组件是 kubelet、kube-apiserver 和 etcd。我建议关注这些，但是也要查看所有组件的日志。

### 例子

这些例子来自一个 kubeadm 星团。kubelet 由 systemd 管理，所有控制面板组件都由 kubelet 作为静态 pod 管理。

# 库伯勒日志

```
Jul 08 16:32:51 ip-10-0-25-163 kubelet[20747]: E0708 16:32:51.415697   20747 kubelet_node_status.go:391] Error updating node status, will retry: error getting node "ip-10-0-25-163.us-west-2.compute.internal": Get https://10.0.25.163:6443/api/v1/nodes/ip-10-0-25-163.us-west-2.compute.internal?resourceVersion=0&timeout=10s: context deadline exceeded                                                          
Jul 08 16:33:01 ip-10-0-25-163 kubelet[20747]: W0708 16:33:01.416933   20747 reflector.go:341] k8s.io/kubernetes/pkg/kubelet/kubelet.go:455: watch of *v1.Service ended with: very short watch: k8s.io/kubernetes/pkg/kubelet/kubelet.go:455: Unexpected watch close - watch lasted less than a second and no items received                                                                                           
Jul 08 16:33:01 ip-10-0-25-163 kubelet[20747]: W0708 16:33:01.417001   20747 reflector.go:341] k8s.io/kubernetes/pkg/kubelet/config/apiserver.go:47: watch of *v1.Pod ended with: very short watch: k8s.io/kubernetes/pkg/kubelet/config/apiserver.go:47: Unexpected watch close - watch lasted less than a second and no items received                                                                               
Jul 08 16:33:01 ip-10-0-25-163 kubelet[20747]: W0708 16:33:01.417031   20747 reflector.go:341] k8s.io/kubernetes/pkg/kubelet/kubelet.go:464: watch of *v1.Node ended with: very short watch: k8s.io/kubernetes/pkg/kubelet/kubelet.go:464: Unexpected watch close - watch lasted less than a second and no items received                                                                                              
Jul 08 16:33:01 ip-10-0-25-163 kubelet[20747]: E0708 16:33:01.417105   20747 mirror_client.go:88] Failed deleting a mirror pod "etcd-ip-10-0-25-163.us-west-2.compute.internal_kube-system": Delete https://10.0.25.163:6443/api/v1/namespaces/kube-system/pods/etcd-ip-10-0-25-163.us-west-2.compute.internal: read tcp 10.0.25.163:36190->10.0.25.163:6443: use of closed network connection; some request body already written 
```

Enter fullscreen mode Exit fullscreen mode

这里的错误和警告是:

*   删除镜像窗格失败
*   手表失灵了
*   更新节点状态失败

这些错误一起表明 etcd 或 kube-apiserver 不工作。如果你知道 kubelet 和 kube-apiserver 是如何交互的，你甚至可以直接判断出这是 etcd 的问题，而不是 kube-apiserver 的问题。

但是为了举例，让我们来看看 kube-apiserver 日志。

# kube-apiserver 日志

kube-apiserver 目前正在崩溃，并不断重启。kube-apiserver 重启前的日志如下:

```
Flag --insecure-port has been deprecated, This flag will be removed in a future version.
I0708 16:38:26.448081       1 server.go:703] external host was not specified, using 10.0.25.163
I0708 16:38:26.448218       1 server.go:145] Version: v1.11.0
I0708 16:38:26.775022       1 plugins.go:158] Loaded 7 mutating admission controller(s) successfully in the following order: NamespaceLifecycle,LimitRanger,ServiceAccount,NodeRestriction,DefaultTolerationSeconds,DefaultStorageClass,MutatingAdmissionWebhook.
I0708 16:38:26.775040       1 plugins.go:161] Loaded 5 validating admission controller(s) successfully in the following order: LimitRanger,ServiceAccount,PersistentVolumeClaimResize,ValidatingAdmissionWebhook,ResourceQuota.
I0708 16:38:26.775722       1 plugins.go:158] Loaded 7 mutating admission controller(s) successfully in the following order: NamespaceLifecycle,LimitRanger,ServiceAccount,NodeRestriction,DefaultTolerationSeconds,DefaultStorageClass,MutatingAdmissionWebhook.
I0708 16:38:26.775737       1 plugins.go:161] Loaded 5 validating admission controller(s) successfully in the following order: LimitRanger,ServiceAccount,PersistentVolumeClaimResize,ValidatingAdmissionWebhook,ResourceQuota.
F0708 16:38:36.778177       1 storage_decorator.go:57] Unable to create storage backend: config (&{ /registry [https://127.0.0.1:2379] /etc/kubernetes/pki/apiserver-etcd-client.key /etc/kubernetes/pki/apiserver-etcd-client.crt /etc/kubernetes/pki/etcd/ca.crt true false 1000 0xc42029ce80 <nil> 5m0s 1m0s}), err (dial tcp 127.0.0.1:2379: connect: connection refused) 
```

Enter fullscreen mode Exit fullscreen mode

最后一条失败线是最重要的。这告诉我们 kube-apiserver 无法连接到 etcd，因为连接被拒绝。

如果我们重新启动 etcd，一切都会恢复，所有组件都会再次满意。

# 和 cd 日志

通常没有正常行为的日志，不像 kubelet 喜欢不断地告诉你它在做什么，即使一切都很好。

以下是一些 etcd 日志示例:

```
2018-07-08 16:42:20.351537 I | etcdserver: 8e9e05c52164694d as single-node; fast-forwarding 9 ticks (election ticks 10)
2018-07-08 16:42:21.039713 I | raft: 8e9e05c52164694d is starting a new election at term 4
2018-07-08 16:42:21.039745 I | raft: 8e9e05c52164694d became candidate at term 5
2018-07-08 16:42:21.039777 I | raft: 8e9e05c52164694d received MsgVoteResp from 8e9e05c52164694d at term 5
2018-07-08 16:42:21.039792 I | raft: 8e9e05c52164694d became leader at term 5
2018-07-08 16:42:21.039803 I | raft: raft.node: 8e9e05c52164694d elected leader 8e9e05c52164694d at term 5
2018-07-08 16:42:21.040764 I | etcdserver: published {Name:ip-10-0-25-163.us-west-2.compute.internal ClientURLs:[https://127.0.0.1:2379]} to cluster cdf818194e3a8c32
2018-07-08 16:42:21.041029 I | embed: ready to serve client requests
2018-07-08 16:42:21.041449 I | embed: serving client requests on 127.0.0.1:2379
WARNING: 2018/07/08 16:42:21 Failed to dial 127.0.0.1:2379: connection error: desc = "transport: authentication handshake failed: remote error: tls: bad certificate"; please retry. 
```

Enter fullscreen mode Exit fullscreen mode

最后一个警告是 etcd 特定版本中的一个错误，但对功能没有影响。这是那个 bug 的 [github 问题。](https://github.com/coreos/etcd/issues/8603)

# 其他一切

当你在那里的时候，看看所有其他组件，你将开始建立对每个组件的理解，以及如何识别什么时候有问题。