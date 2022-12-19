# kubernetes & trae fik 101——简单至关重要

> 原文：<https://dev.to/geraldcroes/kubernetes--traefik-101-when-simplicity-matters-6k6>

#### 本可以让我省去几个小时摆弄键盘的教程

[![](../Images/d48bbf7ec5794540258abaa1a5a27ccd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Cjw7yyhE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A8jEVYbc3AEcLcr4pQjmkJg.jpeg)

你有没有遇到过这样的情况，开始于…

> *工作中的某人:*“看，超级简单，我来解释一下……”

…接着是…

> [ …晦涩术语的痛苦…]
> 
> *同样热心的同事:*“看，你拿到了吗？”
> 
> 你(扑克脸):“当然，明白了，啊哈，没问题，哇，超级简单，啊哈……”

…但结果是你偷偷溜进浴室，喋喋不休地说着你几乎不记得的关键词。

> “好吧，谷歌，什么是节点立方体中的入口集群？……请请帮帮我。”

没有。在我身上没发生过。一次也没有。不先生。

但是让我们*假装*几年前它确实发生过(*假设*)，我在研究过程中偶然发现的所有文章都是由如此神秘的符号组成的，以至于我的眼泪开始流出(而且它们不是喜悦的眼泪)。

> 我说:我们假装一下。

因此，基于这种*虚构的*情况，让我们试着编写一个最简单的教程，将 Traefik 设置为 Kubernetes 的入口控制器。(而且我保证:凡是不明显的我都会解释。)

如果你熟悉 Kubernetes，你可以跳过这一部分。

### 你需要了解的 Kubernetes

#### Kubernetes，如在集群中

Kubernetes 是一种集群技术。这意味着您将把一群计算机视为一个实体。您将不会在特定的计算机上部署应用程序，而是在集群中的某个地方部署应用程序，Kubernetes 的工作是确定最符合您的应用程序需求的计算机。

<figure>[![](../Images/05c51e03d7040c1e2a94328fad9f06b2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--geoV4lXK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AqQ-j7P3WoogJgyZlDzxDSQ.png) 

<figcaption>集群中的机器可以有不同的特性</figcaption>

</figure>

#### 节点

群集中的每台计算机称为一个节点。最终，这些节点将托管您的应用程序。这些节点可以分布在世界各地不同的数据中心，Kubernetes 的工作就是让它们像邻居一样交流。

<figure>[![](../Images/60f9683468b9fc19d2b40ac7514f4d06.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sgTUJOL4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A2j_S_hK2c9Xc2Sgd6fPQCA.png) 

<figcaption>节点是集群中的机器</figcaption>

</figure>

#### 容器

我猜你已经对容器很熟悉了。以防万一，它们曾经是虚拟化的下一步，而今天它们只是虚拟化的事实标准。您不需要配置一台机器来托管您的应用程序，而是将您的应用程序包装到一个容器中(以及您的应用程序需要的所有东西—操作系统、库、依赖项等)，并部署在一台托管容器引擎的机器上，该引擎负责实际运行容器。

从技术上讲，集装箱不属于 Kubernetes 它们只是交易的工具之一。

<figure>[![](../Images/168136ccd0129e361dea0e693d904e06.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--u5gV1vOF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A9ODdq3k6CY-xKX5ryRgLtw.png) 

<figcaption>容器嵌入所需技术</figcaption>

</figure>

基本上，Kubernetes 看到的容器如下图所示:

<figure>[![](../Images/003da49a96b255c7ae87a5b7857827b8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Eg88P8zW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AoHF7k5YxREmWZ3HrWF9E3g.png) 

<figcaption>容器引擎负责处理你的容器中需要的底层技术</figcaption>

</figure>

#### 豆荚

因为 Kubernetes 喜欢分层，Kubernetes 在你的容器周围添加了豆荚的概念。单元是您最终将部署到集群的最小单元。一个 Pod 可以容纳多个容器，但是为了简单起见，我们现在假设一个 Pod 将容纳一个容器。

因此，在 Kubernetes 的世界中，Pod 是应用程序实例、服务实例的新名称。Pod 将被托管在节点上，Kubernetes 的工作是确定哪个节点将托管哪个 Pod。

<figure>[![](../Images/36da3227565a4020b0525564bf8c6956.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BEnhH2sf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AJEN9ir7oKMz7EfBtzN5nIQ.png) 

<figcaption>如果一个 Pod 由多个容器组成，它们将共享相同的资源/网络</figcaption>

</figure>

#### 部署

有趣的部分来了！部署是您向 Kubernetes 提出的关于您的应用程序(您的 pod)的要求。基本上，对于部署，您会告诉 Kubernetes:

> 嘿 Kube，始终保持这些 pod 的 5 个实例(或副本)运行——始终。

Kubernetes 的工作是确保您的集群将在任何给定时间托管您的 pod 的 5 个副本。

<figure>[![](../Images/416665568d97a683755bb1aa2766c444.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kMiehTvn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AnXocwqHpCScrQZjgMapeqQ.png) 

<figcaption>Kubernetes 负责将吊舱部署到合适的地方</figcaption>

</figure>

#### 服务

哦，又一个有趣的概念…

豆荚的生命周期是不稳定的；他们是按照库伯内特的意愿来来去去的。

> 不健康？被杀。
> 
> 不在正确的地方？克隆，然后杀死。

*(不，你肯定不想被库伯统治！)*

那么，如果您不能确定应用程序在哪里，您如何向它发送请求呢？答案在于服务。

服务定义您的*部署的*Pod 的集合，因此您可以向“您的应用程序类型的任何可用 Pod”发送请求

<figure>[![](../Images/a6c095e90d0c634c2296f603ad209b86.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PQMcfofo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A7ylCISKaMMfnqK8lOJ41PA.png) 

<figcaption>当请求您的应用程序时，您不关心它的位置或哪个 pod 响应请求</figcaption>

</figure>

#### 入口

所以我们的星团里有豆荚。

我们有服务，所以我们可以从我们的集群内部与他们对话。(从技术上讲，有一些方法可以使服务从外部可用，但是我们现在有意跳过这一部分。)

现在，我们希望将我们的服务公开到互联网上，这样客户就可以访问我们的产品。

入口对象是定义应该存在的路由的规则。

<figure>[![](../Images/3df3438eefe9795701cc572b9448b4b0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---NEfMwoI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AZBjq0otb_77wzQc8Tz6BEw.png) 

<figcaption>入口对象是定义到我们服务的路由的规则</figcaption>

</figure>

#### 入口控制器

既然您已经定义了从外部访问您的服务的规则，那么您所需要的就是一个根据规则路由传入请求的组件…这些组件被称为入口控制器！

由于这是本文的主题，Traefik 是 Kubernetes 的一个很好的入口控制器。

[![](../Images/d9fdd42a269b4c3bba819110873c812b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qgpxyQ3M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AevU3Y7fXZtrIYPmCuoKpmQ.png)

### 关于 Traefik 你需要知道的事情

*   太棒了。
*   很好用。
*   它已经可以生产并被大公司使用。
*   它适用于现有的每一种主要集群技术。
*   是[开源](https://github.com/containous/traefik)。
*   超级受欢迎；在撰写本文时，下载量已超过 1.5 亿次。

…哦？我的观点是否有失偏颇？嗯，也许…但以上都是真的！

### 让我们开始把所有东西放在一起吧！

*这是我们实际开始将服务部署到 Kube 集群并将 Traefik 配置为入口控制器的部分。*

#### 先决条件

您可以访问一个 Kubernetes 集群，并且您有指向该集群的*。*

 **如你所知，这里我用的是 Kubernetes 嵌入在*[*Docker for Mac*](https://docs.docker.com/docker-for-mac/)*。*

#### 我们要做什么

*   我们将使用一个预制的容器——containous/whoami——能够告诉你它被托管在哪里，以及当你调用它时它接收到什么。(抱歉让你失望了，但我可以确定我们不会开发新的杀手级应用 here^^)
*   我们将定义两个不同的 pod，一个 *whoami* 和一个*whoami*将使用这个容器。
*   我们将创建一个部署，要求 Kubernetes 部署 1 个 *whoami* 的副本和 2 个*whoami*的副本(因为我们关心他人多过关心自己……多么体贴啊！).
*   我们将定义两个服务，每个 pod 一个。
*   我们将定义入口对象来定义服务的路由。
*   我们将设置 Traefik 作为入口控制器。

因为我们喜欢图表，这是图片:

[![](../Images/13f4a17b4853e236d842e3d77cbf8c69.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yywdXJVS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A1KSzpNWahErAqMi4p93JUw.png)

#### 使用舵设置 Traefik

Helm 是 Kube 的一个包管理器，对我来说，它是配置 Traefik 最方便的方式，而且不会把它搞乱。(如果你还没有安装 Helm，[这样做很容易](https://docs.helm.sh/using_helm/)，适合两个命令行，例如 *brew install kubernetes-helm，*和 *helm init* 。)

[![](../Images/4aa96ff90bbde332aeda69dce9441496.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_HtuSioG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/200/1%2A9LF-XeJP9m7K9VU-bYs6fQ.png)

要设置 Traefik，请复制/粘贴以下命令行:

```
helm install stable/traefik --set dashboard.enabled=true,dashboard.domain=dashboard.localhost 
```

Enter fullscreen mode Exit fullscreen mode

在上面的命令行中，我们启用了 dashboard(*dashboard . enabled = true*)并使其在[http://dashboard . localhost](http://dashboard.localhost)(*dashboard . domain = localhost . domain*)上可用。

helm install 命令行的输出如下所示:

[![](../Images/f1cb1df45a767e69342e54a23e2bd08a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--H0CdRhqS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AeoA_R3WTiHnqgK2dO5olyg.png)

在我的例子中，*外部 IP* 已经可用，并且是*本地主机*。

*EXTERNAL-IP* 是我的集群的入口地址；这是入口控制器(Traefik)的公共地址。

正如我们之前所说，我们应该能够立即访问仪表板！

<figure>[![](../Images/1f511c52646c2ba077109c97b267aea1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LdPyY1nY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ARlzppvn7upe9u4z3A3DWbQ.png) 

<figcaption>仪表板显示 Traefik 检测到了一个服务(右侧称为 dashboard.localhost)并创建了一个到它的路由(左侧为 host:dashboard . localhost)</figcaption>

</figure>

#### 让我们部署一些豆荚吧！

现在是时候部署我们的第一个 WhoAmI 了！

提醒一下，我们希望一个 Pod 有一个容器( *containous/whoami* )。

<figure>[![](../Images/ac815ace8bf9cbc31addc07a89a0e8b5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dSg3ji0r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/352/1%2ASooVurEI3Qc3gGftJDmvLg.png) 

<figcaption>相当直白的荚</figcaption>

</figure>

Kubernetes 使用 yaml 文件来描述它的对象，所以我们将使用一个文件来描述我们的部署。*