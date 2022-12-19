# 访问并调试 kubernetes 服务/ docker 容器

> 原文：<https://dev.to/ineedale/access-and-debug-your-kubernetes-service-docker-container-39oh>

你已经按照基本步骤在 kubernetes 中部署了一个服务，假设这个服务是 *Nginx* ，因为为什么不……

```
$ kubectl create deployment ngx --image=nginx:latest 
```

让我们将它扩展到 2 个，因为实际上只运行 1 个 pod 有点太基础了

```
$ kubectl scale deployment --replicas 2 ngx 
```

你有 2 个 Nginx pods 在你的 k8s“集群”上运行，我使用 Docker Edge
自带的内置 k8s 安装

```
$ kubectl get pods

NAME READY STATUS RESTARTS AGE
ngx-5cb59c856c-cmn8p 1/1 Running 0 52m
ngx-5cb59c856c-s65nh 1/1 Running 0 52m 
```

现在我们有了 2 个 pods，我们需要以某种方式访问服务，这样我们就可以自己做一些基本的检查，例如，在我们通过负载平衡器或入口公开该服务之前，检查事情是否按预期工作——所以我们现在将其作为内部服务公开

```
$ kubectl expose deployment ngx --port 8080 --target-port 80 
```

使用

```
$ kubectl proxy & 
```

然后，您可以通过以下方式访问您的服务(安全地说，它实际上是一个加密的隧道)

