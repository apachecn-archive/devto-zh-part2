# Kubernetes 的现代化应用

> 原文：<https://dev.to/digitalocean/modernizing-applications-for-kubernetes-1hon>

### 简介

现代的无状态应用程序被构建和设计为在 Docker 这样的软件容器中运行，并由 Kubernetes 这样的容器集群管理。它们是使用[云原生](https://github.com/cncf/toc/blob/master/DEFINITION.md)和[十二因素](https://12factor.net/)原则和模式开发的，以最小化人工干预，最大化可移植性和冗余性。将基于虚拟机或裸机的应用程序迁移到容器中(称为“容器化”)并在集群中部署它们通常会涉及这些应用程序的构建、打包和交付方式的重大转变。

基于[为 Kubernetes](https://www.digitalocean.com/community/tutorials/architecting-applications-for-kubernetes) 设计应用程序，在本概念指南中，我们将讨论应用程序现代化的高级步骤，最终目标是在 Kubernetes 集群中运行和管理应用程序。虽然您可以在 Kubernetes 上运行像数据库这样的有状态应用程序，但本指南主要关注无状态应用程序的迁移和现代化，将持久数据卸载到外部数据存储中。Kubernetes 为高效管理和扩展无状态应用程序提供了高级功能，我们将探索在 Kubernetes 上运行可扩展、可观察和可移植的应用程序所需的应用程序和基础设施变化。

## 准备迁移应用程序

在容器化您的应用程序或编写 Kubernetes Pod 和部署配置文件之前，您应该实现应用程序级别的更改，以最大化您的应用程序在 Kubernetes 中的可移植性和可观察性。Kubernetes 是一个高度自动化的环境，可以自动部署和重启失败的应用程序容器，因此构建适当的应用程序逻辑以与容器编制器通信并允许它在必要时自动扩展您的应用程序非常重要。

### 提取配置数据

首先要实现的应用程序级变化之一是从应用程序代码中提取应用程序配置。配置由随部署和环境变化的任何信息组成，如服务端点、数据库地址、凭证以及各种参数和选项。例如，如果您有两个环境，比如说`staging`和`production`，并且每个环境都包含一个单独的数据库，那么您的应用程序不应该在代码中显式声明数据库端点和凭证，而是存储在一个单独的位置，要么作为运行环境中的变量，要么作为本地文件，要么作为外部键值存储，从这些地方将值读入应用程序。

将这些参数硬编码到您的代码中会带来安全风险，因为这些配置数据通常包含敏感信息，然后您将这些信息签入到您的版本控制系统中。这也增加了复杂性，因为您现在必须维护应用程序的多个版本，每个版本都包含相同的核心应用程序逻辑，但在配置上略有不同。随着应用程序及其配置数据的增长，将配置硬编码到应用程序代码中很快变得难以操作。

通过从您的应用程序代码中提取配置值，而不是从运行环境或本地文件中获取它们，您的应用程序就成为了一个通用的、可移植的包，只要您为它提供相应的配置数据，它就可以部署到任何环境中。像 Docker 这样的容器软件和像 Kubernetes 这样的集群软件都是围绕这种模式设计的，内置了管理配置数据的特性，并将它注入到应用程序容器中。这些功能将在[集装箱化](https://www.digitalocean.com/community/tutorials/modernizing-applications-for-kubernetes#inject-configuration)和 [Kubernetes](https://www.digitalocean.com/community/tutorials/modernizing-applications-for-kubernetes#injecting-configuration-data-with-kubernetes) 章节中详细介绍。

这里有一个简单的例子，演示了如何从一个简单的 Python [Flask](http://flask.pocoo.org/) 应用程序的代码中外部化两个配置值`DB_HOST`和`DB_USER`。我们将使它们在应用程序的运行环境中作为 env 变量可用，应用程序将从中读取它们:

**hardcoded_config.py** :

```
from flask import Flask

DB_HOST = 'mydb.mycloud.com'
DB_USER = 'sammy'

app = Flask( __name__ )

@app.route('/')
def print_config():
    output = 'DB_HOST: {} -- DB_USER: {}'.format(DB_HOST, DB_USER)
    return output 
```

运行这个简单的应用程序(参考 [Flask Quickstart](http://flask.pocoo.org/docs/1.0/quickstart/) 了解如何操作)并访问它的 web 端点将显示一个包含这两个配置值的页面。

现在，这是一个将配置值外化到应用程序运行环境的相同示例:

**env _ config . py**T2】

```
import os

from flask import Flask

DB_HOST = os.environ.get('APP_DB_HOST')
DB_USER = os.environ.get('APP_DB_USER')

app = Flask( __name__ )

@app.route('/')
def print_config():
    output = 'DB_HOST: {} -- DB_USER: {}'.format(DB_HOST, DB_USER)
    return output 
```

在运行应用程序之前，我们在本地环境中设置必要的配置变量:

```
 export APP_DB_HOST=mydb.mycloud.com

export APP_DB_USER=sammy

flask run 
```

显示的 web 页面应该包含与第一个示例相同的文本，但是现在可以独立于应用程序代码修改应用程序的配置。您可以使用类似的方法从本地文件中读入配置参数。

在下一节中，我们将讨论将应用程序状态移出容器。

### 卸载应用状态

云原生应用运行在容器中，由 Kubernetes 或 Docker Swarm 等集群软件动态编排。给定的应用或服务可以跨多个副本进行负载平衡，任何单独的应用容器都应该能够出现故障，对客户端的服务中断最小或没有中断。为了实现这种水平的、冗余的扩展，应用程序必须以无状态的方式设计。这意味着它们响应客户端请求，而不在本地存储持久的客户端和应用程序数据，并且在任何时间点，如果正在运行的应用程序容器被破坏或重启，关键数据不会丢失。

例如，如果您正在运行一个地址簿应用程序，并且您的应用程序在地址簿中添加、删除和修改联系人，则地址簿数据存储应该是一个外部数据库或其他数据存储，并且容器内存中保存的唯一数据本质上应该是短期的，并且是可处置的，不会丢失重要的信息。跨用户访问(如会话)持续的数据也应该移动到外部数据存储(如 Redis)。只要有可能，您应该将应用程序中的任何状态卸载到托管数据库或缓存等服务中。

对于需要持久数据存储的有状态应用程序(如复制的 MySQL 数据库)，Kubernetes 内置了将持久块存储卷附加到容器和 pod 的特性。为了确保 Pod 可以在重新启动后保持状态并访问同一个持久性卷，必须使用 StatefulSet 工作负载。StatefulSets 非常适合将数据库和其他长期运行的数据存储部署到 Kubernetes。

无状态容器实现了最大的可移植性，并充分利用了可用的云资源，允许 Kubernetes 调度程序快速缩放您的应用程序，并在任何资源可用的地方启动 Pods。如果您不需要 StatefulSet 工作负载提供的稳定性和排序保证，您应该使用部署工作负载来管理和扩展您的应用程序。

要了解更多关于无状态云原生微服务的设计和架构，请参考我们的 [Kubernetes 白皮书](http://assets.digitalocean.com/white-papers/running-digitalocean-kubernetes.pdf)。

### 实施健康检查

在 Kubernetes 模型中，可以依靠集群控制平面来修复损坏的应用程序或服务。它通过检查应用程序容器的健康状况，重新启动或重新安排不健康或无响应的容器来做到这一点。默认情况下，如果您的应用程序容器正在运行，Kubernetes 会认为您的 Pod 是“健康的”在许多情况下，这是运行中的应用程序健康状况的可靠指标。但是，如果您的应用程序被死锁，没有执行任何有意义的工作，应用程序进程和容器将继续无限期运行，默认情况下，Kubernetes 将保持停滞的容器活动。

为了将应用程序健康状况正确地传达给 Kubernetes 控制平面，您应该实现定制的应用程序健康状况检查，以指示应用程序何时正在运行并准备好接收流量。第一种健康检查称为**就绪探测**，它让 Kubernetes 知道您的应用程序何时准备好接收流量。第二种类型的检查称为**活性探测器**，它让 Kubernetes 知道您的应用程序何时是健康的和运行的。Kubelet 节点代理可以使用 3 种不同的方法在运行的 pod 上执行这些探测:

*   HTTP:kube let 探针对一个端点(如`/health`)执行 HTTP GET 请求，如果响应状态在 200 和 399 之间，则成功
*   容器命令:Kubelet 探针在运行的容器内部执行一个命令。如果退出代码为 0，则探测成功。
*   TCP:kube let 探针试图在指定的端口上连接到您的容器。如果可以建立 TCP 连接，则探测成功。

您应该根据运行的应用程序、编程语言和框架来选择合适的方法。就绪和活性探测可以使用相同的探测方法并执行相同的检查，但是包含就绪探测将确保在探测开始成功之前，Pod 不会接收流量。

当计划和考虑将您的应用程序容器化并在 Kubernetes 上运行时，您应该分配计划时间来定义“健康”和“就绪”对您的特定应用程序意味着什么，并分配开发时间来实现和测试端点和/或检查命令。

下面是上面提到的烧瓶示例的最小健康端点:

**env _ config . py**T2】

```
. . .  
@app.route('/')
def print_config():
    output = 'DB_HOST: {} -- DB_USER: {}'.format(DB_HOST, DB_USER)
    return output

@app.route('/health')
def return_ok():
    return 'Ok!', 200 
```

检查该路径的 Kubernetes 活跃度探测器将如下所示:

**下的 _spec.yaml**

```
. . .
  livenessProbe:
      httpGet:
        path: /health
        port: 80
      initialDelaySeconds: 5
      periodSeconds: 2 
```

`initialDelaySeconds`字段指定 Kubernetes(特别是节点 Kubelet)应该在等待 5 秒后探测`/health`端点，而`periodSeconds`告诉 Kubelet 每 2 秒探测一次`/health`。

要了解更多关于活性和就绪性探针的信息，请参考 Kubernetes 文档。

### 用于记录和监控的仪器代码

在 Kubernetes 这样的环境中运行容器化的应用程序时，发布遥测和日志数据以监控和调试应用程序的性能是非常重要的。内置发布性能指标(如响应持续时间和错误率)的特性将有助于您监控应用程序，并在应用程序不正常时向您发出警报。

你可以使用一个工具来监控你的服务，那就是由云本地计算基金会(CNCF)托管的开源系统监控和警报工具包 [Prometheus](https://prometheus.io/) 。Prometheus 提供了几个客户端库，用于使用各种度量类型对您的代码进行检测，以计算事件及其持续时间。例如，如果您使用 Flask Python 框架，您可以使用 Prometheus [Python 客户端](https://github.com/prometheus/client_python)向您的请求处理函数添加装饰器，以跟踪处理请求所花费的时间。这些指标可以由 Prometheus 在 HTTP 端点(如`/metrics`)上收集。

在设计应用程序的工具时，一个有用的方法是 RED 方法。它由以下三个关键请求指标组成:

*   Rate:应用程序收到的请求数
*   Errors:应用程序发出的错误数
*   持续时间:应用程序提供响应所需的时间

这个最小的度量集应该为您提供足够的数据，以便在您的应用程序性能下降时发出警报。实现这个工具以及上面讨论的健康检查将允许您快速检测失败的应用程序并从中恢复。

要了解更多关于监控应用时要测量的信号，请参考 Google 网站可靠性工程书籍中的[监控分布式系统](https://landing.google.com/sre/book/chapters/monitoring-distributed-systems.html#xref_monitoring_golden-signals)。

除了考虑和设计用于发布遥测数据的功能，您还应该计划您的应用程序将如何在基于集群的分布式环境中进行记录。理想情况下，您应该删除对本地日志文件和日志目录的硬编码配置引用，而是直接记录到 stdout 和 stderr。您应该将日志视为连续的事件流，或者按时间顺序排列的事件序列。然后，这个输出流将被封装您的应用程序的容器捕获，从那里它可以被转发到一个日志记录层，如 EFK (Elasticsearch，Fluentd 和 Kibana)堆栈。Kubernetes 在设计日志架构方面提供了很大的灵活性，我们将在下面详细探讨。

### 在 API 中构建管理逻辑

一旦您的应用程序被容器化，并在像 Kubernetes 这样的集群环境中运行，您可能就不再能够通过 shell 访问运行您的应用程序的容器。如果您已经实现了足够的健康检查、日志记录和监控，那么您可以很快得到警告并调试生产问题，但是除了重新启动和重新部署容器之外，采取其他措施可能会很困难。对于快速的操作和维护修复，如刷新队列或清除缓存，您应该实现适当的 API 端点，这样您就可以执行这些操作，而不必重启容器或进入正在运行的容器并执行一系列命令。容器应该被视为不可变的对象，在生产环境中应该避免手动管理。如果您必须执行一次性的管理任务，如清除缓存，您应该通过 API 公开此功能。

### 总结

在这几节中，我们讨论了在将应用程序打包并转移到 Kubernetes 之前，您可能希望实现的应用程序级更改。要更深入地了解构建云原生应用，请参考[为 Kubernetes](https://www.digitalocean.com/community/tutorials/architecting-applications-for-kubernetes) 设计应用。

现在，我们将讨论在为您的应用程序构建容器时需要牢记的一些注意事项。

## 容器化您的应用程序

现在，您已经实现了应用程序逻辑，以在基于云的环境中最大化其可移植性和可观察性，是时候将您的应用程序打包到容器中了。出于本指南的目的，我们将使用 Docker 容器，但是您应该使用最适合您的生产需求的容器实现。

### 明确声明依赖关系

在为您的应用程序创建 docker 文件之前，首先要做的一件事是评估您的应用程序正确运行所需的软件和操作系统依赖关系。Dockerfiles 允许您显式地对安装到映像中的每个软件进行版本控制，您应该通过显式地声明父映像、软件库和编程语言版本来利用这一特性。

尽可能避免`latest`标签和未版本化的包，因为它们可能会移动，潜在地破坏你的应用程序。您可能希望创建一个私有注册中心或公共注册中心的私有镜像，以便对映像版本进行更多的控制，并防止上游更改无意中破坏您的映像构建。

要了解更多关于设置私有映像注册中心的信息，请参考 Docker 官方文档中的[部署注册服务器](https://docs.docker.com/registry/deploying/)和下面的[注册中心](https://www.digitalocean.com/community/tutorials/modernizing-applications-for-kubernetes#publish-image-to-a-registry)部分。

### 保持图像尺寸较小

在部署和提取容器映像时，大型映像会显著降低速度并增加带宽成本。将一组最少的工具和应用程序文件打包到映像中有几个好处:

*   缩小图像尺寸
*   加速图像构建
*   减少集装箱启动延迟
*   加快图像传输速度
*   通过减少攻击面来提高安全性

构建映像时，您可以考虑以下步骤:

*   使用像`alpine`这样的最小基础操作系统映像或者从`scratch`开始构建，而不是像`ubuntu`这样的全功能操作系统
*   安装软件后清理不必要的文件和工件
*   使用单独的“构建”和“运行时”容器来保持生产应用程序容器较小
*   在大目录中复制时，忽略不必要的构建工件和文件

关于优化 Docker 容器的完整指南，包括许多说明性的例子，请参考[为 Kubernetes](https://www.digitalocean.com/community/tutorials/building-optimized-containers-for-kubernetes) 构建优化的容器。

### 注入配置

Docker 提供了几个有用的特性，可以将配置数据注入到应用程序的运行环境中。

这样做的一个选择是使用`ENV`语句在 Dockerfile 中指定环境变量及其值，以便将配置数据内置到映像中:

**Dockerfile**

```
...
ENV MYSQL_USER=my_db_user
... 
```

然后，您的应用程序可以从其运行环境中解析这些值，并适当地配置其设置。

当使用`docker run`和`-e`标志:
启动一个容器时，也可以传入环境变量作为参数

```
docker run -e MYSQL_USER='my_db_user' IMAGE[:TAG] 
```

最后，您可以使用一个 env 文件，其中包含环境变量及其值的列表。为此，创建文件并使用`--env-file`参数将其传递给命令:

```
docker run --env-file var_list IMAGE[:TAG] 
```

如果您正在更新您的应用程序，以使用像 Kubernetes 这样的集群管理器来运行它，您应该进一步从映像中外部化您的配置，并使用 Kubernetes 的内置 [ConfigMap](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) 和 [Secrets](https://kubernetes.io/docs/concepts/configuration/secret/) 对象来管理配置。这允许您将配置从映像清单中分离出来，以便您可以从应用程序中单独管理和版本化它。要了解如何使用配置映射和机密来具体化配置，请参考下面的[配置映射和机密部分](https://www.digitalocean.com/community/tutorials/modernizing-applications-for-kubernetes#injecting-configuration-data-with-kubernetes)。

### 将图像发布到注册表

一旦构建了应用程序映像，为了使它们对 Kubernetes 可用，应该将它们上传到容器映像注册中心。像 [Docker Hub](https://hub.docker.com) 这样的公共注册中心托管着像 [Node.js](https://hub.docker.com/_/node/) 和 [nginx](https://hub.docker.com/_/nginx/) 这样的流行开源项目的最新 Docker 镜像。私有注册中心允许您发布您的内部应用程序映像，使它们对开发人员和基础设施可用，但不对更广泛的世界可用。

您可以使用现有的基础设施(例如，在云对象存储之上)部署私有注册中心，或者可选地使用几个 Docker 注册中心产品中的一个，如 [Quay.io](https://quay.io/) 或付费 Docker Hub plans。这些注册中心可以与 GitHub 等托管版本控制服务集成，这样当 docker 文件被更新和推送时，注册中心服务将自动提取新的 docker 文件，构建容器映像，并使更新的映像对您的服务可用。

为了对容器映像的构建和测试以及它们的标记和发布进行更多的控制，您可以实现一个持续集成(CI)管道。

### 实现一个构建管道

手动构建、测试、发布您的映像并将其部署到生产环境中很容易出错，并且扩展性不好。要管理构建并将包含最新代码更改的容器连续发布到映像注册表，应该使用构建管道。

大多数构建管道执行以下核心功能:

*   观察源代码库的变化
*   对修改后的代码运行冒烟测试和单元测试
*   构建包含已修改代码的容器映像
*   使用构建的容器映像运行进一步的集成测试
*   如果测试通过，标记并发布图像到注册表
*   (可选，在连续部署设置中)更新 Kubernetes 部署并将映像部署到暂存/生产集群

有许多付费的持续集成产品内置了与 GitHub 等流行的版本控制服务和 Docker Hub 等图像注册中心的集成。这些产品的替代产品是 [Jenkins](https://jenkins.io/) ，这是一个免费的开源构建自动化服务器，可以配置为执行上述所有功能。要了解如何设置 Jenkins 持续集成管道，请参考[如何在 Ubuntu 16.04 上的 Jenkins 中设置持续集成管道](https://www.digitalocean.com/community/tutorials/how-to-set-up-continuous-integration-pipelines-in-jenkins-on-ubuntu-16-04)。

### 实现容器日志记录和监控

当使用容器时，重要的是考虑您将用于管理和存储所有运行和停止的容器的日志的日志基础设施。有多种容器级模式可用于日志记录，也有多种 Kubernetes 级模式。

在 Kubernetes 中，默认情况下，容器使用`json-file` Docker [日志驱动](https://docs.docker.com/config/containers/logging/configure/)，它捕获 stdout 和 stderr 流，并将它们写入运行容器的节点上的 JSON 文件。有时，直接记录到 stderr 和 stdout 对于您的应用程序容器来说可能是不够的，您可能希望将应用程序容器与 Kubernetes Pod 中的日志记录 *sidecar* 容器配对。然后，这个 sidecar 容器可以从文件系统、本地套接字或 systemd 日志中获取日志，这比简单地使用 stderr 和 stdout 流更加灵活。这个容器还可以进行一些处理，然后将丰富的日志流式传输到 stdout/stderr，或者直接传输到日志后端。要了解更多关于 Kubernetes 日志记录模式的信息，请参考本教程的 Kubernetes 日志记录和监控[部分](https://www.digitalocean.com/community/tutorials/modernizing-applications-for-kubernetes#logging-and-monitoring)。

您的应用程序如何在容器级别记录日志将取决于它的复杂性。对于简单的、单一用途的微服务，建议直接记录到 stdout/stderr 并让 Kubernetes 获取这些流，因为您可以利用`kubectl logs`命令从部署了 Kubernetes 的容器中访问日志流。

与日志记录类似，您应该开始考虑在基于容器和集群的环境中进行监控。Docker 提供了有用的`docker stats`命令来获取标准指标，如主机上运行容器的 CPU 和内存使用情况，并通过[远程 REST API](https://docs.docker.com/develop/sdk/) 公开更多指标。此外，开源工具 [cAdvisor](https://github.com/google/cadvisor) (默认安装在 Kubernetes 节点上)提供了更高级的功能，如历史指标收集、指标数据导出，以及用于整理数据的有用的 web UI。

然而，在多节点、多容器的生产环境中，更复杂的度量堆栈，如[普罗米修斯](https://prometheus.io/)和[格拉夫纳](https://grafana.com/)可能有助于组织和监控容器的性能数据。

### 总结

在这些小节中，我们简要地讨论了构建容器、设置 CI/CD 管道和映像注册中心的一些最佳实践，以及提高容器可观察性的一些注意事项。

*   要了解更多关于为 Kubernetes 优化容器的信息，请参考[为 Kubernetes 构建优化容器](https://www.digitalocean.com/community/tutorials/building-optimized-containers-for-kubernetes)。
*   要了解关于 CI/CD 的更多信息，请参考[持续集成、交付和部署介绍](https://www.digitalocean.com/community/tutorials/an-introduction-to-continuous-integration-delivery-and-deployment)和[CI/CD 最佳实践介绍](https://www.digitalocean.com/community/tutorials/an-introduction-to-ci-cd-best-practices)。

在下一节中，我们将探索 Kubernetes 的特性，这些特性允许您在集群中运行和扩展您的容器化应用程序。

## 部署在库伯内特斯

至此，您已经将您的应用程序容器化并实现了逻辑，以最大化其在云原生环境中的可移植性和可观察性。我们现在将探索 Kubernetes 的特性，这些特性提供了简单的界面来管理和扩展 Kubernetes 集群中的应用程序。

### 编写部署和 Pod 配置文件

一旦您将应用程序打包并发布到注册中心，现在就可以使用 Pod 工作负载将它部署到 Kubernetes 集群中。Kubernetes 集群中最小的可部署单元不是容器，而是 Pod。Pods 通常由一个应用程序容器(比如一个容器化的 Flask web app)或者一个应用程序容器和任何执行一些辅助功能(比如监控或日志记录)的“sidecar”容器组成。Pod 中的容器共享存储资源、网络名称空间和端口空间。它们可以使用`localhost`相互通信，也可以使用挂载的卷共享数据。另外，Pod 工作负载允许您定义在主应用程序容器开始运行之前运行设置脚本或实用程序的 [Init 容器](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/)。

pod 通常使用部署来推出，部署是由声明特定期望状态的 YAML 文件定义的控制器。例如，应用程序状态可能正在运行 Flask web app 容器的三个副本，并公开端口 8080。创建后，控制平面通过根据需要将容器调度到节点上，逐渐使群集的实际状态与部署中声明的所需状态相匹配。要扩展集群中运行的应用程序副本的数量，比如从 3 个增加到 5 个，您需要更新部署配置文件的`replicas`字段，然后更新新配置文件的`kubectl apply`。使用这些配置文件，可以使用您现有的源代码控制服务和集成来跟踪和管理伸缩和部署操作。

下面是一个用于 Flask 应用程序的示例 Kubernetes 部署配置文件:

**flask _ deployment . YAML**

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: flask-app
  labels:
    app: flask-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: flask-app
  template:
    metadata:
      labels:
        app: flask-app
    spec:
      containers:
      - name: flask
        image: sammy/flask_app:1.0
        ports:
        - containerPort: 8080 
```

这个部署启动了 3 个 pod，它们运行一个名为`flask`的容器，使用的是端口`8080`打开的`sammy/flask_app`映像(版本`1.0`)。这个部署叫做`flask-app`。

要了解更多关于 Kubernetes Pods 和部署的信息，请参考 Kubernetes 官方文档的[pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/)和[部署](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)部分。

### 配置 Pod 存储

Kubernetes 使用卷、永久卷(PVs)和永久卷声明(PVC)来管理 Pod 存储。卷是用于管理 Pod 存储的 Kubernetes 抽象，支持大多数云提供商块存储产品，以及托管正在运行的 Pod 的节点上的本地存储。要查看支持的卷类型的完整列表，请参考 Kubernetes [文档](https://kubernetes.io/docs/concepts/storage/volumes/#types-of-volumes)。

例如，如果您的 Pod 包含两个需要在它们之间共享数据的 NGINX 容器(比方说第一个，称为`nginx`服务网页，第二个，称为`nginx-sync`从外部位置获取页面并更新由`nginx`容器服务的页面)，那么您的 Pod 规范应该是这样的(这里我们使用 [`emptyDir`](https://kubernetes.io/docs/concepts/storage/volumes/#emptydir) 卷类型):

**pod_volume.yaml**

```
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx
    volumeMounts:
    - name: nginx-web
      mountPath: /usr/share/nginx/html

  - name: nginx-sync
    image: nginx-sync
    volumeMounts:
    - name: nginx-web
      mountPath: /web-data

  volumes:
  - name: nginx-web
    emptyDir: {} 
```

我们为每个容器使用了一个`volumeMount`,表示我们想要在`nginx`容器中的`/usr/share/nginx/html`和`nginx-sync`容器中的`/web-data`挂载包含网页文件的`nginx-web`卷。我们还定义了一个类型为`emptyDir`的`volume`，称为`nginx-web`。

以类似的方式，您可以使用云块存储产品配置 Pod 存储，方法是将`volume`类型从`emptyDir`修改为相关的云存储卷类型。

卷的生命周期与 Pod 的生命周期相关，但与容器的生命周期无关。如果 Pod 中的一个容器失效，该卷将继续存在，新启动的容器将能够装入相同的卷并访问其数据。当一个 Pod 重新启动或终止时，其卷也会随之终止，尽管如果卷由云块存储组成，它们将被简单地卸载，数据仍可由未来的 Pod 访问。

要在 Pod 重新启动和更新时保留数据，必须使用 PersistentVolume (PV)和 PersistentVolumeClaim (PVC)对象。

持久卷是代表诸如云块存储卷或 NFS 存储等持久存储的抽象概念。它们是与 PersistentVolumeClaims 分开创建的，PersistentVolumeClaims 是开发人员对存储块的需求。在他们的 Pod 配置中，开发人员使用 PVCs 请求持久存储，Kubernetes 将这些持久存储与可用的 PV 卷相匹配(如果使用云块存储，Kubernetes 可以在创建 PersistentVolumeClaims 时动态创建持久卷)。

如果您的应用程序要求每个副本有一个持久卷(许多数据库都是这种情况)，则不应使用部署，而应使用 StatefulSet 控制器，该控制器是为要求稳定的网络标识符、稳定的持久存储和排序保证的应用程序而设计的。部署应该用于无状态的应用程序，并且如果您定义了一个 PersistentVolumeClaim 用于部署配置中，该 PVC 将由部署的所有副本共享。

要了解更多关于 StatefulSet 控制器的信息，请参考 Kubernetes [文档](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/)。要了解关于持久卷和持久卷声明的更多信息，请查阅 Kubernetes storage [文档](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)。

### 用 Kubernetes 注入配置数据

与 Docker 类似，Kubernetes 提供了用于在 Pod 配置文件中设置环境变量的`env`和`envFrom`字段。下面是一个 Pod 配置文件的示例片段，它将正在运行的 Pod 中的`HOSTNAME`环境变量设置为`my_hostname`:

**sample_pod.yaml**

```
...
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80
        env:
        - name: HOSTNAME
          value: my_hostname
... 
```

这允许您将配置从 docker 文件中移到 Pod 和部署配置文件中。从 docker 文件中进一步外部化配置的一个关键优势是，您现在可以独立于应用程序容器定义来修改这些 Kubernetes 工作负载配置(比如，将`HOSTNAME`值改为`my_hostname_2`)。一旦修改了 Pod 配置文件，就可以使用它的新环境重新部署 Pod，而底层容器映像(通过其 does 文件定义)不需要重新构建、测试和推送到存储库。您还可以将这些 Pod 和部署配置的版本与 docker 文件分开，从而允许您快速检测重大更改，并进一步将配置问题与应用程序错误分开。

Kubernetes 为进一步外部化和管理配置数据提供了另一种构造:配置映射和秘密。

### 配置图和秘密

ConfigMaps 允许您将配置数据保存为对象，然后在 Pod 和部署配置文件中引用这些对象，以便您可以避免硬编码配置数据，并在 Pod 和部署之间重用这些数据。

这里有一个例子，使用上面的 Pod 配置。我们首先将`HOSTNAME`环境变量保存为一个 ConfigMap，然后在 Pod config:
中引用它

```
kubectl create configmap hostname --from-literal=HOSTNAME=my_host_name 
```

为了从 Pod 配置文件中引用它，我们使用了`valueFrom`和`configMapKeyRef`构造:

**sample _ pod _ config map . YAML**

```
...
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80
        env:
        - name: HOSTNAME
          valueFrom:
            configMapKeyRef:
              name: hostname
              key: HOSTNAME
... 
```

所以`HOSTNAME`环境变量的值已经完全从配置文件中外部化了。然后，我们可以在所有部署和引用它们的 pod 中更新这些变量，并重新启动 pod 以使更改生效。

如果您的应用程序使用配置文件，ConfigMaps 还允许您将这些文件存储为 ConfigMap 对象(使用`--from-file`标志)，然后您可以将这些文件作为配置文件装入容器。

机密提供与 ConfigMaps 相同的基本功能，但是应该用于数据库凭据等敏感数据，因为这些值是 base64 编码的。

要了解关于配置图和秘密的更多信息，请查阅 Kubernetes [文档](https://kubernetes.io/docs/concepts/configuration/)。

### 创建服务

一旦您的应用程序在 Kubernetes 中启动并运行，每个 Pod 将被分配一个(内部)IP 地址，由其容器共享。如果其中一个 pod 被移除或死亡，新启动的 pod 将被分配不同的 IP 地址。

对于向内部和/或外部客户端公开功能的长期运行的服务，您可能希望向执行相同功能(或部署)的一组 pod 授予一个稳定的 IP 地址，以便在其容器之间对请求进行负载平衡。您可以使用 Kubernetes 服务来实现这一点。

Kubernetes 服务有 4 种类型，由服务配置文件中的`type`字段指定:

*   `ClusterIP`:这是默认类型，授予服务一个稳定的内部 IP，可从集群内部的任何地方访问。
*   `NodePort`:这将在每个节点的静态端口上公开您的服务，默认端口在 30000-32767 之间。当一个请求在它的节点 IP 地址和您的服务的`NodePort`到达一个节点时，请求将被负载平衡并路由到您的服务的应用程序容器。
*   `LoadBalancer`:这将使用您的云提供商的负载平衡产品创建一个负载平衡器，并为您的服务配置一个`NodePort`和`ClusterIP`，外部请求将被路由到该服务。
*   `ExternalName`:该服务类型允许您将 Kubernetes 服务映射到 DNS 记录。它可用于使用 Kubernetes DNS 从您的 pod 访问外部服务。

请注意，为集群中运行的每个部署创建一个类型为`LoadBalancer`的服务将会为每个服务创建一个新的云负载平衡器，这可能会非常昂贵。要使用单个负载平衡器管理将外部请求路由到多个服务，可以使用入口控制器。入口控制器超出了本文的范围，但是要了解更多关于它们的信息，你可以参考 Kubernetes [文档](https://kubernetes.io/docs/concepts/services-networking/ingress/)。一个流行的简单入口控制器是 [NGINX 入口控制器](https://github.com/kubernetes/ingress-nginx)。

以下是本指南第节的 pod 和部署[中使用的 Flask 示例的简单服务配置文件:](https://www.digitalocean.com/community/tutorials/modernizing-applications-for-kubernetes#write-deployment-and-pod-configuration-files)

**flask_app_svc.yaml**

```
apiVersion: v1
kind: Service
metadata:
  name: flask-svc
spec:
  ports:
  - port: 80
    targetPort: 8080
  selector:
    app: flask-app
  type: LoadBalancer 
```

这里我们选择使用这个`flask-svc`服务来公开`flask-app`部署。我们创建一个云负载平衡器，将流量从负载平衡器端口`80`路由到公开的容器端口`8080`。

要了解更多关于 Kubernetes 服务的信息，请查阅 Kubernetes 文档的[服务](https://kubernetes.io/docs/concepts/services-networking/service/)部分。

### 记录和监控

随着运行的应用程序数量的增加，使用`kubectl logs`和`docker logs`解析单个容器和 Pod 日志会变得很乏味。为了帮助您调试应用程序或集群问题，您应该实现集中式日志记录。在高层次上，这由运行在所有工作节点上的代理组成，这些工作节点处理 Pod 日志文件和流，用元数据丰富它们，并将日志转发到后端，如 [Elasticsearch](https://github.com/elastic/elasticsearch) 。从那里，可以使用像[基巴纳](https://github.com/elastic/kibana)这样的可视化工具来可视化、过滤和组织日志数据。

在容器级日志部分，我们讨论了推荐的 Kubernetes 方法，让容器中的应用程序记录到 stdout/stderr 流。我们还简要讨论了日志 sidecar 容器，它可以在从应用程序进行日志记录时为您提供更多的灵活性。您还可以直接在 Pods 中运行日志代理，它可以捕获本地日志数据，并将它们直接转发到您的日志后端。每种方法都有其优点和缺点，以及资源利用的权衡(例如，在每个 Pod 中运行一个日志代理容器可能会占用大量资源，并很快淹没您的日志后端)。要了解更多关于不同日志架构及其权衡的信息，请参考 Kubernetes [文档](https://kubernetes.io/docs/concepts/cluster-administration/logging/)。

在标准设置中，每个节点都运行一个日志代理，如 [Filebeat](https://www.elastic.co/products/beats/filebeat) 或 [Fluentd](https://github.com/fluent/fluentd) 来获取 Kubernetes 创建的容器日志。回想一下，Kubernetes 为节点上的容器创建 JSON 日志文件(在大多数安装中，可以在`/var/lib/docker/containers/`找到这些文件)。应该使用 logrotate 之类的工具来旋转它们。节点日志记录代理应该作为 [DaemonSet 控制器](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/)运行，这是一种 Kubernetes 工作负载，确保每个节点都运行一个 DaemonSet Pod 的副本。在这种情况下，Pod 将包含日志记录代理及其配置，它处理装入日志记录 DaemonSet Pod 的文件和目录中的日志。

类似于使用`kubectl logs`调试容器问题的瓶颈，最终您可能需要考虑一个更健壮的选项，而不是简单地使用`kubectl top`和 Kubernetes 仪表板来监控集群上的 Pod 资源使用情况。可以使用 [Prometheus](https://prometheus.io/) 监控系统和时序数据库以及 [Grafana](https://github.com/grafana/grafana) 度量仪表板来设置集群和应用级监控。Prometheus 使用“拉”模型工作，该模型定期从 HTTP 端点(如节点上的`/metrics/cadvisor`，或`/metrics` application REST API 端点)获取度量数据，然后处理并存储这些数据。然后可以使用 Grafana dashboard 对这些数据进行分析和可视化。Prometheus 和 Grafana 可以像任何其他部署和服务一样启动到 Kubernetes 集群中。

为了增加弹性，您可能希望在单独的 Kubernetes 集群上运行您的日志记录和监控基础设施，或者使用外部日志记录和度量服务。

## 结论

对应用程序进行迁移和现代化，以便它能够在 Kubernetes 集群中高效运行，这通常涉及大量的软件和基础设施变更的规划和架构。一旦实施，这些变化允许服务所有者持续部署其应用的新版本，并根据需要轻松扩展它们，只需最少的手动干预。将配置从您的应用中外部化、设置适当的日志记录和指标发布以及配置健康检查等步骤允许您充分利用 Kubernetes 围绕其设计的云原生范式。通过构建可移植容器并使用 Kubernetes 对象(如部署和服务)管理它们，您可以充分利用可用的计算基础设施和开发资源。

* * *

[![CC 4.0 License](img/4662fd03838b335557c183fd57c6b02c.png)T2】](http://creativecommons.org/licenses/by-nc-sa/4.0/)

*本作品根据[知识共享署名-非商业性使用-类似共享 4.0 国际许可证](http://creativecommons.org/licenses/by-nc-sa/4.0/)* 进行许可