# Docker 安全最佳实践

> 原文：<https://dev.to/pbnj/docker-security-best-practices-45ih>

[![voodoo container](img/fbfeac38a41f68ee65c1bc717eed8c2b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NxBdHYQw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://upload.wikimedia.org/wikipedia/en/4/41/Stefan_Beese%2527s_Eco_Shipping_Container_Lounge_by_Melissa_Carrier.jpg)

## 目录

*   [概述](#overview)
*   [主持人](#the-host)
*   [Docker 硬化标准](#docker-harden)
*   [Docker 引擎](#docker-engine)
*   [容器权限](#container-privileges)
*   [静态分析](#static-analysis)
*   [运行时安全性](#runtime-security)
*   [结论](#conclusion)

## 

近年来，容器已经彻底改变了科技产业，原因有很多。由于它们将依赖项封装到可移植容器映像中的特性，许多组织已经将它们作为开发、构建和部署生产应用程序的主要方法。

作为一名软件工程师和安全工程师，我甚至无法表达我对容器和编排器(即 Kubernetes)改变游戏规则的潜力的兴奋之情。然而，由于开源的混乱(好的)本质以及项目移动和发展的速度，许多组织根本不能或不具备适当保护这些技术的能力。

本文旨在提供 2018 年常见安全错误和安全最佳实践/建议的列表。

在下一篇文章中，我将为 Kubernetes 提供相同的见解。

**图例:**

| 图标 | 意义 |
| --- | --- |
| -好的 | 不推荐 |
| 🗒️ | 基本原理 |
| 981 号房 | 建议 |

* * *

## 

❌在一台不安全的主机上运行 Docker

🗒️ Docker 的安全性取决于底层主机的安全性

✅:确保你遵循[操作系统安全最佳实践](https://downloads.cisecurity.org/)来强化你的基础设施。如果你向组织中的每一个用户发放访问权限，那么 Docker 有多安全并不重要。

* * *

## 

✅互联网安全中心(CIS)发布了详细说明安全最佳实践、建议和可行步骤的文档，以实现强化基线。最棒的是:它们是免费的。

✅更好的是， [docker-bench-security](https://github.com/docker/docker-bench-security) 是一个基于 CIS 基准的自动检查器。

```
# recommended
$ docker run \
    -it \
    --net host \
    --pid host \
    --userns host \
    --cap-add audit_control \
    -e DOCKER_CONTENT_TRUST=$DOCKER_CONTENT_TRUST \
    -v /var/lib:/var/lib \
    -v /var/run/docker.sock:/var/run/docker.sock \
    -v /usr/lib/systemd:/usr/lib/systemd \
    -v /etc:/etc --label docker_bench_security \
    docker/docker-bench-security 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 

Docker Engine 是一个 API，它侦听传入的请求，并依次与底层主机内核交互以完成其工作。Docker 引擎支持 3 种不同套接字上的通信:`unix`、`tcp`和`fd`。

❌在`tcp`或任何网络套接字上运行 Docker 引擎(又名 Docker 守护进程，又名`dockerd`)

🗒️如果任何人能够到达 Docker 正在监听的网络套接字，他们就有可能访问 Docker，并且因为 Docker 需要以 root 用户身份运行，所以有可能访问底层主机

✅今天默认的 docker 行为是最安全的假设，那就是监听一个`unix`套接字

```
# not recommended
$ dockerd -H "tcp://1.2.3.4:8080"

# recommended
$ dockerd -H "unix:///var/run/docker.sock" 
```

Enter fullscreen mode Exit fullscreen mode

* * *

❌将对接插座安装到集装箱中

将🗒️安装在集装箱内是一种常见但非常危险的做法。攻击者可以执行 docker 服务可以运行的任何命令，这通常会提供对整个主机系统的访问权限，因为 docker 服务以根用户身份运行。

✅只是说“不要安装 docker 插座”，仔细考虑需要这个巨大漏洞的用例。例如，许多在容器中运行 Jenkins master 的教程将指导您安装 docker 套接字，以便 Jenkins 可以启动其他容器来运行您的测试。这是危险的，因为这意味着任何人都可以从 Jenkins 执行任何 shell 命令，从其他容器获得对敏感信息或机密(例如 API 令牌、环境变量)的未经授权的访问，或者启动特权容器并挂载`/etc/shadow`来提取所有用户的密码。

```
# not recommended
$ docker run -it -v /var/run/docker.sock:/var/run/docker.sock ubuntu /bin/bash 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 

❌经营特许集装箱

🗒️容器将拥有对底层主机的完全访问权限

✅如果容器需要，只授予它需要的特定功能

```
# not recommended
$ docker run -d --privileged ubuntu

# recommended
$ docker run -d --cap-add SYS_PTRACE ubuntu 
```

Enter fullscreen mode Exit fullscreen mode

* * *

❌作为根用户运行容器

🗒️这是系统管理标准的最佳实践。几乎没有理由像`root`那样在容器中运行软件。

✅作为非根用户运行容器

```
# not recommended (runtime example)
$ docker run -d ubuntu sleep infinity
$ ps aux | grep sleep
root ... sleep infinity

# recommended (runtime example)
$ docker run -d -u 1000 ubuntu sleep infinity
$ ps aux | grep sleep
1000 ... sleep infinity

# recommended (build-time example)
FROM ubuntu:latest
USER 1000 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 

❌从公共登记处提取并运行集装箱

🗒️最近，安全研究人员在码头中心发现了 17 个密码挖掘集装箱

✅扫描容器映像，以检测并防止具有已知漏洞或恶意软件包的容器部署到您的基础架构中

*   这个领域的开源工具有 [anchore](https://anchore.com/) 和 [clair](https://github.com/coreos/clair)

* * *

✅标志容器图像

*   [Docker 内容信任](https://docs.docker.com/engine/security/trust/content_trust/)保证发布者的完整性和容器图像内容的完整性，从而建立信任

* * *

## 

✅将`seccomp`、`apparmor`或`selinux`剖面贴在您的集装箱上

*   安全配置文件，如 [`seccomp`](https://docs.docker.com/engine/security/seccomp/) 、 [`apparmor`](https://docs.docker.com/engine/security/apparmor/) 和`selinux`在容器周围添加了更强的安全边界，以防止它进行不被明确允许的系统调用

* * *

✅监控、检测异常、可疑和恶意的容器行为，并发出警报

*   这个领域的开源工具包括 [Sysdig Falco](https://github.com/draios/falco)

* * *

✅考虑在容器运行时沙箱中运行容器，比如 [gvisor](https://github.com/google/gvisor)

*   容器运行时沙箱在运行时为你的容器增加了一个更强的安全边界

* * *

## 

容器技术本质上并不比传统虚拟化技术更安全或更不安全。容器依靠 linux 原语实现安全性，例如 uid、GID、名称空间隔离、cgroups 来控制 cpu、内存、PID、io...等等。

如果你觉得我错过了什么，弄错了一些细节，或者只是想打个招呼，请随时在下面留下评论或者在 GitHub 上联系我🐙，[推特🐦](https://twitter.com/petermbenjamin)，或 [LinkedIn🔗](https://www.linkedin.com/in/pmbenjamin)