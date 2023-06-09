# 我的 Openshift 之旅

> 原文：<https://dev.to/livioribeiro/my-journey-through-openshift-13f2>

最近我开始使用 [Openshift](https://www.openshift.org/) 工作，因为我工作的公司采用了它。这将是一个非常有趣的旅程，因为我将不得不修改我们目前运行在 JBoss 和 Tomcat 上的服务，使之运行在容器上。

但是首先，Openshift 到底是什么？

根据我的理解，这是一个 Kubernetes 的发行版，致力于提供一种更简单的方法来在容器化的环境中迁移、部署和管理您的应用程序。它隐藏了 Kubernetes 的一些复杂性，同时提供了一些不错的功能，如仪表板和源到图像(S2I)策略。

Openshift 由 RedHat 维护。

S2I 非常有趣:它将专门构建的 docker 映像与应用程序的源代码相结合，并构建另一个随时可以部署的 docker 映像。每个 S2I 映像都专门用于构建和部署不同的技术堆栈。例如，JBoss S2I 映像将在源代码中查找`pom.xml`，运行`mvn package`，将生成的工件复制到部署目录中，并且在运行时，将使用环境变量配置数据源。雄猫 S2I 形象的工作方式类似。RedHat 为 Java、Python、Ruby、Nodejs 等提供图像。

您还可以从 docker hub 部署映像。

## 入门

要开始使用 Openshift，您可以下载 [minishift](https://github.com/minishift/minishift/releases) 工具，它将启动一个运行 Openshift 实例的虚拟机。

但是在我们开始之前，我们应该稍微调整一下虚拟机:

```
minishift config set vm-driver virtualbox
minishift config set memory 8G
minishift config set cpus 4
minishift config set disk-size 100G 
```

Enter fullscreen mode Exit fullscreen mode

这些命令将告诉 minishift:

*   使用 virtualbox 而不是 KVM/Hyper-V/xyve(你仍然可以使用它们)
*   将内存设置为 8GB(但 4GB 也可以)
*   将 CPU 的数量设置为 4，这样我们可以运行更多的容器
*   使用 100GB 的磁盘大小，这样我们就不会用完存储空间

可以对内存和 CPU 使用更低的值，但是会非常有限(有时 openshift 可能会死机)。

要启动 minishift，我们可以运行:

```
minishift start 
```

Enter fullscreen mode Exit fullscreen mode

当机器准备好了，我们就可以运行:

```
eval $(minishift oc-env) 
```

Enter fullscreen mode Exit fullscreen mode

在我们的路径中有`oc`(open shift 命令行工具)。

在我们与 Openshift 交互之前，我们需要登录(使用 admin/admin 作为凭证):

```
oc login 
```

Enter fullscreen mode Exit fullscreen mode

一切都在运行，是时候部署应用程序了。对于这个例子，我们将部署 [Gitea](https://gitea.io) ，一个从 [Gogs](https://gogs.io/) 派生出来的 git 存储库管理器。

但是首先，我们需要创建一个项目(实际上是一个 kubernetes 名称空间):

```
oc new-project scm --display-name "Source Control Management" 
```

Enter fullscreen mode Exit fullscreen mode

现在我们告诉 openshift 从 Gitea docker image:
创建一个“app”

```
oc new-app gitea/gitea:1.5 
```

Enter fullscreen mode Exit fullscreen mode

最终会创造出:

*   一个[图像流](https://docs.openshift.org/latest/dev_guide/managing_images.html)
*   一个 [DeploymentConfig](https://docs.openshift.org/latest/dev_guide/deployments/kubernetes_deployments.html) ，类似于 kubernetes 部署(并将最终被其取代)
*   广告服务

为了向外部世界公开服务，我们可以创建一个[路由](https://docs.openshift.org/latest/architecture/networking/routes.html)

```
oc expose svc/gitea \
    --name gitea-http \
    --port 3000-tcp 
```

Enter fullscreen mode Exit fullscreen mode

`3000-tcp`是服务上端口的名称。

创建路线后，Gitea 现在可以在[http://Gitea-SCM . 192 . 168 . 99 . 100 . nip . io](http://gitea-scm.192.168.99.100.nip.io)上访问

您还可以公开 Gitea 的 ssh 端点

```
oc expose svc/gitea \
    --name gitea-ssh \
    --port 22-tcp \
    --hostname gitea-scm-ssh.192.168.99.100.nip.io 
```

Enter fullscreen mode Exit fullscreen mode

Gitea 正在工作，但是它的数据将在容器重启时丢失，所以我们需要一个持久卷来保存这些数据:

```
# remove automatically create volume
oc volume dc/gitea --remove --name gitea-volume-1

oc volume dc/gitea --add \
    --name 'gitea-volume-data' \
    --type 'pvc' \
    --mount-path '/data/' \
    --claim-name 'gitea-data' \
    --claim-size '1G' 
```

Enter fullscreen mode Exit fullscreen mode

缺少的一点是配置。幸运的是，Gitea 可以使用环境变量来配置[。因此，现在我们将创建一个配置图来保存环境:](https://docs.gitea.io/en-us/install-with-docker/) 

```
oc create configmap gitea-config \
    --from-literal APP_NAME="My Gitea Instance" \
    --from-literal RUN_MODE=prod \
    --from-literal SSH_DOMAIN=gitea-scm-ssh.192.168.99.100.nip.io \
    --from-literal ROOT_URL=http://gitea-scm.192.168.99.100.nip.io \
    --from-literal DB_TYPE=sqlite3 \
    --from-literal INSTALL_LOCK=true 
```

Enter fullscreen mode Exit fullscreen mode

现在将环境添加到 DeploymentConfig:

```
oc set env --from configmap/gitea-config dc/gitea 
```

Enter fullscreen mode Exit fullscreen mode

如果您现在访问我们的 Gitea 实例，您会注意到我们的配置没有被应用！这是因为 Gitea 在我们创建的卷中已经有了一个配置文件。为了解决这个问题，我们需要添加另一个卷，一个 EmptyDir 卷，这样 Gitea 就可以使用我们设置的环境变量来重建配置:

```
oc volume dc/gitea --add \
    --name 'gitea-volume-config' \
    --type 'emptyDir' \
    --mount-path '/data/gitea/conf' 
```

Enter fullscreen mode Exit fullscreen mode

由于应用程序正在创建数据库表，所以它可能需要更长的时间才能准备好，但是您将会看到我们的设置已经应用。

现在我们只需要设置[探测器来监控应用程序的健康状态](https://docs.openshift.org/latest/dev_guide/application_health.html) :

```
oc set probe dc/gitea --liveness \
    --failure-threshold 3 \
    --initial-delay-seconds 5 \
    -- echo ok

oc set probe dc/gitea --readiness \
    --failure-threshold 3 \
    --initial-delay-seconds 10 \
    --get-url=http://:3000/user/login 
```

Enter fullscreen mode Exit fullscreen mode

就绪探测器在部署期间特别有用，因此 openshift 可以判断它是否可以安全地移除旧容器并将请求重定向到新容器。

更新:

我做了一个[模板](https://docs.openshift.org/3.9/dev_guide/templates.html)让部署 Gitea 更容易。[这里有](https://github.com/livioribeiro/devops-stuff/blob/master/openshift/gitea-persistent-template.yaml)。

Gitea 的设置比通过环境变量配置的更多，但是现在从 configmap 设置配置文件(app.ini)不是很好，因为 Gitea 试图写入该文件并失败，因为它是只读的。然而，[有一个 pull 请求来修复那个](https://github.com/go-gitea/gitea/pull/4412)。