[http://localhost:8001/API/v1/namespaces/default/services/ngx:8080/proxy/](http://localhost:8001/api/v1/namespaces/default/services/ngx:8080/proxy/)

如何工作的细节在这里[这里](https://kubernetes.io/docs/tasks/administer-cluster/access-cluster-services/#manually-constructing-apiserver-proxy-urls)，但是简单地说，你可以通过
访问任何内部服务

```
http://localhost:8001/api/v1/namespace/{namespace}/services/{service-name}:{port}/ 
```

这就是你注意到其中一个容器行为异常的地方，有时似乎无法连接到后端或者只是断断续续地工作，等等。(基本上有问题)所以你决定，你想运行`ngrep`或`tcpdump`或`strace`来弄清楚发生了什么，但是你也不想修改容器映像…那么我们能做什么呢？

只要您能够访问运行容器实例的节点，您就很幸运了——在本例中，我们将只使用本地 Docker4Mac 安装，但它可以与任何运行 Docker 容器的节点一起工作。

用
找出哪个节点运行了有问题的容器

```
$ kubectl describe pod ngx-5cb59c856c-cmn8p

Name: ngx-5cb59c856c-cmn8p
Namespace: default
Node: docker-for-desktop/192.168.65.3 
```

并登录到该节点；我最初尝试用
编辑 pod

```
$ kubectl edit pod ngx-5cb59c856c-cmn8p 
```

添加一个*调试*容器，但是保存配置将会失败，因为您不能从一个 pod 添加/删除容器:(

然而，由于我们在运行容器的节点上，我们可以创建一个定制的调试容器(本地),并在与现有 ngx-5cb59c856c-cmn8p 相同的 pid 和网络名称空间中运行该容器。

docker 文件可能是这样的(无耻地复制/使用自[https://medium . com/@ Roth gar/how-to-debug-a-running-docker-container-from-a-separate-container-983 f 11740 DC 6](https://medium.com/@rothgar/how-to-debug-a-running-docker-container-from-a-separate-container-983f11740dc6))

```
FROM alpine
RUN apk update && apk add strace
CMD ["strace", "-p", "1"] 
```

运行

```
$ docker build -t strace . 
```

一旦构建了容器(实际上，你可以构建一个一致的调试容器，并让你可以随时从 GCR 或 ECR 或任何地方获得它)，你就可以用`--privileged`运行它(是的，在这个例子中我们不关心安全性，参见[贾斯汀·加里森](https://medium.com/u/18e7f33d2669)关于如何更严格地做到这一点的帖子，所以你可以用*做所有的事情*，而不必争夺权限)。

要附加到`pid`和`net`名称空间，您需要`container Id`或名称，使用
很容易找到

```
$ docker ps | grep nginx

6b6e65ebc7c8 nginx "nginx -g 'daemon of…" About an hour ago Up About an hour k8s\_nginx\_ngx-5cb59c856c-cmn8p\_default\_402e0d53-a933-11e8-93cb-025000000001\_0
e245d91ba045 nginx "nginx -g 'daemon of…" About an hour ago Up About an hour k8s\_nginx\_ngx-5cb59c856c-s65nh\_default\_36a3a1e7-a933-11e8-93cb-025000000001\_0 
```

所以我们将使用第一个是`6b6e65ebc7c8`(你显然应该使用引起麻烦的那个)…

```
docker run -ti --pid=container:6b6e65ebc7c8 --net=container:6b6e65ebc7c8 --privileged strace /bin/ash 
```

一旦被执行，你需要实际执行工作的 PID，PID 1 实际上是父 Nginx 进程，但它不处理任何请求，它只是管理子进程

```
/ # ps -ef
PID USER TIME COMMAND
 1 root 0:00 nginx: master process nginx -g daemon off;
 6 101 0:00 nginx: worker process
 44 root 0:00 /bin/ash
 50 root 0:00 ps -ef
/ # 
```

好，让我们对 PID 6 做一个`strace`，因为它实际上在做功……

```
/ # strace -fp 6
strace: Process 6 attached
gettimeofday({tv\_sec=1535294355, tv\_usec=751153}, NULL) = 0
epoll\_wait(8, [{EPOLLIN, {u32=2097902081, u64=139627189727745}}], 512, 61010) = 1
gettimeofday({tv\_sec=1535294359, tv\_usec=908313}, NULL) = 0
recvfrom(3, "GET / HTTP/1.1\r\nHost: localhost:"..., 1024, 0, NULL, NULL) = 213
stat("/usr/share/nginx/html/index.html", {st\_mode=S\_IFREG|0644, st\_size=612, ...}) = 0
open("/usr/share/nginx/html/index.html", O\_RDONLY|O\_NONBLOCK) = 11
fstat(11, {st\_mode=S\_IFREG|0644, st\_size=612, ...}) = 0
writev(3, [{iov\_base="HTTP/1.1 200 OK\r\nServer: nginx/1"..., iov\_len=238}], 1) = 238
sendfile(3, 11, [0] =\> [612], 612) = 612
write(5, "10.1.0.1 - - [26/Aug/2018:14:39:"..., 111) = 111
close(11) = 0
epoll\_wait(8, [{EPOLLIN, {u32=2097902081, u64=139627189727745}}], 512, 65000) = 1
gettimeofday({tv\_sec=1535294361, tv\_usec=971440}, NULL) = 0
recvfrom(3, "GET / HTTP/1.1\r\nHost: localhost:"..., 1024, 0, NULL, NULL) = 213
stat("/usr/share/nginx/html/index.html", {st\_mode=S\_IFREG|0644, st\_size=612, ...}) = 0
open("/usr/share/nginx/html/index.html", O\_RDONLY|O\_NONBLOCK) = 11
fstat(11, {st\_mode=S\_IFREG|0644, st\_size=612, ...}) = 0
writev(3, [{iov\_base="HTTP/1.1 200 OK\r\nServer: nginx/1"..., iov\_len=238}], 1) = 238
sendfile(3, 11, [0] =\> [612], 612) = 612
write(5, "10.1.0.1 - - [26/Aug/2018:14:39:"..., 111) = 111
close(11) = 0
epoll\_wait(8,
^Cstrace: Process 6 detached
 \<detached ...\> 
```

这就是了，对这个特定 Nginx 实例的两个请求

*   使用额外的调试容器重新部署 Pod(您可能会认为这样会更好，但是您可能无法立即重现问题，并且可能需要长时间运行它，这会耗费资源)
*   以任何方式修改 Dockerfile 文件(安装调试工具)
*   更改运行容器上的特权，它可以在比具有附加功能的调试容器更安全的环境中继续运行

这种模式的好处在于，您可以为自己创建一个调试容器，可以重用该容器来调试在任何运行 Docker 的节点(ECS、本地 K8S、EKS、AKS、GKE)上运行的应用程序。

调试愉快！

学分和资源:

*   [https://kubernetes . io/docs/tasks/administer-cluster/access-cluster-services/# manually-constructing-API server-proxy-URLs](https://kubernetes.io/docs/tasks/administer-cluster/access-cluster-services/#manually-constructing-apiserver-proxy-urls)
*   [https://medium . com/@ Roth gar/how-to-debug-a-running-docker-container-from-a-separate-container-983 f 11740 DC 6](https://medium.com/@rothgar/how-to-debug-a-running-docker-container-from-a-separate-container-983f11740dc6)
*   [https://medium . com/Google-cloud/kubernetes-node port-vs-load balancer-vs-ingress-when-I-should-use-when-when-I-what-922 f 010849 E0](https://medium.com/google-cloud/kubernetes-nodeport-vs-loadbalancer-vs-ingress-when-should-i-use-what-922f010849e0)