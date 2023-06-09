# Kubernetes 用图片解释:有状态的集合、持久的卷和无头服务

> 原文：<https://dev.to/danlebrero/kubernetes-explained-in-pictures-statefulsets-persistent-volumes-and-headless-services-5do9>

这是系列文章的第三部分，我们用主题公园的例子来解释 Kubernetes 的概念。

在第一篇帖子中，我们介绍了 Kubernetes 集群的基础知识。

在第二篇帖子中，我们讨论了缩放、亲和力和污点。

在这篇文章中，我们将讨论有状态集合、持久卷和无头服务。

## 衣帽间和衣帽间服务

为了改善 KubePark 的体验，您决定提供免费的衣帽间服务。你使用相同的趣味骑行模板通知你的船员在公园里建造一个衣帽间，你为它添加了新的路标颜色，游客们高兴地开始使用它。

抱怨丢了衣服不会持续太久。调查正在发生的事情，当你看到你的维修人员更换有故障的衣帽间时，你很快就会意识到正在发生什么:

[![kubernetes stateful set maintenance](img/87ffab94b5112660330369428fc874ee.png "Kubernetes stateful set maintenance")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uOZ6Dhhz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://danlebrero.cimg/blog/kubernetes-explained/kubernetes-stateful-set-maintenance.jpg)

也许将一切付之一炬的想法并非没有缺陷。

类似地，当带衣帽间的包裹变得不可用时，无论是由于一些洪水还是因为你的控制人员正在减少租赁包裹的数量，衣帽间中的所有衣服都被留下。更多丢失的衣服！

这对 KubePark 来说显然不是一个好的宣传，你的房东不会浪费时间提供他的自我存储服务 **(k8s [持久卷](https://cloud.google.com/kubernetes-engine/docs/concepts/persistent-volumes) )** ，作为一个解决方案，建在一个核避难所里。

[![kubernetes stateful set maintenance](img/6d9b7f7d73a77b33d13c7c116fe63efa.png "Kubernetes persistent volume")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qAmbNjpR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://danlebrero.cimg/blog/kubernetes-explained/kubernetes-persistent-volume.jpg)

你需要做的唯一一件事就是在衣帽间的计划中指定必须租赁多少个存储单元，你的控制团队将负责所有的文书工作 **(k8s [动态持续量声明](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) )** 。

由于自助寄存在公园外，衣帽间的工作人员会跑到存储单元离开并取回游客的物品，使整个服务变慢，但至少物品不会丢失。

问题解决了！至少在短时间内…

## 扩展您的衣帽间服务

随着 KubePark 越来越受欢迎，离开衣服的队列开始变得难以忍受。您会感到恐慌:每个存储单元只有一个密钥，并且该密钥不可复制。增加衣帽间数量不会减少排队！

每个衣帽间都必须有自己的储物单元，但这带来了一系列全新的问题。

[![kubernetes stateful set headless service missing](img/ebea6885b0b0500c0394c5b3dc405469.png "Kubernetes stateful set wrong instance")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1z3wez0k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://danlebrero.cimg/blog/kubernetes-explained/kubernetes-stateful-set-wrong-instance.jpg)

首先，游客需要在他们收到收据的同一个衣帽间里赎回衣帽间的收据。

第二，如果一个衣帽间必须被移动或替换以进行维护，替换的衣帽间必须保持与它所替换的衣帽间相同的名称和存储单元钥匙:

*   相同的名字，以便从游客的角度来看是相同的衣帽间。
*   相同的存储单元，因为如果不是这样，该存储单元将是空的，并且存储在先前存储单元中的项目是不可访问的。

这是如此的复杂，以至于它应该有自己吸引人的模板 **(k8s [statefulsets](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/) ，注意，statefulsets 还带有一些额外的排序保证)**。

第三，记住彩色寻路器带游客去任何符合标签的景点，但游客必须去收据的衣帽间，所以彩色寻路器没有用。即使对游客来说更麻烦，你最终还是安装了一个路标 **(k8s [无头服务](https://kubernetes.io/docs/concepts/services-networking/service/#headless-services) )** ，上面有衣帽间的名称和方向，这样游客可以自己找到合适的衣帽间。

现在 KubePark 可以有上百个衣帽间。梦想成真。

[![kubernetes stateful set headless service](img/a54452e9b311adcd8b5667eaed23c381.png "Kubernetes stateful set headless service")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--99iHpEqB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://danlebrero.cimg/blog/kubernetes-explained/kubernetes-stateful-set-final.jpg)

## 就这些？

差不多！仍然有一些额外的概念需要注意，但我不认为它们需要 KubePark 类比:

1.  名称空间，就像通常的文件系统文件夹。
2.  DaemonSets，在每个地块运行一个 pod，无论是现在还是将来。
3.  机密和配置映射，相当于配置文件。
4.  工作，一次性任务。
5.  CronJobs，一份有 cron 的工作。
6.  优先级，决定在安排或耗尽资源时哪些景点更重要。
7.  初始化容器，为分离舱准备场地。
8.  复制控制器，过时，由副本集取代。
9.  副本集，您可以将其视为部署的实现细节。
10.  [中断和滚动更新](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/)，可以同时停机多少个吊舱？
11.  kube let/调度器/主控制器/API 服务器: [k8s 内部](https://jvns.ca/blog/2017/06/04/learning-about-kubernetes/)。

当然，在每个概念上还有无数的细节需要你去了解，但是我希望你在 Kubernetes 的世界里少迷失一点。

享受你的 Kubernetes 之旅！