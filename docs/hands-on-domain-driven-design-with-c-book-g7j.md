# 在新的 Azure Kubernetes 集群上部署 GitLab CE

> 原文：<https://dev.to/alexeyzimarev/hands-on-domain-driven-design-with-c-book-g7j>

我想分享我在 Azure Container Service(AKS Preview)上创建一个小型 Kubernetes 集群并使用 Helm chart 在其上部署 GitLab CE 的经验。

最初发表于[媒体](https://medium.com/@zimareff/deploy-gitlab-ce-on-a-new-azure-kubernetes-cluster-9251100df5d7)。

[![](img/3b4a9889847f0a79785985718ee08a8f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TGFoP1JC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AughT-zUXTkdL0xZJA9Nn5w.png)

* * *

在 AKS 中创建一个集群应该是一件容易的事情，但是有时候事情并没有按照预期进行。我发现至少有两个主要问题:

*   当使用 Azure 门户时，很有可能会出现问题，并且群集将始终处于“正在创建”状态，您只能通过使用 Azure CLI 来删除它。
*   Azure CLI 无法了解哪个区域有哪些虚拟机大小。当然，并不是所有的虚拟机大小都可以在 AKS 中使用，在不同的地区，可用的虚拟机大小是不同的。当别人为你的 Azure 订阅付费时，这是可以的，但使用你自己的免费订阅或自掏腰包可能最终不会那么好。
*   您需要一个至少有三个节点的集群，因为较小的虚拟机对可以连接多少数据卷有限制，而 GitLab 使用相当多的持久卷。

因此，我设法安装了带有三个最新虚拟机节点的集群，这些虚拟机仍然非常便宜，是相对较新的 B 系列机器大小。据微软称，这些机器最适合不规则的 CPU 负载，如网站、开发或测试环境。这非常符合我的目的。

我用的是 B2s 机器大小，带 2 个 vCPUs，8 Gb RAM，8 Gb 本地 SSD。

如果你想重复我所做的，你需要 Azure CLI。如果您没有安装，请参见本页的[中的安装说明。你还需要](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) ，Kubernetes 的管理命令类工具。

登录 Azure 账户后，我必须注册一个新的资源组，
，因为 AKS 需要一个单独的资源组。所以这样做，我执行了这个命令:

```
az group create --name ubiquitous --location eastus 
```

Enter fullscreen mode Exit fullscreen mode

虽然我不是来自美国，但我选择了这个地区，因为它拥有大多数可用的虚拟机大小。可以想象，`ubiquitous`是我自己的资源组名称，您应该使用其他名称。

接下来，创建集群。使用 Azure CLI 做这件事相当简单。

```
az aks create --resource-group ubiquitous --name ubiquitous --node-count 3 --generate-ssh-keys --node-vm-size Standard_B2s 
```

Enter fullscreen mode Exit fullscreen mode

集群名称可以是任何名称，我选择使用我的公司名称，它与资源组名称相匹配。如您所见，我为我的群集和 B2s 虚拟机大小指定了三个节点。

这个过程相当漫长，所以让它运行至少 20 分钟或更长时间。最后，它会产生一个 JSON 输出，您可能会记录下来，但我没有使用过。

为了能够在新集群中使用 kubectl，您需要将集群上下文添加到您的`.kube/config`文件中。使用 Azure CLI 可以轻松做到这一点。

```
az aks get-credentials -g ubiquitous -n ubiquitous 
```

Enter fullscreen mode Exit fullscreen mode

在这里，`-g`是`--group`和`-n`的快捷方式——对于`--name`，您可以在这里用
指定集群的名称。

* * *

接下来就是安装 GitLab CE 了。在 Kubernetes 上安装它的最佳方式是使用综合舵图表。如果你不了解 Helm，在这里了解更多[。](https://helm.sh/)

> 注意:它将被一个新的“云就绪”图表所取代，该图表目前处于 alpha 阶段
> 。

首先，如果你没有头盔，你需要先安装它。每个平台的安装都不同，所以最好的方法是参考[安装说明](https://github.com/kubernetes/helm/blob/master/docs/install.md)。

安装 Helm 后，我们需要安装 Kubernetes 中的 Helm 代理 Tiller。运行这个命令很容易做到:

```
helm init 
```

Enter fullscreen mode Exit fullscreen mode

因为您已经将当前的 Kubernetes 上下文切换到了新的集群，所以它将在默认的名称空间中安装 Tiller。

然后，我们需要添加 GitLab 头盔库:

```
helm repo add gitlab 
```

Enter fullscreen mode Exit fullscreen mode

在安装之前，请记住 GitLab 将使用 kube-lego 使用 Let's Encrypt 为您的入口创建 SSL。但是您需要有一个域，它将用于您的 GitLab 实例。您还需要控制该域的 DNS。这是因为需要添加一个通配符 DNS 条目并指向 Ingress，这将由 GitLab 配置。

在这里，您可以预先为您的集群获取一个外部 IP 地址，创建一个 DNS 条目并在 Helm deployment 命令中指定该地址。或者像我一样，依靠 Azure 来分配 IP 地址，但在这种情况下，您只有几分钟的时间来创建 DNS 条目，因为您需要在 Ingress 获得地址后、GitLab 部署完成之前完成创建。这是我以前用的方法。

所以，安装是通过这个命令执行的(记住使用自己的域名):

```
helm install --name gitlab --set provider=acs gitlab/gitlab-omnibus 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我选择使用`app.ubiquitous.no`子域，所以我需要为它添加一个 DNS 条目。

为了查看 IP 地址是否已经可用，我使用了这个命令:

```
kubectl get svc -w --namespace nginx-ingress nginx 
```

Enter fullscreen mode Exit fullscreen mode

首先，它产生输出，其中`EXTERNAL_IP`将显示为`<pending>`,但让它运行几分钟，直到出现新的一行并显示新分配的外部 IP 地址。

现在，快速为您的(子)域名添加一个`A` DNS 条目，并使用这个新地址。在我的例子中，DNS 条目是:

```
A   *.app.ubiquitous.no   23.96.2.189 
```

Enter fullscreen mode Exit fullscreen mode

> 我有意在这里公开真正的细节，因为这是一个包含非生产数据的测试集群。此外，它相当安全。

此时，GitLab 实例仍在部署中。Azure 在完成持久卷声明方面速度不是很快，在此之前，GitLab pods 不会运行。我花了大约 20 分钟的时间来设置一切。

* * *

现在，在所有工作完成之后，我在 Azure 的 Kubernetes 集群上运行了一个 GitLab CE 实例。赫尔姆部署告诉 Ingress 使用几个主机名:`gitlab`、`mattermost`和`registry`。这些主机名适用于为安装指定的子域。所以在我的例子中，我通过进入[https://GitLab . app . ubiquitous . no](https://gitlab.app.ubiquitous.no)来访问 git lab。当我第一次去那里时，我能够为 root 用户指定新密码，所以我的建议是第一次登录时尽可能快。

默认包含哪些内容？嗯，相当多:

*   GitLab CE 本身，它托管您的 Git 存储库并处理问题和 wikis。
*   用于 Kubernetes 的 GitLab Runner，它用于使用 CI/CD 管道构建和部署您的应用程序。
*   GitLab Docker Registry，允许您在每个项目的单独 Docker 存储库中托管 Docker 映像。
*   Mattermost 聊天系统。
*   Prometheus 时序数据库，它将为 GitLab 本身以及 CI 管道将部署的 pod 收集指标。

因此，Kubernetes 集成是开箱即用的，因此，如果您的存储库中有 docker 文件，或者您的应用程序可以使用默认的 Heroku buildpacks 之一构建，则您可以使用其默认的自动部署 CI/CD 配置并开始构建和部署您的应用程序到 Kubernetes。点击阅读更多关于自动部署
[的信息。](https://docs.gitlab.com/ee/topics/autodevops/index.html#auto-deploy)

普罗米修斯没有暴露在外界，所以如果你想到达它，你可以使用这个命令:

```
kubectl port-forward svc/gitlab-gitlab 9090:9090 
```

Enter fullscreen mode Exit fullscreen mode

要设置到 GitLab 服务的端口转发，然后打开 [http://localhost:9090](http://localhost:9090) 到达 Prometheus UI。

稍后，我将描述如何在 GitLab 中使用 Auto Deploy 来构建和部署 ASP.NET 核心应用程序。

* * *

最后，如果需要对 GitLan 进行一些配置，您需要更改 GitLab 请求配置的 Ruby 文件。由于配置存储在永久卷上，因此可以安全地对其进行更改，并且更改将在 pod 或整个集群重新启动后保留。

但是由于 pod 既没有 root 登录也没有 SSH 证书，所以配置 GitLab 最简单的方法是使用 kubectl exec。为了使用它，你必须知道 GitLab 运行的 pod 的名称。为此，您可以列出所有窗格:

```
$ kubectl get pods
NAME                                        READY     STATUS    RESTARTS   AGE
gitlab-gitlab-764bd7665-w94t6               1/1       Running   1          22h
gitlab-gitlab-postgresql-5fff4f67bb-lp74p   1/1       Running   0          22h
gitlab-gitlab-redis-6c88945d56-gm5qc        1/1       Running   0          22h
gitlab-gitlab-runner-f7c85548-bl8c5         1/1       Running   7          22h 
```

Enter fullscreen mode Exit fullscreen mode

你可以看到一些 pods 来运行 Postgresql，Redis，GitLab Runner，但是第一个 pos 是我们需要的。因此，如果我们运行这个命令:

```
kubectl exec -it gitlab-gitlab-764bd7665-w94t6 -- /bin/bash 
```

Enter fullscreen mode Exit fullscreen mode

我们将在 sudo 模式下得到 pod bash 提示符。从那里一切都很容易。要更改配置，只需编辑 Ruby 文件:

```
vim /etc/gitlab/gitlab.rb 
```

Enter fullscreen mode Exit fullscreen mode

如果您不熟悉 vim，当然可以使用 nano 之类的东西。

当所有的修改完成后，保存文件，退出编辑器并运行这些命令:

```
gitlab-ctl reconfigure
gitlab-ctl restart 
```

Enter fullscreen mode Exit fullscreen mode

* * *

GitLabbing 快乐！