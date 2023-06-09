# 将 Node.js API 容器化&将 Docker 与 Kubernetes 和 Minikube 一起使用

> 原文：<https://dev.to/nickparsons/containerizing-a-nodejs-api--using-docker-with-kubernetes-and-minikube-16o9>

[![](img/4ce65fcc668b1c441894f4e87e362693.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Gnz8E4de--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3qlr9tepvzqg3550p6w6.jpeg)

作为 [Stream](https://getstream.io) 的开发者传道者，我有机会了解我们的工程团队和客户每天都在使用的大量令人敬畏的新技术。可以吸收的知识量绝对惊人，我喜欢抓住每一个机会学习所有的新兴工具和玩具。

最近谈论最多的组合之一是 Docker 和 Kubernetes。Docker 和 Kubernetes 是一个发电站，它使得开发能够在多种操作系统上运行的快速、不可变的应用程序变得非常容易，而没有处理包管理的所有需求的所有麻烦。Docker 将任何给定操作系统的所有需求打包到您的 Docker 容器中，通过几个 Kubernetes 命令，您的应用程序就可以提供给用户，并且具有不可变的 pods，可以通过一个 Kubernetes 命令随时终止和启动。

在这篇文章中，我将向您介绍我如何用 Docker 封装一个应用程序，并使用 Kubernetes 和 Minikube 在本地提供它。最终，你将获得足够的知识去做同样的事情，并有希望通过在 Kubernetes 的云中启动你自己的应用程序，将它带到一个新的高度。

# 要求

和任何教程一样，我们提出了一些要求来帮助你走向成功；我们想确保你，读者，能够跟上而不会迷路😉。

这篇文章最重要的一点是，它是面向 macOS 用户的。如果你使用的是 Windows 或 Linux，你仍然可以跟着做；但是，我的命令将与您想要使用的命令略有不同。

## 第一步:自制

如果你的电脑上没有安装家酿软件，你可以使用 https://brew.sh/网站上的安装命令来安装。

[![](img/c6c74f275071bd01a3397d853782eeb8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uxSbJWR3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1l2xwgk9tqfpwdlthvug.png)

## 第二步:Docker

要安装 Docker，请进入入门页面，点击“Mac 版下载”。您将被重定向到另一个页面，您可能需要先创建一个帐户。

[![](img/00c227669f10fcf7f2370a8059774949.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wYHzoJFn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2l3bife2spvgprdxxngn.png)

## 第三步:Minikube

本教程使用 Minikube 创建一个本地集群。说了这么多，让我们安装 Minikube:

```
brew cask install minikube 
```

## [步骤 4:超链接](#step-4-hyperkit)

按照 [Minikube 驱动程序安装指南](https://github.com/kubernetes/minikube/blob/master/docs/drivers.md#hyperkit-driver)所述安装 HyperKit 驱动程序。或者，运行这个命令(如果你在 macOS 上)来安装 Hyperkit 驱动程序:

```
curl -Lo docker-machine-driver-hyperkit https://storage.googleapis.com/minikube/releases/latest/docker-machine-driver-hyperkit \
&& chmod +x docker-machine-driver-hyperkit \
&& sudo cp docker-machine-driver-hyperkit /usr/local/bin/ \
&& rm docker-machine-driver-hyperkit \
&& sudo chown root:wheel /usr/local/bin/docker-machine-driver-hyperkit \
&& sudo chmod u+s /usr/local/bin/docker-machine-driver-hyperkit 
```

## 步骤 5:不间断 CLI

使用 Homebrew 下载 kubectl 命令行工具，您可以使用它与 Kubernetes 集群进行交互:

```
brew install kubernetes-cli 
```

# 克隆样板 Express API💾

现在您已经完成了启动和运行 Docker 和 Kubernetes 的步骤，让我们继续下载我整理的样板 Express API。您可以使用自己的命令，但是，我建议您首先尝试一下，直到您掌握了我们将要使用的命令。

## 前往你想要存储代码的目录。然后继续运行以下命令，将存储库克隆到您的目录中:

```
git clone git@github.com:nparsons08/boilerplate-express-api.git api 
```

> 注:如果你想先看看回购，[点击这里](https://github.com/nparsons08/boilerplate-express-api)。

## 接下来，进入目录，构建 API 并启动它进行测试:

```
cd api && yarn build && yarn start 
```

## API 会自动打开一个窗口，您会看到与此类似的内容:

[![](img/50f31d9c8364cef3112afa8dadba5e36.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LZRMTjYd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vm51r7a7dg0duqmi0zyv.png)

# 用 Docker 容器化 API📦

我承认，当我第一次看到 Docker 的时候，我有点害怕。这个概念对我来说没什么意义，命令看起来完全陌生。然而，在阅读和研究了一些文档之后，我开始理解使用 Docker 的基本原理——你也会理解的。现在，我们要保持事情简单；在以后的文章中，我们将继续前进，让事情更上一层楼😀。

> 注意:所有命令都应该在终端中运行。

## 第一步:了解 Dockerfile(目录中已经存在一个模板):

```
# use latest version of node
FROM mhart/alpine-node:latest

# set working directory
WORKDIR /dist

# bundle source code
COPY . .

# expose port 3000
EXPOSE 3000

# start app with yarn
CMD ["yarn", "start"] 
```

## 第二步:构建 Docker 镜像:

```
docker build -t boilerplate-api/api . 
```

## 第三步:列出所有 Docker 图片(验证您的图片是否存在):

```
# list all docker images
docker images 
```

## 第四步:运行 Docker 镜像:

```
# run the docker image
docker run -p 8080:3000 -d boilerplate-api/api 
```

## 第五步:查看正在运行的 API:

```
# open in browser
open http://localhost:8080 
```

繁荣💥！您刚刚使用 Docker 启动了 API！现在让我们把它拆了，因为几分钟后我们将使用 Kubernetes 和 Minikube 来启动 Docker 容器。

## 第六步:拆掉:

```
# stop all containers
docker stop $(docker ps -a -q)

# destroy all containers
docker rm $(docker ps -a -q)

# destroy all images
docker rmi $(docker images -q) 
```

# 踏入迷你库贝&库贝内斯的土地👣

Minikube 是一个让本地运行 Kubernetes 变得容易的工具。Minikube 在笔记本电脑上的虚拟机中运行单节点 Kubernetes 集群。

## 设置 Minikube

通过打开一个新的终端并使用以下命令，确定你是否可以直接访问像 https://cloud.google.com/container-registry/这样的网站，而不需要代理服务器:

```
# check that you have access to google's container registry
curl --proxy "" https://cloud.google.com/container-registry/ 
```

> 注意:如果这个 cURL 命令不起作用，请停止并尝试解决问题。如果不能访问外部互联网，Minikube 将无法正常启动(对于本教程)。

cURL 命令应该会退回一堆类似如下的 HTML:

[![](img/552911b9716279348603820cf55b86c7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tfhpZ7bo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6e3vzebpehnilo2sccm0.png)

> 注意:如果您无法访问 Google 容器注册表，则您的连接有问题。在继续下一步之前，您需要对此进行调试。

接下来，确保 Docker 守护进程已经启动。您可以使用命令来确定 docker 是否正在运行，例如:

```
# quick check if docker is running
docker images 
```

> 注意:如果命令成功，您应该在终端中看到 Docker 图像列表。如果你没有，你需要调试为什么 Docker 没有在你的机器上运行。

现在您已经验证了 Docker 正在运行，我们可以使用下面的命令启动一个 Minikube 进程:

```
# start minikube with kyperkit specified
minikube start --vm-driver=hyperkit 
```

> 注意:`--vm-driver=hyperkit`标志指定您正在使用 macOS 的 Docker。默认的 VM 驱动实际上是 VirtualBox，但在 macOS 上首选 HyperKit。

如果成功，您的终端看起来将完全像这样:

[![](img/7faa59fed2e42df2ff28fd7a8a28f831.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--us7KUVCV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ywmjkt274rh37dczdiim.png)

现在设置 Minikube 上下文。上下文决定了与哪个集群`kubectl`进行交互。我们将使用下面的命令来做到这一点:

```
# specify context
kubectl config use-context minikube 
```

验证`kubectl`是否配置为与您的集群:
通信

```
# get cluster info
kubectl cluster-info 
```

# 启动 Minikube 仪表盘

> 注意:Minikube 附带了一个仪表盘，这样你就可以直观地看到正在发生的一切！

现在，让我们开始启动仪表板吧！

```
# start minikube dashboard
minikube dashboard 
```

[![](img/7fa7b3aa405332e42c80782ff57e0434.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7w10P-KY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vpjvsuxplhwyhkso7dr7.png)

好吧，你已经走到这一步了。我们继续吧！

## 创建一个 Docker 图像

为了简单起见，让我们继续使用我们在本文前面使用的样板 API。

因为本教程使用 Minikube，而不是将 Docker 映像推送到注册表，所以您可以使用与 Minikube VM 相同的 Docker 主机来构建映像，这样映像就会自动出现。为此，请确保您使用的是 Minikube Docker 守护进程:

```
# set the docker daemon to minikube
eval $(minikube docker-env) 
```

现在我们的守护进程已经为 Docker 设置好了，我们可以继续创建 Docker 映像。转到我们之前创建的`/api`目录，运行下面的命令:

```
# build docker image
docker build -t api:v1 . 
```

> 注意:现在 Minikube VM 可以运行您构建的映像了。

该命令的输出应该如下所示:

[![](img/9a22b10fc6b1655161199efff23c757a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fGjhLLMn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fds37ewg68j66ijzky8q.png)

# 创建一个 Kubernetes 部署💻

Minikube 正在运行，我们的 Docker 映像已经创建，一切进展顺利。让我们快速讨论一下 Kubernetes 部署的结构。

Kubernetes [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/) 是一组一个或多个容器，为了管理和联网而连接在一起。本教程中的 Pod 只有一个容器。Kubernetes [部署](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)检查你的 Pod 的健康状况，如果它终止，重启 Pod 的容器。部署是管理 pod 的创建和扩展的推荐方式。

使用 kubectl run 命令创建一个管理 Pod 的部署。Pod 运行一个基于您的`api:v1` Docker 图像的容器。将`--image-pull-policy`标志设置为`Never`，以便总是使用本地图像，而不是从 Docker 注册表中提取图像(因为您还没有将它推到那里):

```
# create a kubernetes deployment
kubectl run api --image=api:v1 --port=8080 --image-pull-policy=Never 
```

现在我们可以使用以下命令查看部署:

```
# get kubernetes deployments
kubectl get deployments 
```

而且，如果您访问您的仪表板(在您的终端中运行 minikube dashboard 命令)，您会看到绿色！

[![](img/49980a032ff2137ccd34f8614ac2ee63.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DeC2LaUZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tr4vdurfsbeccqcdb9sy.png)

你甚至可以在 [http://localhost:8080](http://localhost:8080) 访问 API！

> 注意:当您不想再使用 Minikube 主机时，您可以通过运行`eval $(minikube docker-env -u)`来撤销此更改。

# 恭喜恭喜！🎉

恭喜你！您刚刚用 Docker 封装了一个 API，启动了 Minikube，并在本地机器上将 Docker 映像部署到 Kubernetes。

干得好！在以后的文章中，我们将详细介绍如何封装和运行一个无状态但需要访问外部服务(如 Stream、MongoDB、Redis 等)的应用程序。

在此之前，我推荐以下文章来提高你的技能:

*   [Docker 入门](https://docs.docker.com/get-started/)
*   [Kubernetes 基础知识](https://kubernetes.io/docs/tutorials/kubernetes-basics/)
*   [Docker 最佳实践](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)
*   [Kubernetes 最佳实践](https://kubernetes.io/docs/concepts/configuration/overview/)

编码快乐！👏