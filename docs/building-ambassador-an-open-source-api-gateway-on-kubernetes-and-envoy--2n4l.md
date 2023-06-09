# Building Ambassador，Kubernetes 和 Envoy 上的一个开源 API 网关

> 原文：<https://dev.to/datawireio/building-ambassador-an-open-source-api-gateway-on-kubernetes-and-envoy--2n4l>

API 网关是向消费者公开服务端点的一种流行模式。在 [Datawire](https://www.datawire.io/) ，我们希望通过 API 网关向我们的终端用户公开我们的大量云服务。我们所有的云服务都在 Kubernetes 上运行，所以我们也想在 Kubernetes 上部署 API 网关。最后，我们想要开源的东西。

我们看了一下 [Tyk](https://github.com/TykTechnologies/tyk-kubernetes) 、 [Kong](https://getkong.org/install/kubernetes/) 和其他几个开源 API 网关，发现它们有一个共同的架构——一个持久数据存储(例如 Cassandra、Redis、MongoDB)，一个代理服务器来进行实际的流量管理，以及 REST APIs 来进行配置。虽然所有这些看起来都可行，但我们问自己是否有更简单、更符合 Kubernetes 本地语言的方法。

### 我们的 API 网关愿望清单

我们在白板上潦草地写下了需求:

*   可靠性、可用性、可扩展性。咄。
*   声明性配置。我们致力于配置的声明性模型(这里有一篇关于对比的文章),不喜欢将命令性配置(通过 REST)和声明性配置混合用于我们的操作基础设施的想法。
*   轻松自省。当某些东西不起作用时，我们希望能够自省网关，以找出什么不起作用。
*   好用。
*   认证。
*   性能。
*   现代分布式应用程序所需的所有特性，例如，速率限制、断路、gRPC、可观察性等等。

我们意识到 Kubernetes 给了我们可靠性、可用性和可伸缩性。我们知道[特使代理](https://www.envoyproxy.io/)给了我们想要的性能和特性。所以我们问自己，我们是否可以让 Envoy 和 Kubernetes 结婚，然后填补剩下的空白。

### 大使=特使+ Kubernetes

我们开始编写一些原型代码，与 Kubernetes 社区分享，并根据反馈进行迭代。我们最终有了[开源大使 API 网关](https://www.getambassador.io/)。其核心是 Ambassador 的一个基本功能:它监视对 Kubernetes 清单的配置更改，然后安全地将必要的配置更改传递给 Envoy。所有 L7 网络都由 Envoy 直接执行，Kubernetes 负责可靠性、可用性和可伸缩性。

[![local diagram amb on kube](img/5f8c4d88198d67fc69e9d1e24cc7f2c9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1yoakleS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.datawire.io/wp-content/uploads/2018/02/building-ambassador-photo-1.png)

除了这个核心功能，我们还增加了一些其他核心功能:通过[诊断](https://www.getambassador.io/user-guide/running#diagnostics)用户界面(见上文)进行自省，以及集成了 Envoy 和所有必要的位以使其在生产中运行的单个 Docker 映像(截至`0.23`，它是一个 113MB 的基于 Alpine Linux 的映像)。

[![Ambassador diagnostics interface](img/1d2f363d84b089d057711895f86eefef.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XzQHNuaT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.datawire.io/wp-content/uploads/2018/02/building-ambassador-photo-2.png)

特使和 Kubernetes 的结合使大使能够在短时间内做好生产准备。

*   Ambassador 使用 Kubernetes 实现持久性，因此不需要运行、扩展或维护数据库。(因此，我们不需要测试或调整数据库查询。)
*   缩放大使是由 Kubernetes 完成的，因此您可以使用[水平 pod 自动缩放器](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)或根据需要添加副本。
*   Ambassador 使用 Kubernetes liveness 和 readiness 探测器，因此如果 Kubernetes 检测到问题，它会自动重启 Ambassador。
*   所有实际的 L7 路由都是由 Envoy 完成的，因此我们的性能与 Envoy 相同。(实际上，您可以从 pod 中删除大使代码，而您的特使实例将继续路由流量。)

由于 Envoy 极其强大的功能集，我们能够在短时间内添加速率限制、gRPC 支持、web sockets 等功能。

### Ingress 呢？

我们考虑让大使成为入口控制器。在这个模型中，用户将在 Kubernetes 中定义[入口资源](https://kubernetes.io/docs/concepts/services-networking/ingress/)，然后由 Ambassador ingress 控制器进行处理。在进一步研究了这种方法之后，我们决定不采用这种方法，因为入口资源的特性集*非常*有限。特别是，入口资源只能定义基本的 HTTP 路由规则。我们希望在 Envoy 中使用的许多功能(例如，gRPC、超时、速率限制、CORS 支持、基于 HTTP 方法的路由等。)不可能用入口资源来表达。

### 使用大使

我们的目标是使大使成为 Kubernetes 的习惯用法。安装 Ambassador 就是创建一个 Kubernetes 部署(例如，`kubectl apply -f [https://www.getambassador.io/yaml/ambassador/ambassador-rbac.yaml](https://getambassador.io/yaml/ambassador/ambassador-rbac.yaml)`如果您使用的是 RBAC)并创建一个指向该部署的 Kubernetes 服务。

一旦完成，配置就通过 Kubernetes 注释完成了。这种方法的一个优点是，关于您的服务如何发布的实际元数据都在一个地方——您的 Kubernetes 服务对象。