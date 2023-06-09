# 驯服容器中的蛇

> 原文：<https://dev.to/apcelent/taming-snakes-inside-a-container-36c6>

在这篇文章中，让我们来谈谈如何驯服容器中的蛇。本文总结了在整理 python 微服务时学到的经验。如果你想看 Docker 和 Python 微服务的详细实现，请参考我们已经发表的这篇文章[。](https://blog.apcelent.com/setup-microservices-architecture-in-python-with-zeromq-docker.html)

[![Alt text of image](img/7d2d92cc33c98b37780a0866252e3e92.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--m51xxHgk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.apcelent.cimg/python-docker-container-apcelent.png)

我们使用容器的主要原因是

*   容器的内存占用比虚拟机少
*   容器提供了一个隔离的用户空间实例，而不是机器仿真

构建新容器和设置环境很容易。这样创建的图像可以共享并发布到私有/公共注册中心，并且随时可以使用。每当新的开发人员开始在代码库上工作时，这就提供了统一的工作流。

## 用容器构建 python 图像

*   我们不需要创建额外的虚拟环境层，因为每个容器本身都是唯一的，因此不需要额外创建虚拟环境。不同的微服务可以放在不同的容器中。
*   可以使用类似 NGINX 的外部代理，或者为 NGINX 部署一个单独的容器。

## 调试 Python 容器

*   在正在运行的服务上运行 bash(或任何其他命令)很容易

 `docker compose exec $SERVICE_NAME /bin/bash` 

*   标准输出和错误的日志检查

 `docker compose logs $SERVICENAME` 

*   低级的信息可以通过

 `docker inspect $SERVICENAME` 

## 坚持不懈

一旦正在运行的容器关闭，更改就会丢失。为了克服同样的问题，部署的策略是

*   数据卷到外部主机
*   另一种模式是使用单独的 Docker 容器来保存数据

## 测试&工装

*   Docker 为您的 CI 环境增加了一致性。
*   对容器中内容的控制意味着可重复的工作流和更简单的测试环境。
*   有 docker 支持的基于云的 CI 选项。
*   Docker Hub 可以充当 CI 服务器，因为您可以配置它，以便在每次向 Github 提交新的提交时创建一个自动构建。
*   Docker 的内部 API 可能因版本不同而有很大差异，因此建议使用可用的工具。
*   使用 Docker API 的另一种 Pythonic 方式是使用 [docker-py](https://github.com/docker/docker-py) 。

## 部署&缩放

*   负载均衡- [HA 代理](http://www.haproxy.org/)， [NGINX](https://www.nginx.com/)
*   预备- [负责人](https://www.ansible.com/)、[主厨](https://www.chef.io/chef/)、[傀儡](https://puppet.com/)
*   监控- [Nagios](https://www.nagios.org/) ， [Pingdom](https://www.pingdom.com/) ，[新遗迹](https://newrelic.com/)
*   测井- [流体](https://www.fluentd.org/guides/recipes/docker-logging)T2】测井
*   云基础设施- [Swarm](https://www.docker.com/products/docker-swarm) ， [Kubernetes](https://kubernetes.io/) ， [Magnum](https://docs.openstack.org/magnum/latest/) ， [CoreOS Fleet](https://github.com/coreos/fleet)

希望总结和链接对你有所帮助！请在评论中告诉我们你还想让我们写些什么。

这篇文章最初出现在 [Apcelent 科技博客](https://blog.apcelent.com/taming-snakes-inside-container.html)上。