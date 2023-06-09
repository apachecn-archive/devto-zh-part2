# Kubernetes 用图片解释:主题公园类比

> 原文：<https://dev.to/danlebrero/kubernetes-explained-in-pictures-the-theme-park-analogy-3d99>

Kubernetes (k8s 从现在开始)有自己的一套抽象，自己的行话，但是只要有一点想象力，我们就可以将这些抽象与更熟悉的概念联系起来。

我希望香肠工厂的类比能够奏效，但不幸的是 k8s 不仅仅是香肠，所以我们将不得不满足于主题公园的类比。

以下基于 [Google Kubernetes 引擎](https://cloud.google.com/kubernetes-engine/)；一些概念在其他提供商或者您自己运行 k8s 的情况下可能会略有不同。

## 你是谁

你是 Kubenelius Fizzbuzz，一名刚毕业的工商管理硕士，专门研究各种尺寸的盒子，你即将开始你最伟大的想法:KubePark 主题世界。

## 趣味骑行计划

要开始 KubePark，你需要计划它有趣的游乐设施。你决定，对于每个景点，你只需要填写这样的模板 **(k8s [部署](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) )** :

[![kubernetes deployment](img/899d66fce56f331fb6e865c7212a74b4.png "Kubernetes deployment")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---2fY0CDB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://danlebrero.cimg/blog/kubernetes-explained/kubernetes-deployment.jpg)

这里你说库贝帕克将有三个 1909 年的旋转木马。

1909 年的旋转木马由两部分组成:一个经典的旋转木马加上一个棉花糖摊位。每个零件 **(k8s [集装箱](https://www.docker.com/what-container) )** 将由无人机装在一个巨大的箱子里运送，完全组装好，一旦拆箱就可以滚动。

每个景点都将被围栏包围，将它们与其他景点隔离开来，这样一个景点的问题就不会蔓延到其他景点。但是景点的各个部分形成了一个不可分割的整体 **(k8s [pod](https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/) )** ，因此没有任何障碍或防火墙会阻碍各个部分之间的通信或移动，破坏游客的享受 **(k8s 除非你想[共享文件](https://www.mirantis.com/blog/multi-container-pods-and-container-communication-in-kubernetes/)这需要一点配置)**。

有一个栅栏意味着你需要计划访客的门 **(k8s [集装箱港口](https://kubernetes.io/docs/concepts/services-networking/connect-applications-service/) )** 应该在哪里。以 1909 年的旋转木马为例，游客将穿过棉花糖摊位:坐旋转木马在享受蓬松的糖云时总是更有趣。

该模板还指定了大小 **(k8s 内存)**和功率 **(k8s CPU)** 要求 **(k8s [资源](http://www.noqcks.io/notes/2018/02/03/understanding-kubernetes-resources/) )** ，以及标签 **(k8s [标签](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/))**1909 旋转木马作为一个旋转木马，对孩子友好且经典的景点。我们稍后会看到您将如何使用所有这些信息。

在模板的背面，上面没有显示，你写下每个零件的维护说明 **(k8s [探头](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) )** 。您的团队将定期遵循这些指示，以了解是否有任何零件需要更换。如果出现这种情况，维修人员会将故障部件烧成灰烬，无人机会带来另一个装有替代品的大箱子。因此，没有摆弄周围试图修复任何东西，而只是一个简单的替换整个摊位或转盘。在更换期间，景点将对游客关闭，即使一些部分仍处于工作状态:1909 年旋转木马体验必须有棉花糖，必须包括旋转木马乘坐，这两件事是不可分割的。

## 土地

随着游乐设施的规划，是时候建造公园了。

你和房东签订租房合同 **(k8s [节点池](https://cloud.google.com/kubernetes-engine/docs/concepts/node-pools) )** :

[![kubernetes node pool](img/6f9f31071a2f19795a723a82d20df043.png "Kubernetes node pool")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HCkDlGpC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://danlebrero.cimg/blog/kubernetes-explained/kubernetes-node-pool.jpg)

合同是两块地 **(k8s [节点](https://kubernetes.io/docs/concepts/architecture/nodes/) )** ，每块都有规定大小的 **(k8s 内存)**和发电机 **(k8s CPU)** 。请注意，合同不是针对两个特定的地块，而是针对任何两个具有这些特征的地块，找到它们将是房东的工作。有了现代客运手段，他们甚至不需要相邻。

房东还负责通过在每个地块周围安装带刺铁丝网来确保进入地块的安全。

你还勾选了免费灾害保险 **(k8s [汽修](https://cloud.google.com/kubernetes-engine/docs/concepts/node-auto-repair) )** 框:如果一个包裹变得不能用了，房东会换一个完全相同特性的。房东将全权负责查明并决定包裹是否无法使用，并进行更换。

最后，合同提供的任何地块都将被标记为 **(k8s [再次标记为](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) )** 地中海气候的平原。我们将在后面看到如何使用这些信息。

## 建筑公园

你已经足够努力了，所以你把公园建设的细节委托给你的管理团队。工作人员将确保您在计划中指定的景点的确切数量和类型始终有效。

作为一种意想不到的慷慨姿态，你的房东提供了一座空中交通控制塔 **(k8s [集群主](https://cloud.google.com/kubernetes-engine/docs/concepts/cluster-architecture#master) )** 来容纳控制人员，并负责塔台可能需要的任何维护和改进工作。少了一件需要关心的事！

从塔的特权位置和你手中的游乐乘坐计划，你的工作人员将决定在哪些地块上建造每个景点，确保地块的可用空间和备用电力能够满足景点所需的 **(k8s [资源服务质量](http://callmeradical.com/post/k8s-resource-limits-requests-qos/) )** 。

一个景点将始终获得所需的最小功率 **(k8s CPU)** ，绝不会获得超过最大功率的功率(否则会短路和烧毁)，如果包裹中有备用功率，将获得额外的功率(超过最小值)。如果没有多余的，吸引力只会移动得更慢。

康加舞派对线有最小和最大尺寸 **(k8s 内存)**。它将总是获得指定的最小大小，但是如果超过最大大小，它将被关闭。每个人都知道太长的康茄舞总是以疯狂的骚乱告终！如果包裹中有剩余空间，它将获得额外的空间(超过最小空间)，但如果没有剩余空间，超过最小空间的景点将被关闭。这是由交通控制塔来决定哪一个，这可能是康加舞派对线本身或另一个。

由于有足够的空间和电力，团队可以毫无问题地创建您计划的所有景点。公园差不多准备好了！

## KubePark 开业

为了帮助游客到达他们的景点，你决定对他们来说最简单的方法是使用彩色的路径搜索器 **(k8s [节点服务](https://kubernetes.io/docs/concepts/services-networking/service/#type-nodeport) )** 比如:

[![kubernetes nodeport service](img/ea775e3deb7e18cf3055d20469f84fa6.png "Kubernetes node port service")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--G0xS3kyM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://danlebrero.cimg/blog/kubernetes-explained/kubernetes-nodeport-service.jpg)

<small>图片归属:[伦敦维多利亚站楼层线路](https://commons.wikimedia.org/wiki/File:Cmglee_London_Victoria_station_floor_lines.jpg)由 [Cmglee](https://commons.wikimedia.org/wiki/User:Cmglee) 负责。</small>

在这里，您决定创建一条蓝色通道，将游客带到您标记为“经典”的景点，以及一条绿色通道，将游客带到您标记为“过山车”的景点。您可以根据需要指定特定的或通用的标签，并且可以在必要时组合多个标签。

这个寻路器不仅仅是给游客用的，你的内部船员也可以使用它，你甚至可以创建只有你的船员**(k8s[cluster IP service](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types))**可见的路径，例如找到船员的食堂。

由于包裹完全被铁丝网包围，你需要设法让游客进入公园。一种选择是从一条彩色的路径一直到铁丝网，并在上面打一个洞 **(k8s [负载平衡器服务](https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer) )** ，但这意味着你需要设法保护每一条彩色的路径。

因此，你可以选择设置一些准入门槛 **(k8s [入口](https://kubernetes.io/docs/concepts/services-networking/ingress/) )** 。你的房东提供了一些标准的门，所以你的船员不需要维护他们，他们也不需要空间或电力从你的包裹。房东还将负责建造一座从大门到地块的立交桥。也许将来你会选择更专业的准入门槛 **(k8s 比如 [Traefik](https://docs.traefik.io/user-guide/kubernetes/) )** 。

你给入口门的工作人员一些简单的指示:

1.  带着六岁以下孩子的游客应该使用红色通道。
2.  只有青少年的游客应该使用绿色通道。
3.  ...

## 启动运行！！！

有了这个，你的公园就可以开始了:

[![kubernetes as a picture](img/ece124c9424f8dba2bb61c963490f83a.png "Kubernetes explained in pictures")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iKDjTg0Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://danlebrero.cimg/blog/kubernetes-explained/kubernetes-explained-in-pictures.jpg)

## 就这些？

你想得美！

你可以阅读更多关于 Kubernetes [伸缩、污点和亲缘关系](https://dev.to/danlebrero/kubernetes-explained-the-theme-park-analogy-auto-scaling-taints-and-affinities-2ced)和[有状态集合、持久卷和无头服务](https://dev.to/danlebrero/kubernetes-explained-in-pictures-statefulsets-persistent-volumes-and-headless-services-5do9)！