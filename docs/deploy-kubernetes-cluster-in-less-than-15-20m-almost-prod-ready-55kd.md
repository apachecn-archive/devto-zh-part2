# 在不到 15-20 分钟的时间内部署 Kubernetes 集群，几乎可用于生产

> 原文：<https://dev.to/joehobot/deploy-kubernetes-cluster-in-less-than-15-20m-almost-prod-ready-55kd>

如今，有许多方法可以将 kubernetes 部署到大多数云提供商，如 AWS、Google Cloud、Azure 和/或 Baremetal。

为了 POC(概念验证),我最喜欢做的一件事是利用 Waveworks 的一个叫做 [EKSCLI](https://github.com/weaveworks/eksctl) 的工具。

这是非常容易设置的，只要你有他们描述的需求，你几乎不需要做任何事情。

这是我制作的关于 EKS 集群完全部署的 YT 视频+一些随机提示。

[https://www.youtube.com/embed/zKCudYazOwM](https://www.youtube.com/embed/zKCudYazOwM)

继续使用其他工具...

Kubespray ，配置有点简单，但是需要时间来设置，因为它在后台使用 Ansible。你必须设置服务器/ssh 密钥等，我花了大约 1 个小时设置了 3 个主节点和 6 个工作节点，在 terraform 的帮助下建立了 EC2 实例等..

KOPS 是另一个类似于 EKSCLI 的工具，但是它在 AWS vs EKS 中使用 ec2 实例。超级容易配置，可以在 20-30 米内建立一个集群，他们还说其生产准备就绪..(这完全取决于人们如何看待 K8s 的“生产就绪”)

[JX](https://github.com/jenkins-x/jx) -这是我已经玩了一段时间的东西，因为它支持我“玩”的大多数云提供商，包括我的 minikube 本地副本，但不仅仅是这样！
JX 为你提供了 jenkins X CI/CD pipeline 的一些例子，包括挂钩到 Github 和部署一个应用程序。因此，如果你是开发人员，这可能是你最喜欢的东西。

如果您知道任何其他用于部署 Kubernetes 集群的自动化工具，请告诉我。