# 在 Kubernetes 经营一个现代化的基础设施

> 原文：<https://dev.to/p_chhetri/running-a-modern-infrastructure-on-kubernetes-1end>

在斯塔沙威，我们从第一天就开始经营码头集装箱。最初，我们使用 Rancher 作为容器协调器，但随着我们的发展，我们决定改用 Kubernetes(k8s)——主要是因为它的生态系统迅速发展，并被广泛采用。

这篇文章描述了我们如何使用 k8s 及其工具栈在生产级环境中运行我们的应用程序。

<figure>[![](../Images/bad76bdd6a52883af361b361bb4257d4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--L2dPRExq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AG9OVxt8KHclvgfedgmyV-Q.png) 

<figcaption>谷歌趋势图显示了人们对各种集装箱调度程序的兴趣如何随时间变化。</figcaption>

</figure>

所有应用程序，无论是无状态的还是有状态的，都需要一个内置了这些基本要素的环境:

### 服务发现

集装箱本质上是有弹性的，它们可以随时上升和下降。因为每个容器都有一个动态 IP 地址，所以每个容器实例的注册是必须的，这样其他人就可以与它通信。Kubernetes 支持两种发现模式:环境变量和基于 DNS 的。

例如，如果你在一个容器中运行 Cassandra，它的 IP 地址既可以作为一个环境变量 *CASSANDRA_HOST* ，也可以作为一个域名*Cassandra . default . SVC . cluster . local*。

基于 DNS 的服务发现在这两者中更受欢迎，但是需要特别注意，因为一些 DNS 客户端库设置了较高的 DNS 缓存 TTL 值。例如 [JVM 默认永久缓存域名](https://docs.oracle.com/javase/8/docs/technotes/guides/net/properties.html)。

### 服务寻址

如上所示的基于 DNS 的发现名称只能从 Kubernetes 集群内部解析。为了从集群外部处理服务，需要向第三方 DNS 提供商(如 AWS Route53、Google CloudDNS、AzureDNS、CloudFlare)自动注册 DNS。为了减轻对单个 DNS 提供商的依赖，您应该考虑在多个提供商上托管您的区域。

在 k8s 世界中，这可以通过使用[外部](https://github.com/kubernetes-incubator/external-dns)的[注释](https://kubernetes.io/docs/concepts/overview/working-with-objects/annotations/)轻松实现。这个孵化器项目负责在创建任何新的 k8s 服务或入口控制器时注册一个新的(子)域。它还通过一个额外的 TXT 记录以及主 A 记录来了解它所管理的记录，从而防止现有记录的任何意外覆盖。

### 路由

随着大量容器和服务的出现和消失，将外部流量路由到健康的容器是一个挑战。 [Kubernetes Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/) 是救星。它提供负载平衡、SSL 终端，甚至基于名称的路由。Ingress 只是一个抽象层，可以使用任何软件负载平衡器作为其实现。

目前的 Ingress 控制器实现包括 [Nginx Ingress](https://github.com/kubernetes/ingress-nginx) (基于 Nginx)[Voyager](https://github.com/appscode/voyager)(基于 HAProxy)和 [Contour](https://github.com/heptio/contour) (基于 EnvoyProxy)。第一种是最成熟的，我们(与 ELB 一起)将其用于所有流量路由，但它仅提供基于 HTTP 的路由。对于基于 TCP 的路由，您需要使用 Voyager。Contour 非常有趣，因为它具有 Envoy 的所有优点，Envoy 是一种专门为现代云原生应用程序设计的服务代理。它具有对 gRPC 的一流支持，并提供标准负载平衡器所不具备的断路等功能。

### 监控

许多 Kubernetes 对象(如 pods、services 和 ingresses)一起定义了应用程序，因此监视它们中每一个的状态是很重要的。

Prometheus 绝对是开源软件中监控 Kubernetes 应用和集群的正确选择。对于这些 k8s 物体，它有一个内置的[发现](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#<kubernetes_sd_config)。由于没有警报的监控是没有用的， [Alertmanager](https://github.com/prometheus/alertmanager) 通过提供良好的集成，如 Slack 通知，完美地填补了这一空白。

大多数人使用 Prometheus 和 [Heapster](https://github.com/kubernetes/heapster) ，后者可以与许多开源监控解决方案集成，如 InfluxDB 和 Riemann。那些想要获得更好的容器级度量的人也可以将 [cAdvisor](https://github.com/google/cadvisor) 添加到他们的监控栈中。

### 测井

当您运行同一个映像的多个实例时，您不能登录到每个容器并跟踪日志。每个 k8s 节点都需要运行一个代理来推送这些容器日志。令人惊讶的是，在 k8s 世界中，EFK 栈比麋鹿栈更受欢迎。

[Fluent-bit](http://fluentbit.io/) 是一个轻量级的(替代 [Fluentd](https://www.fluentd.org/) )代理，是一个完全 Docker 和 Kubernetes 感知的代理，可用于将这些日志直接推送到 Elasticsearch。它会自动在每个日志行中添加 kubernetes 标签和注释。您还可以将其与 Slack 集成，以便在出现任何错误/异常时发送通知。

### 正在部署

我们将所有的应用程序捆绑成 Docker 映像及其依赖项。为了保持部署管道的整洁，对这些清单进行模板化非常重要。k8s 的软件包管理器 Helm 是在 Kubernetes 上部署应用程序的一个好方法。有许多社区管理的赫尔姆[图表](https://github.com/kubernetes/charts)是稳定的，并准备在生产中使用。

由于 Helm 没有提供一种简洁的方式来存储秘密，我们使用 [Ansible Vault](http://docs.ansible.com/ansible/2.4/vault.html) 作为他们的真实来源。我们使用 [ansible-helm](https://gist.github.com/chhetripradeep/12cd9f0b94572cede89e18502b84ced1) 模块通过 Ansible 触发 helm 命令行。

helm 的一个痛点是，有人需要先了解每个 YAML 场，然后才能写这些图表。Ksonnet 将通过按需动态生成舵图的方式[移除它](https://blog.heptio.com/the-next-chapter-for-ksonnet-1dcbbad30cb)。

### SSL 证书

几乎 90%的现代应用程序都公开了 HTTP 端点。为了保护这些 HTTP 服务，基本要求是安装一个 SSL 证书来实现加密通信。

如果没有正确地自动化，供应、安装和更新这些证书会变得很麻烦。

通过 [kube-cert-manager](https://github.com/PalmStoneGames/kube-cert-manager) 可以自动提供 k8s 入口的 [Let's Encrypt](https://letsencrypt.org/) 证书。我们选择这个而不是 [kube-lego](https://github.com/jetstack/kube-lego) ，因为它支持基于验证挑战的加密 DNS。因此，它可以用于为托管在专用网络中的应用程序颁发证书。它还负责更新这些证书。

JetStack 的人正在开发另一个名为 [cert-manager](https://github.com/jetstack/cert-manager) 的工具，这非常有趣，因为它很快就可以使用 [Hashicorp Vault](https://www.vaultproject.io/) 作为 CA 机构。

### 结论

在这篇文章中，我们讨论了为什么你应该为你的下一个项目选择 Kubernetes。我们将在接下来的博文中深入探讨其中的几个话题。

*我们一直在寻找优秀的技术人才加入我们的团队—* [*访问我们的网站*](https://www.stashaway.sg) *了解更多信息，并随时联系我们！*

* * *