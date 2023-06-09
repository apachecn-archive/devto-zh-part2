# 使用 Helm 将可扩展的 web 应用程序部署到 Kubernetes

> 原文：<https://dev.to/vidyasagarmsc/deploy-a-scalable-web-app-to-kubernetes-using-helm-1gf6>

这篇博客文章向您展示了如何构建一个 web 应用程序，在一个容器中本地运行它，然后使用 Helm 图表将它部署到一个使用[IBM Cloud Kubernetes Service](https://console.bluemix.net/containers-kubernetes/catalog/cluster)创建的 Kubernetes 集群。

容器是打包应用程序及其所有依赖项的标准方式，因此您可以在环境之间无缝地移动应用程序。与虚拟机不同，容器不捆绑操作系统。只有应用程序代码、运行时、系统工具、库和设置被打包在容器中。容器比虚拟机更加轻量级、可移植性和高效。

对于希望快速启动项目的开发人员来说，IBM Cloud Developer Tools CLI 通过生成模板应用程序来实现快速的应用程序开发和部署，您可以立即运行这些模板应用程序，或者将其定制为您自己的解决方案的启动程序。除了生成 starter 应用程序代码、Docker 容器映像和 CloudFoundry 资产之外，dev CLI 和 web 控制台使用的代码生成器还会生成文件，以帮助部署到 [Kubernetes](https://kubernetes.io/) 环境中。这些模板生成 [Helm](https://github.com/kubernetes/helm) 图表，描述应用程序的初始 Kubernetes 部署配置，并且可以根据需要轻松扩展以创建多映像或复杂部署。

### 使用的服务

这篇博文使用了以下运行时和服务:

*   [IBM 云容器注册中心](https://console.bluemix.net/containers-kubernetes/launchRegistryView)
*   [IBM Cloud Kubernetes 服务](https://console.bluemix.net/containers-kubernetes/catalog/cluster)

### 建筑

[![](img/05d93d897ad9b54da0d6dd0f8e57234d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sU1bBdqZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ay1qJ8aRExZ2O6mGX8Ok6cQ.png)

1.  开发人员使用 IBM Cloud 开发人员工具生成一个启动应用程序。
2.  构建应用程序会产生一个 Docker 容器映像。
3.  图像被推送到 IBM Cloud Kubernetes 服务中的一个名称空间。
4.  该应用程序被部署到 Kubernetes 集群中。
5.  用户访问应用程序。

### 开始之前

*   [设置 IBM Cloud Container Registry CLI 和您的注册表名称空间](https://console.bluemix.net/docs/services/Registry/registry_setup_cli_namespace.html)
*   [安装 IBM Cloud Developer Tools](https://console.bluemix.net/docs/cli/idt/setting_up_idt.html#add-cli) —安装 Docker、kubectl、helm、ibmcloud cli 和所需插件的脚本
*   [了解 Kubernetes 的基础知识](https://kubernetes.io/docs/tutorials/kubernetes-basics/)

### 创建 Kubernetes 集群

IBM Cloud Kubernetes Service 通过结合 Docker 和 Kubernetes 技术、直观的用户体验以及内置的安全性和隔离来提供强大的工具，以在计算主机集群中自动化容器化应用的部署、操作、扩展和监控。

本教程的主要部分可以通过一个免费的集群来完成。与 Kubernetes 入口和自定义域相关的两个可选部分需要标准类型的付费集群。

*   从 [IBM 云目录](https://console.bluemix.net/containers-kubernetes/launch)创建一个 Kubernetes 集群。
*   为了便于使用，请检查配置详细信息，如使用 Lite 和 Standard 计划获得的 CPU 数量、内存和工作节点数量。

[![](img/5b72399bcec84da56affbd7d36ef539b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--c9ucjDPH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A-WQyE67lzPLmUz89pp6k8w.png)

*   选择集群类型，然后单击“创建集群”来配置 Kubernetes 集群。
*   检查集群和工作节点的状态，并等待它们准备就绪。

### 配置 kubectl

在本节中，您将配置 kubectl，使其指向您新创建的集群。kubectl 是一个命令行工具，用于与 Kubernetes 集群进行交互。

*   使用 ibmcloud login 以交互方式登录。提供创建集群的组织(org)、区域和空间。您可以通过运行 ibmcloud target 命令来重新确认详细信息。
*   当集群准备就绪时，检索集群配置:

```
ibmcloud cs cluster-config <CLUSTER NAME> 
```

*   复制并粘贴 export 命令，按照指示设置 KUBECONFIG 环境变量。要验证 KUBECONFIG 环境变量设置是否正确，请运行以下命令:echo $KUBECONFIG
*   检查 kubectl 命令是否配置正确:

```
kubectl cluster-info 
```

### 创建一个启动器应用程序

ibmcloud dev 工具通过生成包含所有必要的样板文件、构建和配置代码的应用程序启动器，极大地减少了开发时间，这样您就可以更快地开始编写业务逻辑代码。

*   启动 ibmcloud 开发向导。

```
ibmcloud dev create 
```

*   选择后端服务/Web App > Java-micro profile/JavaEE > Web App-Java micro profile/Java EE Basic 创建一个 Java starter。(要创建 Node.js starter，请使用后端服务/ Web App >基本 Web >节点> Web App - Express.js 基本)
*   输入项目的名称。
*   输入项目的唯一主机名。如果您将应用程序部署为 Cloud Foundry app .mybluemix.net，则会使用主机名。
*   不要添加 DevOps 工具链，选择手动部署。
*   不要添加额外的服务。

这将生成一个 starter 应用程序，其中包含本地开发和部署到 Cloud Foundry 或 Kubernetes 上的代码和所有必要的配置文件。

### 构建应用程序

您可以像通常使用 mvn 进行 java 本地开发或 npm 进行节点开发一样构建和运行应用程序。您还可以构建 docker 映像，并在容器中运行应用程序，以确保本地和云上的一致执行。使用以下步骤来建立您的 docker 映像。

*   确保您的本地 Docker 引擎已启动。

```
docker ps 
```

*   切换到生成的项目目录。

```
cd <projectname> 
```

*   构建应用程序。

```
ibmcloud dev build 
```

*   这可能需要几分钟的时间来运行，因为所有的应用程序依赖项都已下载，包含您的应用程序和所有所需环境的 Docker 映像也已构建。

### 本地运行应用程序

*   运行容器。

```
ibmcloud dev run 
```

*   这将使用您的本地 docker 引擎来运行您在上一步中构建的 Docker 映像。
*   在你的容器启动后，进入 [http://localhost:9080/](http://localhost:9080/) 。如果您创建了 Node.js 应用程序，请使用端口 3000。

[![](img/b6eb785efc5f62b379e704940f8de47c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--E6pHxnat--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/912/1%2AnrFEawAYuLcO8mK2dnFi_g.png)

### 使用 helm 将应用程序部署到集群

在本节中，首先将 Docker 映像推送到 IBM Cloud private container registry，然后创建一个指向该映像的 Kubernetes 部署。

*   通过列出注册表中的所有名称空间来找到您的名称空间。

```
ibmcloud cr namespaces 
```

如果您有一个名称空间，请记下该名称以备后用。如果你没有，那就创建一个。

*   将 MYNAMESPACE 和 MYPROJECT 环境变量分别设置为您的命名空间和项目名称

```
export MYNAMESPACE=<NAMESPACE>

export MYPROJECT=<PROJECTNAME> 
```

*   通过运行以下命令确定您的集装箱注册地(例如 registry.ng.bluemix.net)

```
ibmcloud cr info 
```

*   将 MYREGISTRY env 变量设置为您的注册表。

```
export MYREGISTRY=<REGISTRY> 
```

*   标记 docker 图像，该图像用于创建在本地运行应用程序的容器

```
docker images

docker tag <DOCKER IMAGE NAME> ${MYREGISTRY}/${MYNAMESPACE}/${MYPROJECT}:v1.0.0 
```

*   对于 Java app，替换为您的项目名称，对于 node app，替换为以-run 结尾的图像名称。
*   将 docker 映像推送到 IBM Cloud 上的容器注册中心

```
docker push ${MYREGISTRY}/${MYNAMESPACE}/${MYPROJECT}:v1.0.0 
```

*   在 IDE 上，导航到 chartYOUR PROJECT NAME 下的 values.yaml，并更新指向 IBM Cloud container registry 上的图像的图像存储库 value。保存文件。
*   有关图像存储库的详细信息，请运行 echo $ { my registry }/$ { my namespace }/$ { my project }
*   [Helm](https://helm.sh/) 通过 Helm Charts 帮助您管理 Kubernetes 应用程序，帮助您定义、安装和升级最复杂的 Kubernetes 应用程序。通过导航到您的项目名称图表并在您的集群中运行以下命令来初始化 Helm

```
helm init 
```

要升级 helm，请运行以下命令 helm init - upgrade

*   要安装舵图，请运行以下命令

```
helm install . --name ${MYPROJECT} 
```

*   你应该看到==> v1/Service。记住节点端口，它是端口下的一个 5 位数(例如，31569)。这是你的端口号。
*   对于 worker 节点的公共 IP，运行以下命令

```
ibmcloud cs workers <CLUSTER NAME> 
```

*   访问应用程序[http://worker-IP-address:port number/name of project](http://worker-ip-address:portnumber/nameofproject)

不要停在这里。通过以下部分继续您的 Kubernetes 之旅

*   [使用 IBM 为您的集群提供的域](https://console.bluemix.net/docs/tutorials/scalable-webapp-kubernetes.html#ibm_domain)或[使用您自己的定制域](https://console.bluemix.net/docs/tutorials/scalable-webapp-kubernetes.html#custom_domain)
*   [监控应用健康状况](https://console.bluemix.net/docs/tutorials/scalable-webapp-kubernetes.html#monitor_application)
*   [持续部署到 Kubernetes](https://console.bluemix.net/docs/tutorials/continuous-deployment-to-kubernetes.html#continuous-deployment-to-kubernetes)

* * *