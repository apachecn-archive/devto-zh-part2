# 介绍多集群控制器

> 原文：<https://dev.to/admiralty/introducing-multicluster-controller-39f1>

[*最初发表于金钟的博客。*T3】](https://admiralty.io/blog/introducing-multicluster-controller/)

混合和多云架构正在变得普遍，或者作为一种[战略](https://thenewstack.io/why-enterprises-are-adopting-a-multicloud-strategy/)，或者仅仅是由于[的历史和/或并购](https://www.reddit.com/r/devops/comments/91afzz/why_multicloud/e2x156y/)。幸运的是，为了帮助降低固有的复杂性，Kubernetes 正在标准化云的操作方式:相同的工作流程可以用于管理任何云中的资源，无论是公共云还是私有云。然而，跨云管理工作负载*仍然是一个挑战。从技术上来说，您可以*创建一个包含您整个基础设施的单一 Kubernetes 集群，但这可能会使在 Kubernetes 本身的设计中做出的一些[假设](https://kubernetes.io/docs/concepts/cluster-administration/federation/#scope-of-a-single-cluster)无效。此外，你会错过[交钥匙 Kubernetes 发行](https://kubernetes.io/docs/setup/pick-right-solution/)。更常见的方法是操作多个集群。**

默认情况下，集群相互隔离，这有助于:

*   故障隔离；
*   信任边界；
*   只为生产中的顶层服务级别付费；
*   执行地理法规；
*   等等。

但是，当您想要全局管理以下内容时，集群边界会成为障碍:

*   调度和自动扩展(以最低成本确保高可用性和低延迟)；
*   服务发现；
*   存储；
*   监控；
*   备份和迁移；
*   政策执行；
*   等等。

我们需要工具来跨集群管理资源。有具体的解决办法。值得注意的是， [federation-v2](https://github.com/kubernetes-sigs/federation-v2) 可以跨集群同步工作负载和路由流量。为此，它使用了模板、位置和覆盖的概念，通过推送协调器传播资源。

当在 [Admiralty (stay tuned)](https://admiralty.io) 构建多集群调度程序时，我们需要一个较低层次的抽象:即控制器模式(有时称为操作者模式)，但用于多个集群中的资源。我们需要一个像[运营商 SDK](https://github.com/operator-framework/operator-sdk) 或者 [Kubebuilder](https://github.com/kubernetes-sigs/kubebuilder) 这样的工具(参见之前博客文章中的[对比)，但是要支持多个集群。不幸的是，他们的设计不允许这样。他们的 API 将不得不做出重大改变。因此，我们决定制作自己的工具，而不是提交一个拉请求。幸运的是，我们能够利用](https://admiralty.io/blog/kubernetes-custom-resource-controller-and-operator-development-tools/)[控制器运行时](https://github.com/kubernetes-sigs/controller-runtime)的一部分，这个库支持 Kubebuilder，现在也支持 Operator SDK。

**今天，我们正在开源[多集群控制器](https://github.com/admiraltyio/multicluster-controller)。**查看自述文件，了解其工作原理的更多细节，包括如何与定制资源一起使用(使用 CRDs)。我们还包括了一些例子。我们希望社区会发现该项目是有用的。(有人自愿建立一个多集群普罗米修斯运营商吗？)

## ![GitHub logo](../Images/a73f630113876d78cff79f59c2125b24.png) [提奥](https://github.com/admiraltyio) / [多集群控制器](https://github.com/admiraltyio/multicluster-controller)

### 用于构建混合和多云 Kubernetes 运算符的库

<article class="markdown-body entry-content container-lg" itemprop="text">

# 多集群控制器

Multicluster-controller 是一个 Go 库，用于构建需要在多个集群中监视资源的 Kubernetes 控制器。它使用了[控制器-运行时](https://github.com/kubernetes-sigs/controller-runtime)(支持 [kubebuilder](https://github.com/kubernetes-sigs/kubebuilder) 和现在的 [operator-sdk](https://github.com/operator-framework/operator-sdk) 的库)的最佳部分，并替换了它的 API(即`manager`、`controller`、`reconcile`和`handler`包)来支持多集群操作。

为什么？查看海军部介绍多集群控制器的博客文章。

## 目录

*   [工作原理](https://raw.githubusercontent.com/admiraltyio/multicluster-controller/master/#how-it-works)
*   [入门](https://raw.githubusercontent.com/admiraltyio/multicluster-controller/master/#getting-started)
*   [配置](https://raw.githubusercontent.com/admiraltyio/multicluster-controller/master/#configuration)
*   [使用自定义资源](https://raw.githubusercontent.com/admiraltyio/multicluster-controller/master/#usage-with-custom-resources)
*   [API 引用](https://raw.githubusercontent.com/admiraltyio/multicluster-controller/master/#api-reference)

## 它是如何工作的

这是一个最小的多集群控制器，它监视两个集群中的 pod。在 pod 事件中，它只是记录 pod 的集群名称、名称空间和名称。在某种程度上，这个控制器唯一控制的是标准输出，但它说明了一个基本的脚手架:

```
package main
import (
    "context"
    "log"
    "k8s.io/api/core/v1"
    "k8s.io/sample-controller/pkg/signals"
    "admiralty.io/multicluster-controller/pkg/cluster"
    "admiralty.io/multicluster-controller/pkg/controller"
    "admiralty.io/multicluster-controller/pkg/manager"
    "admiralty.io/multicluster-controller/pkg/reconcile"
    "admiralty.io/multicluster-service-account/pkg/config"
)
func main() {
    stopCh := signals.SetupSignalHandler()
    ctx, cancel
```

…</article>

[View on GitHub](https://github.com/admiraltyio/multicluster-controller)

*警告:*尽管我们已经在内部使用 multicluster-controller 并取得了巨大成功，但该项目仍处于起步阶段，API 可能会在未来的版本中出现问题。此外，一些必备功能仍在开发中:

*   跨集群[垃圾收集](https://kubernetes.io/docs/concepts/workloads/controllers/garbage-collection/)；
*   与[集群注册表](https://github.com/kubernetes/cluster-registry)集成；
*   服务账户和 RBAC 发电机；
*   验证和变异[web hooks](https://book.kubebuilder.io/beyond_basics/what_is_a_webhook.html)；
*   [领袖选举](https://godoc.org/k8s.io/client-go/tools/leaderelection)；
*   更多测试。

欢迎投稿、[功能请求和 bug 报告](https://github.com/admiraltyio/multicluster-controller/issues)。

## 鸣谢

非常感谢所有 Kubernetes 的作者，特别是那些[控制器-运行时](https://github.com/kubernetes-sigs/controller-runtime/graphs/contributors)、 [apimachinery](https://github.com/kubernetes/apimachinery/graphs/contributors) 和[客户端-运行时](https://github.com/kubernetes/client-go/graphs/contributors)的作者。