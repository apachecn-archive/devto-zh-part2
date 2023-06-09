# 什么是容器？

> 原文：<https://dev.to/jonfriesen/what-is-a-container-3i4o>

集装箱正在爆炸。每个人都在谈论 Docker、Kubernetes 和集装箱化。在这篇博文中，我将深入探讨什么是容器，为什么它们变得如此流行，并揭露一些误解。

让我们从打开真实世界的海运集装箱开始。对托运人来说，集装箱就是一个盒子，他们可以把东西放在里面，然后无忧无虑地发往世界各地。一个集装箱可以装下一堆被扔来扔去的毛绒玩具，或者由特殊装置固定的昂贵工程设备。这些容器可以装下大多数物品，无论个人需要多少。这是故意的。

集装箱是按照精确的标准建造的，因此它们可以堆叠、装在火车、卡车上，并且可以在空中提升。制造商并不重要，容器的规格将适用于所有这些系统。

[![A picture of a port full of shipping containers](img/85e86fc46b4a8f9c55dd367094dead45.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wr4F24WS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jonfriesen.ca/_next/static/media/shipping-containers.a95d2ced.jpeg)

Linux 容器就像一个运输容器。它有一个标准化的外部，可以嵌入许多系统，如 Docker 和 Kubernetes，但仍然可以容纳创建者想要的任何东西(如 NodeJS 或 Tomcat)。

但是什么是容器呢？

> 容器只是花哨的 Linux 进程——斯科特·麦卡蒂

Linux 中没有容器对象，它不是一个东西。容器是使用[名称空间](http://man7.org/linux/man-pages/man7/namespaces.7.html)和 [Cgroups](http://man7.org/linux/man-pages/man7/cgroups.7.html) 包含的进程。还有 SELinux 内核模块，它提供了出于安全目的给予和限制细粒度控制的能力。

名称空间给出了一个包含方面的容器。也就是说，当在容器内部操作时，它看起来就是 root 用户，可以完全访问所有内容。名称空间提供对内核级系统的访问，如

*   PIDs
*   分时
*   上升
*   网络接口
*   用户 id
*   进程间通信

名称空间描绘了一幅进程在主机上看到的画面，这可能是一个非常有限的视图。

### 试试这个——名称空间

启动一个具有 Docker:
提供的默认名称空间的容器

```
 # press q to exit
    docker run -it --rm busybox top 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我们看到了在 busybox 容器中运行的进程，它只是 PID 1 的一个(`top`)。PID 1 是特殊的，因为:

*   所有其他进程都被终止(通过终止信号)
*   一个有子进程的进程死了，子进程的新父进程是 PID 1
*   PID 1 没有默认操作

关键的一点是命名空间封装了这个进程，隐藏了所有其他进程。我们可以使用`--pid`参数设置要与主机共享的名称空间，运行:

```
 docker run -it --rm --pid host busybox top 
```

Enter fullscreen mode Exit fullscreen mode

将显示所有的主机进程，以及在容器中运行的`top`。请注意，`top`进程不再是 PID 1。

Docker 允许您将名称空间与主机和其他容器连接起来，这对监控进程很有用。

**Cgroups** 控制容器可以访问哪些资源以及每个资源有多少，比如内存和 cpu。Docker 将创建一个控制组来限制容器过度使用资源。这可以防止主机将所有的资源都投入到内存泄漏这样的进程中。这个群组可以在`/sys/fs/cgroup/memory/docker/<container hash>`进行调查。

### 试试这个——Cgroups

1.  启动 docker 容器 hello-world 服务器`docker run --rm -it -p 80:80 strm/helloworld-http`
2.  使用`docker ps`获取容器散列:

```
CONTAINER ID IMAGE
970cafba8013 strm/helloworld-http 
```

Enter fullscreen mode Exit fullscreen mode

1.  导航到该容器`cd /sys/fs/cgroup/memory/docker/970cafba8013<TAB>`的 cgroup。不要忘记使用 tab 键来完成整个散列！
2.  查看分配了多少内存:`cat memory.limit_in_bytes`(对我来说是:`9223372036854771712`)

由于容器只是组合起来构建容器化流程的少数内核结构，因此容器的定义在不同的运行时会有所不同。运行时将在以后的文章中更详细地探讨。

-多克在其中扮演什么角色？

你可能想知道为什么人们现在对容器如此狂热。在 Docker 之前，容器化的流程不仅难以实现，而且难以确保包含所有必要的依赖项。Docker 在容器普及中的作用是它的简单工具集。Docker 工具集可以构建、运行、监控容器等等。

Docker 使用图像作为创建容器的可重用模板。图像包括创建完全可再现的容器化过程所需的一切。容器也是*分层的*，这允许你作为一个自底向上的层来共享不同的依赖关系。使用 dockerfiles 可以很容易地构建一个映像，docker files 是一个允许您以另一个映像作为基础或从头开始的文件。这里有一个 dockerfile 的例子:

```
FROM strm/helloworld-http # our base starting image
RUN mkdir /new-www
WORKDIR /new-www
RUN echo "Hello Docker layer tests" > index.html
ENTRYPOINT python -m SimpleHTTPServer 80 
```

Enter fullscreen mode Exit fullscreen mode

上面 docker 文件中的每一行都是一层。这些层从上到下是分层的。如果父代发生变化，所有的子代都必须重建，但父代保持不变。这意味着您可以有策略地使用层次底部变化最频繁的层来构建图像。

例如，web 应用程序容器由三层图像组成。第一层是 [alpine linux](https://www.alpinelinux.org/) ，一个微小的 linux 发行版。基础设施团队将由性能增强的 web 服务器组成的第二层加载到他们的基础 alpine 层上。第三层由应用程序团队所有，他们将二进制文件和依赖项复制到映像中。

[![Diagram of container image layers stacked](img/9953493ad4551e9e06d5028564f4af34.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ujxh7qRs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jonfriesen.ca/_next/static/media/container-stack.a1079ecf.png)

分层系统的重要性体现在两个方面。首先，它们是可重用的，所以上面例子中的 webserver 层可以被该公司的许多团队使用。第二，构建映像时，您只需从最新的变更开始。如果第一层或第二层没有改变，你不需要去获取最新的版本，这有利于应用程序的发布。基础映像可以包含所有不经常更改的依赖项，下一层可以包含用于快速构建和发布的版本。

Docker 整合了运行、构建、故障排除和共享包含具有复杂依赖关系的应用程序的映像所需的所有必要工具。除了这个小软件包之外，完整且易于使用的文档使它很容易升级，而不必了解 Linux 进程的复杂细节。

关于时机也有话要说。LXC，另一个 Linux 容器系统比 Docker 早 5 年发布。那时，传统的虚拟机是主流，DevOps 文化的转变已经获得了巨大的成功，并将在未来几年继续下去。当生态系统刚刚好的时候，Docker 就是火上浇油，创造封闭系统的燃料。可以随时停止和启动的系统，其资源利用率仅为虚拟机的一小部分。

总的来说，我们已经了解到容器是一种奇特的 Linux 进程，其中隐藏了正常的系统环境。我们已经看到了名称空间如何隐藏或显示系统资源以及 cgroups 如何保护这些资源的演示。“容器”只是 Linux 数据结构的组合，实际的定义是松散的。