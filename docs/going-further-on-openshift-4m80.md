# 在 Openshift 上更进一步

> 原文：<https://dev.to/livioribeiro/going-further-on-openshift-4m80>

在我之前的帖子中，我简要介绍了什么是 Openshift ( [现在是 OKD](https://okd.io) )以及我们如何轻松部署 [Gitea](https://gitea.io) 。现在我们将关注使用“source 2 image”(S2I)策略构建和部署 java 应用程序。

但是这个 S2I 是什么？

据 [S2I 项目现场](https://github.com/openshift/source-to-image):

> 源到图像(S2I)是一个工具包和工作流程，用于从源代码构建可复制的 Docker 图像。S2I 通过将源代码注入 Docker 容器，并让容器准备源代码以供执行，从而生成现成的映像。通过创建自组装构建器映像，您可以像使用 Docker 映像版本化您的运行时环境一样版本化和控制您的构建环境。

实际上，Openshift 将获取您的项目源代码，将其与一个 *builder* docker 映像相结合，并生成用于运行应用程序的最终运行时映像。

## 准备开发环境

为了运行我们的开发环境，我们将使用 [minishift](https://github.com/minishift/minishift) 来创建一个 openshift 集群。使用以下命令下载并安装 minishift(如果您使用的是 linux):

```
MINISHIFT_VERSION=1.22.0
MINISHIFT_URL=https://github.com/minishift/minishift/releases/download/v$MINISHIFT_VERSION/minishift-$MINISHIFT_VERSION-linux-amd64.tgz

curl -L -s -o /tmp/minishift.tgz $MINISHIFT_URL

tar -C /tmp --wildcards -x */minishift --strip 1 -zf /tmp/minishift.tgz
sudo install /tmp/minishift /usr/local/bin/minishift 
```

Enter fullscreen mode Exit fullscreen mode

安装 minishift 后，我们可以配置并启动它(这可能需要几分钟):

```
# change values according to your workstation
MINISHIFT_VM_DRIVER=virtualbox
MINISHIFT_MEMORY=8G
MINISHIFT_CPUS=4
MINISHIFT_DISK_SIZE=100G

# configure minishift
minishift config set vm-driver $MINISHIFT_VM_DRIVER
minishift config set memory $MINISHIFT_MEMORY
minishift config set cpus $MINISHIFT_CPUS
minishift config set disk-size $MINISHIFT_DISK_SIZE

minishift start 
```

Enter fullscreen mode Exit fullscreen mode

为了与 Openshift 交互，我们需要下载`oc`命令行工具，但是使用 minishift 我们可以使用捆绑的工具，只需执行以下命令:

```
eval $(minishift oc-env) 
```

Enter fullscreen mode Exit fullscreen mode

然后我们登录 openshift:

```
oc login 192.168.99.100:8443 -u admin
# use 'admin' as password 
```

Enter fullscreen mode Exit fullscreen mode

## 部署应用程序

现在我们已经启动并运行了 minishift，我们可以开始部署一些应用程序了。让我们从一个 [django 示例应用程序](https://github.com/sclorg/django-ex)开始。

但是首先，让我们创建*项目* :

```
oc new-project django-example --display-name 'Django Example' 
```

Enter fullscreen mode Exit fullscreen mode

它会打印出这样的内容

```
Now using project "django-example" on server "https://192.168.99.100:8443".

You can add applications to this project with the 'new-app' command. For example, try:

    oc new-app centos/ruby-22-centos7~https://github.com/openshift/ruby-ex.git

to build a new example application in Ruby. 
```

Enter fullscreen mode Exit fullscreen mode

(不要担心关于 ruby 应用程序的消息，我们实际上正在部署一个 **python** 应用程序；)

一个*项目*是一个名称空间的 openshift 术语，一个对相关资源进行分组并定义对这些资源的访问规则的地方。例如，我们可以允许用户“snake”查看我们的“django-example”项目，允许用户“rattlesnake”查看和编辑(触发构建和部署，等等)。)并且不给用户“badger”任何访问权。

随着项目的创建，我们可以创建应用程序:

```
oc new-app python:3.6~https://github.com/sclorg/django-ex.git 
```

Enter fullscreen mode Exit fullscreen mode

你可能会问“这是怎么回事？”。嗯，我们告诉 openshift 获取`python:3.6`图像，并将其与在[https://github.com/sclorg/django-ex.git](https://github.com/sclorg/django-ex.git)可用的源代码相结合。语法是“image”~“source”。

**但是要小心！**`python:3.6`是**不是 docker hub** 的官方 python 图片！而是来自 [centos/python-36-centos7](https://hub.docker.com/r/centos/python-36-centos7/) 并被导入到 openshift 集成注册表中！构建和运行 python 项目是 S2I 的形象！您可以使用命令`oc get imagestream -n openshift`查看其他图像

`oc new-app`的输出将有一些关于应用程序的信息，在最后有如下内容:

```
--> Success
    Build scheduled, use 'oc logs -f bc/django-ex' to track its progress.
    Application is not exposed. You can expose services to the outside world by executing one or more of the commands below:
     'oc expose svc/django-ex' 
    Run 'oc status' to view your app. 
```

Enter fullscreen mode Exit fullscreen mode

建议的命令`oc expose svc/django-ex`将做一些非常重要的事情:它将通过*路由*向外部流量公开一个*服务*(一个 Kubernetes 服务)。*路由*非常类似于服务，但是将处理匹配特定主机名的外部请求，并且还可以处理加密(https)。

让我们公开我们的应用程序:

```
oc expose svc/django-ex 
```

Enter fullscreen mode Exit fullscreen mode

与前面的命令不同，公开一个服务不会产生太多的输出，所以让我们列出可用的路由来获得我们刚刚创建的路由:

```
oc get routes 
```

Enter fullscreen mode Exit fullscreen mode

其中将输出:

```
NAME        HOST/PORT                                        PATH      SERVICES    PORT       TERMINATION   WILDCARD
django-ex   django-ex-django-example.192.168.99.100.nip.io             django-ex   8080-tcp                 None 
```

Enter fullscreen mode Exit fullscreen mode

如我们所见，该路由监听主机名`django-ex-django-example.192.168.99.100.nip.io`。这个`nip.io`是一个特殊的服务 service，它将请求重定向到主机名本身包含的 ip 地址，在本例中，是 minishift 虚拟机的 ip 地址 192.168.99.100。

访问路线地址将显示以下屏幕:

[![openshift django example welcome screen](img/cb8fea78aca5c57fe9df5de682b42be7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UVlbVdpo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ym1gv7dgtbkxnh5sguyh.png)

## Openshift web 控制台

Openshift 也有一个非常好的网络控制台，可以在[[https://192 . 168 . 99 . 100:8443/console](https://192.168.99.100:8443/console)访问。只需使用我们之前使用的相同凭证登录即可(用户名和密码均为“admin”)。

[![openshift web console](img/3b000ffdc784b1426a57917d361c2530.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--T858cAYU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/80jf3vky8vxez681m62z.png)

打开右侧的项目链接，我们将进入以下页面:

[![openshift project overview](img/95e21a73660ebadae6632e90fd701445.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Qvbi0UK7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s6cov2vkjuvn09hboghm.png)

这是一个项目控制面板，我们在这里可以看到项目概况、部署的应用程序、路线、容器状态等。我们甚至可以缩放容器！

web 控制台有很多特性，尽管有些操作仍然需要使用 cli，比如通过公开*部署*来创建服务。

暂时就这样了。下次我们将进入网络控制台。