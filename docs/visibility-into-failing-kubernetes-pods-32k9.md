# 了解失败的 Kubernetes 豆荚

> 原文：<https://dev.to/jmarhee/visibility-into-failing-kubernetes-pods-32k9>

CrashLoopBackoff 状态通常意味着您的 Pod 将被创建，容器将被拉出，但某些原因导致容器进程退出并导致容器失败，启动回退计时器，然后重新启动。访问容器有时就像执行相关的 pod 容器一样简单:

```
kubectl exec -ti \<pod name\> -c \<container\> -- /bin/sh 
```

但是另一种方法是，如果 pod 容器不支持这一点(确实如此)，您可以做两件事:

1.  覆盖 entrypoint，用一个新命令启动容器，允许您输入和调试(如果您怀疑 entrypoint 命令由于网络连接或 DNS 解析等原因而失败，这很有用——您可以登录、ping 和 netcat，并挖掘您想要的任何内容),或者…
2.  运行一个运行 shell 的 sidecar，主容器中的任何内容都可以通过本地主机网络获得。

第一种方法相当简单，您只需在容器字段中声明规范的一个新子集，命令:

```
app: web
 spec:
 containers:
 - name: web-app
 image: bizco/neat-frontend:latest
 ports:
 - containerPort: 8081
   command:  
 - "/bin/sh"  
 - "-c"  
 - "sleep 36000" 
```

sleep 命令将使该命令保持活动状态，容器保持运行，您可以直接登录到所需的映像，并有效地覆盖该映像的原始 entrypoint 命令。

在第二种情况下，这可能是软件没有彻底失败，但是您怀疑服务出现问题，或者更广泛地说，为 pod 解决某些问题，或者即使映像不支持命令字段，因此 exec 不会为您做这件事，并让服务在运行容器中运行，您可以添加一个新容器作为 sidecar，以允许您访问 shell 进行调试:

```
app: web
 spec:
 containers:
 - name: web-app
 ...

 - name: debug-shell  
 image: alpine:3.6 
   command:  
 - "/bin/sh"  
 - "-c"  
 - "sleep 36000" 
```

然后，pod 中运行的所有内容，以及正在使用的端口，都可以通过 localhost 获得。例如，如果您有一个运行 MongoDB 之类的数据库的 pod，并且服务没有失败，但似乎在某些操作上遇到了障碍，因此您怀疑有问题需要下载新数据以提供给 MongoDB，配置如下:

```
containers:
 - name: mongodb
 image: mongo
 ports:
 - containerPort: 27017 
```

如果您添加第二个容器，您可以访问 MongoDB，而不是通过 KubeDNS 中的服务(即 MongoDB . whatever namespace . cluster . local)来公开，您可以在该 Pod 中点击 localhost:27017，就像在该 Pod 中运行任何其他服务一样。