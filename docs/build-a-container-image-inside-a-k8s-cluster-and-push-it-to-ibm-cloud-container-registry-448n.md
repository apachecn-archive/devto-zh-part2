# 在 K8s 集群中构建一个容器映像，并将其推送到 IBM Cloud Container Registry

> 原文：<https://dev.to/vidyasagarmsc/build-a-container-image-inside-a-k8s-cluster-and-push-it-to-ibm-cloud-container-registry-448n>

### 在 Kubernetes 集群中构建一个容器映像，并将其推送到 IBM Cloud Container Registry

了解如何从 Kubernetes 集群中的 Dockerfile 文件构建一个容器映像，并将该映像推送到 IBM Cloud Container Registry 所有这些都使用谷歌的 [Kaniko](https://github.com/GoogleContainerTools/kaniko) 工具。

### 那么，Kaniko 是什么？

> Kaniko 是一个在容器或 Kubernetes 集群中从 docker 文件构建容器映像的工具。

<figure>[![](../Images/7973915b68f3414e8a5b93c21786cad4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--j2RLrC_M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AVKL-Byx-GSAOl82ytNe8RQ.png) 

<figcaption>Kaniko 标志</figcaption>

</figure>

> 如果您没有安装了 Knative 和 Istio 的 Kubernetes 集群，建议遵循我在上一篇文章中提到的说明，这篇文章还向您介绍了 Knative 的组件— [安装 Knative 和 Istio，并在 IBM Cloud 上部署一个应用程序](https://medium.com/@VidyasagarMSC/install-knative-with-istio-on-iks-cluster-and-deploy-an-app-on-ibm-cloud-7b7d368b9833)

本教程使用 Knative 的[构建](https://github.com/knative/docs/blob/master/build/README.md)和[服务](https://github.com/knative/docs/blob/master/serving/README.md)组件来编排端到端部署。

Knative [build](https://github.com/knative/docs/tree/master/build) 扩展了 [Kubernetes](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/) 并利用现有的 Kubernetes 原语为您提供从源代码运行集群上容器构建的能力。例如，您可以编写一个构建，使用 Kubernetes-native 资源从存储库中获取源代码，将其构建到容器 a 映像中，然后运行该映像。

Knative Serving 构建在 Kubernetes 和 Istio 之上，支持部署和服务无服务器应用程序和功能。服务易于开始，并可扩展以支持高级场景。

### 什么是构建模板？

BuildTemplateis 是 Knative build 的关键特性之一，用于定义可重用的模板，并封装了具有一些有限参数化功能的可共享构建过程。在 [build-templates](https://github.com/knative/build-templates) repo 中提供了一组管理和支持的构建模板。我们将在教程中使用 **Kaniko 构建模板**

> Kaniko 不依赖 Docker 守护进程，完全在用户空间中执行 Docker 文件中的每个命令。这使得在不能轻松或安全地运行 Docker 守护进程的环境中构建容器映像成为可能，比如标准的 Kubernetes 集群。

让我们首先创建一个 Kaniko 构建模板，并将其保存为 kaniko.yaml