# 将 Prisma 集群部署到 Kubernetes

> 原文：<https://dev.to/andre/deploying-a-prisma-cluster-to-kubernetes--3lbi>

*这篇文章最初是作为[Prisma 官方文档](https://www.prismagraphql.com/docs/tutorials/cluster-deployment/kubernetes-aiqu8ahgha)的教程而写的。*

## 动机

在本教程中，你将学习如何在 [Kubernetes](https://kubernetes.io/) 上部署 [Prisma](http://prismagraphql.com/) 服务器。

Prisma 是围绕数据库的一个薄层，它公开了一个与数据交互的 GraphQL API。它通过定义一个自己的 GraphQL API 来与这个特定的 Prisma 服务进行通信，从而帮助您构建自己的业务逻辑。

Kubernetes 是一个容器编排器，帮助您部署和扩展容器化的应用程序。

本教程中的设置假设您有一个正在运行的 Kubernetes 集群。有几个提供商可以让您建立和维护生产级集群。本教程旨在成为提供者不可知的，因为 Kubernetes 实际上是抽象层。唯一略有不同的部分是创建`persistent volumes`的机制。出于演示的目的，我们在本教程中使用了[谷歌云平台](https://cloud.google.com/)上的 [Kubernetes 引擎](https://cloud.google.com/kubernetes-engine)。

我为你编译了这个[库](https://github.com/akoenig/prisma-kubernetes-deployment)中的所有文件，这样你就不用复制粘贴这篇博文了:)

## 先决条件

如果您以前没有这样做过，那么在 Kubernetes 上部署 Prisma 集群之前，您需要满足以下先决条件。你需要...

*   ...正在运行的 Kubernetes 集群(例如在 Google 云平台上)
*   ...一个本地版本的 [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) ，它被配置为与您正在运行的 Kubernetes 集群进行通信

现在，您可以在本地机器上创建一个新目录——称之为`kubernetes-demo`。这将是我们旅程的参考目录。

## 创建单独的名称空间

你可能知道，Kubernetes 附带了一个叫做`namespace`的原语。这允许您对应用程序进行逻辑分组。在集群上应用实际的名称空间之前，我们必须为它编写定义文件。在我们的项目目录中，创建一个名为`namespace.yml`的文件，内容如下:

```
apiVersion: v1
kind: Namespace
metadata:
  name: prisma 
```

Enter fullscreen mode Exit fullscreen mode

这个定义将导致一个新的名称空间，称为`prisma`。现在，在`kubectl`的帮助下，您可以通过执行
来应用名称空间

```
kubectl apply -f namespace.yml 
```

Enter fullscreen mode Exit fullscreen mode

之后，您可以执行一个`kubectl get namespaces`来检查实际的名称空间是否已经创建。您应该在一个新的 Kubernetes 集群上看到以下内容:

```
❯ kubectl get namespaces
NAME            STATUS    AGE
default         Active    1d
kube-public     Active    1d
kube-system     Active    1d
prisma          Active    2s 
```

Enter fullscreen mode Exit fullscreen mode

## [MySQL](#mysql)

现在我们有了一个可以使用的有效名称空间，是时候部署 MySQL 了。Kubernetes 区分无状态和有状态部署。数据库本质上是有状态部署，需要一个磁盘来实际存储数据。正如上面介绍中所述，每个云提供商都有不同的创建磁盘的机制。在[谷歌云平台](https://cloud.google.com)的情况下，你可以通过以下步骤创建一个磁盘:

1.  打开[谷歌云控制台](https://console.cloud.google.com)
2.  转到[磁盘部分](https://console.cloud.google.com/compute/disks)并选择`Create`

请在表格中填写以下信息:

*   **名称:**应为`db-persistence`
*   **区域:**您的 Kubernetes 集群的节点所部署的区域，例如`europe-west-1c`
*   **磁盘类型:**用于生产场景`SSD persistent disk`
*   **来源类型:** `None (blank disk)`
*   **尺寸(GB):** 选择符合您要求的尺寸
*   **加密:** `Automatic (recommended)`

选择`Create`实际创建磁盘。

*注意:*为了简单起见，我们手动创建了上面的磁盘。您也可以通过 [Terraform](https://www.terraform.io/) 配置磁盘来自动化这个过程，但这超出了本教程的范围。

### 部署 MySQL Pod

现在我们有了数据库磁盘，是时候创建 MySQL 实例的实际部署定义了。一个简短的提醒:Kubernetes 附带了`Pods`和`ReplicationControllers`的原语。

一个`Pod`就像一个“虚拟机”,容器化的应用程序在其中运行。它获得自己的内部 IP 地址和(如果已配置)连接到它的磁盘。`ReplicationController`负责在集群节点上调度您的`Pod`,并确保它们按照配置运行和伸缩。

在 Kubernetes 的旧版本中，有必要分别配置它们。在最近的版本中，有一个新的定义资源，叫做`Deployment`。在这样的配置中，您可以定义想要使用哪种容器映像、应该运行多少副本，以及在我们的示例中应该装载哪个磁盘。

我们的 MySQL 数据库的部署定义如下:

```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: database
  namespace: prisma
  labels:
    stage: production
    name: database
    app: mysql
spec:
  replicas: 1
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        stage: production
        name: database
        app: mysql
    spec:
      containers:
        - name: mysql
          image: 'mysql:5.7'
          args:
            - --ignore-db-dir=lost+found
            - --max-connections=1000
            - --sql-mode=ALLOW_INVALID_DATES,ANSI_QUOTES,ERROR_FOR_DIVISION_BY_ZERO,HIGH_NOT_PRECEDENCE,IGNORE_SPACE,NO_AUTO_CREATE_USER,NO_AUTO_VALUE_ON_ZERO,NO_BACKSLASH_ESCAPES,NO_DIR_IN_CREATE,NO_ENGINE_SUBSTITUTION,NO_FIELD_OPTIONS,NO_KEY_OPTIONS,NO_TABLE_OPTIONS,NO_UNSIGNED_SUBTRACTION,NO_ZERO_DATE,NO_ZERO_IN_DATE,ONLY_FULL_GROUP_BY,PIPES_AS_CONCAT,REAL_AS_FLOAT,STRICT_ALL_TABLES,STRICT_TRANS_TABLES,ANSI,DB2,MAXDB,MSSQL,MYSQL323,MYSQL40,ORACLE,POSTGRESQL,TRADITIONAL
          env:
            - name: MYSQL_ROOT_PASSWORD
              value: "graphcool"
            - name: MYSQL_DATABASE
              value: "graphcool"
          ports:
            - name: mysql-3306
              containerPort: 3306
          volumeMounts:
            - name: db-persistence
              readOnly: false
              mountPath: /var/lib/mysql
      volumes:
        - name: db-persistence
          gcePersistentDisk:
            readOnly: false
            fsType: ext4
            pdName: db-persistence 
```

Enter fullscreen mode Exit fullscreen mode

当应用时，该定义调度一个 Pod ( `replicas: 1`)，使用基于映像`mysql:5.7`的运行容器，配置环境(将`root`用户的密码设置为`graphcool`)并将磁盘`db-persistence`挂载到路径`/var/lib/mysql`。

要实际应用该定义，请执行:

```
kubectl apply -f database/deployment.yml 
```

Enter fullscreen mode Exit fullscreen mode

您可以通过执行
来检查实际的 Pod 是否已经被调度

```
kubectl get pods --namespace prisma

NAME                        READY     STATUS    RESTARTS   AGE
database-3199294884-93hw4   1/1       Running   0          1m 
```

Enter fullscreen mode Exit fullscreen mode

它跑了！

## 部署 MySQL 服务

在进入这一部分之前，这里有一个简短的回顾。

我们的 MySQL 数据库 pod 现在正在集群内部运行并可用。记住，Kubernetes 给`Pod`分配一个本地 IP 地址，以便另一个应用程序可以访问数据库。

现在，想象一个数据库崩溃的场景。集群管理系统将处理这种情况，并再次调度`Pod`。在这种情况下，Kubernetes 将分配一个不同的 IP 地址，这会导致与数据库通信的应用程序崩溃。

为了避免这种情况，集群管理器提供了内部 DNS 解析机制。您必须使用不同的原语，称为`Service`，才能从中受益。服务是一个内部负载平衡器，可以通过`service name`到达。它的任务是将流量转发到您的`Pod(s)`，并使它可以通过它的名称到达整个集群。

我们的 MySQL 数据库的服务定义应该是这样的:

```
apiVersion: v1
kind: Service
metadata:
  name: database
  namespace: prisma
spec:
  ports:
  - port: 3306
    targetPort: 3306
    protocol: TCP
  selector:
    stage: production
    name: database
    app: mysql 
```

Enter fullscreen mode Exit fullscreen mode

该定义将创建一个名为`database`的内部负载平衡器。然后，可以在`prisma`名称空间中通过这个名称访问服务。关于`spec`一节的一点解释:

*   **港口:**在这里，您将服务港口映射到实际的集装箱港口。在这种情况下，映射是从`3306`到`3306`。
*   **选择器:**一种查询。负载平衡器通过选择带有指定标签的来识别`Pods`。

创建此文件后，您可以使用:
应用它

```
kubectl apply -f database/service.yml 
```

Enter fullscreen mode Exit fullscreen mode

要验证服务是否启动，请执行:

```
kubectl get services --namespace prisma

NAME       TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)    AGE
database   ClusterIP   10.3.241.165   <none>        3306/TCP   1m 
```

Enter fullscreen mode Exit fullscreen mode

## 棱镜

好吧，很公平，数据库已经部署好了。接下来:部署实际的 Prisma 服务器，它负责充当 Prisma CLI 的端点。

该应用程序与已经部署的`database`服务通信，并将其用作存储后端。因此，Prisma 服务器是一个无状态应用程序，因为它不需要任何额外的磁盘存储。

### 展开棱镜吊舱

部署实际的 Prisma 服务器在 Pod 中运行是非常简单的。首先你必须定义部署定义:

```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: prisma
  namespace: prisma
  labels:
    stage: production
    name: prisma
    app: prisma
spec:
  replicas: 1
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        stage: production
        name: prisma
        app: prisma
    spec:
      containers:
        - name: prisma
          image: 'prismagraphql/prisma:1.1'
          ports:
            - name: prisma-4466
              containerPort: 4466
          env:
            - name: PORT
              value: "4466"
            - name: SQL_CLIENT_HOST_CLIENT1
              value: "database"
            - name: SQL_CLIENT_HOST_READONLY_CLIENT1
              value: "database"
            - name: SQL_CLIENT_HOST
              value: "database"
            - name: SQL_CLIENT_PORT
              value: "3306"
            - name: SQL_CLIENT_USER
              value: "root"
            - name: SQL_CLIENT_PASSWORD
              value: "graphcool"
            - name: SQL_CLIENT_CONNECTION_LIMIT
              value: "10"
            - name: SQL_INTERNAL_HOST
              value: "database"
            - name: SQL_INTERNAL_PORT
              value: "3306"
            - name: SQL_INTERNAL_USER
              value: "root"
            - name: SQL_INTERNAL_PASSWORD
              value: "graphcool"
            - name: SQL_INTERNAL_DATABASE
              value: "graphcool"
            - name: SQL_INTERNAL_CONNECTION_LIMIT
              value: "10"
            - name: CLUSTER_ADDRESS
              value: "http://prisma:4466"
            - name: BUGSNAG_API_KEY
              value: ""
            - name: ENABLE_METRICS
              value: "0"
            - name: JAVA_OPTS
              value: "-Xmx1G"
            - name: SCHEMA_MANAGER_SECRET
              value: "graphcool"
            - name: SCHEMA_MANAGER_ENDPOINT
              value: "http://prisma:4466/cluster/schema"
            - name: CLUSTER_PUBLIC_KEY
              value: "GENERATE VIA https://api.cloud.prisma.sh/" 
```

Enter fullscreen mode Exit fullscreen mode

这个配置看起来类似于 MySQL 数据库的部署配置。我们告诉 Kubernetes，它应该调度服务器的一个副本，并相应地定义环境变量。如你所见，我们对每个`SQL_*_HOST*`变量使用了名称`database`。这是因为这个`Pod`将在与数据库服务相同的名称空间中运行 Kubernetes DNS 服务器使这成为可能。

在应用这个定义之前，我们必须生成一个公钥/私钥对，以便 CLI 能够与这个 Prisma 服务器通信。前往[https://api.cloud.prisma.sh/](https://api.cloud.prisma.sh/)并执行以下查询:

```
{
  generateKeypair {
    public
    private
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

确保将这些值存储在安全的地方！现在，复制`public`键并粘贴到`prisma/deployment.yml`中`CLUSTER_PUBLIC_KEY`环境变量的`value`中。

之后，我们准备应用部署定义:

```
kubectl apply -f prisma/deployment.yml 
```

Enter fullscreen mode Exit fullscreen mode

如前几节所述:为了检查 Prisma 服务器是否已经被调度到 Kubernetes 集群上，执行:

```
kubectl get pods --namespace prisma

NAME                        READY     STATUS    RESTARTS   AGE
database-3199294884-93hw4   1/1       Running   0          5m
prisma-1733176504-zlphg     1/1       Running   0          1m 
```

Enter fullscreen mode Exit fullscreen mode

耶！Prisma 服务器正在运行！开始我们的下一步也是最后一步:

### 部署 Prisma 服务

好的，酷，数据库`Pod`正在运行，它前面有一个内部负载均衡器，Prisma 服务器`Pod`也在运行，但是缺少负载均衡器，又名`Service`。让我们来解决这个问题:

```
apiVersion: v1
kind: Service
metadata:
  name: prisma
  namespace: prisma
spec:
  ports:
  - port: 4466
    targetPort: 4466
    protocol: TCP
  selector:
    stage: production
    name: prisma
    app: prisma 
```

Enter fullscreen mode Exit fullscreen mode

通过
应用

```
kubectl apply -f prisma/service.yml 
```

Enter fullscreen mode Exit fullscreen mode

好了，搞定！Prisma 服务器现在可以通过其名称`prisma`在 Kubernetes 集群中访问。

仅此而已。Prisma 在 Kubernetes 上跑！

最后一步是配置您的本地`Prisma CLI`,以便您可以与 Kubernetes 集群上的实例通信。

如果您想将`prisma deploy`整合到您的 CI/CD 流程中，即将到来的最后一步也是必要的。

## Prisma CLI 配置

Prisma 服务器运行在 Kubernetes 集群上，有一个内部负载平衡器。这是一个合理的安全默认，因为您不会将 Prisma 服务器直接暴露给公众。相反，您可以开发一个 GraphQL API，并将其部署到 Kubernetes 集群中。

您可能会问:“好吧，但是当我不能直接与 Prisma 服务器通信时，我如何执行`prisma deploy`来填充我的数据模型呢？`. That is indeed a very good question!` kubectl `提供了一种机制，允许将本地端口转发给位于 Kubernetes 集群上的应用程序。

因此，每当您想要与 Kubernetes 集群上的 Prisma 服务器通信时，您必须执行以下步骤:

1.  `kubectl get pods --namespace prisma`识别 pod 名称
2.  `kubectl port-forward --namespace prisma <the-pod-name> 4467:4466`–这将从`127.0.0.1:4467` - > `kubernetes-cluster:4466`转发

现在可以通过`http://localhost:4467`访问 Prisma 服务器。有了这些，我们就可以配置 CLI 了:

``
棱镜集群添加`

 `？请提供集群端点[http://localhost:4467](http://localhost:4467)
？请提供集群密码
？请为您的集群 kubernetes
` `提供一个名称`

 `好吧，你做到了！祝贺您，您已经成功地将 Prisma 服务器部署到生产 Kubernetes 集群环境中。``