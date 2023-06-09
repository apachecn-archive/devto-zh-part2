# Kubernetes 集群上的 DevOps 工具链设置。第三部分

> 原文：<https://dev.to/sr_balaji/devops-tool-chain-setup-on-kubernetes-cluster-part---33-506>

[![alt text](img/5d339f1dbd1e21f29694d92d2181cda4.png "Kubernetes")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cemFoOyb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/298l0v54pjqkldy2j34e.png)

### 简介

这是我之前关于 Kubernetes 集群上 DevOps 工具链设置的文章的第 3 部分，也是最后一部分。在本文中，我解释了如何在 Kubernetes 集群上设置 Nexus。

### Kubernetes 集群上的 Nexus 设置

Nexus 是一个工件库，它在软件开发生命周期中扮演着重要的角色，尤其是在 Docker 容器时代。我创建了下面的 Kubernetes 组件列表，用于在集群上托管 Nexus 工件存储库。

#### 存储类为 Nexus

我已经为 Nexus 创建了存储类。

```
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: nexus-storage-data
  labels:
    app: nexus-storage-data
provisioner: kubernetes.io/aws-ebs
parameters:
  type: gp2
  zone: eu-west-2a
allowVolumeExpansion: true 
```

Enter fullscreen mode Exit fullscreen mode

#### 持续量为 Nexus

我在 Kubernetes 集群上为 Nexus 创建了存储空间为 2GB 的持久性卷。

```
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: nexus-storage
  labels:
    app: nexus-storage
  annotations:
    volume.beta.kubernetes.io/storage-class: "nexus-storage-data"
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 2Gi 
```

Enter fullscreen mode Exit fullscreen mode

#### 为 Nexus 部署

我已经创建了一个部署脚本，如果 Nexus 映像不在 Kubernetes 集群中，它将提取该映像并在端口 8081 上进行配置。我已经为 Nexus 用户配置了用户组。

```
kind: Deployment
apiVersion: extensions/v1beta1
metadata:
  name: nexus
spec:
  replicas: 1
  template:
    metadata:
      name: nexus
      labels: 
        app: nexus
    spec:
      securityContext:
        fsGroup: 2000
      containers:
        - name: nexus
          image: sonatype/nexus3:3.8.0
          imagePullPolicy: IfNotPresent
          ports:
            - containerPort: 8081
              name: nexusport
          volumeMounts:
            - name: nexus-data
              mountPath: /nexus-data
      volumes:
        - name: nexus-data
          persistentVolumeClaim:
            claimName: nexus-storage 
```

Enter fullscreen mode Exit fullscreen mode

#### 为 Nexus 服务

我已经在端口 8081
上为 Nexus 创建了服务

```
kind: Service
apiVersion: v1
metadata:
  name: nexus
  labels:
    app: nexus-svc
spec:
  type: NodePort
  ports:
    - port: 8081
      targetPort: 8081
      name: nexusport
  selector:
    app: nexus
  type: LoadBalancer 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，我已经为 Nexus 创建了存储类、持久卷、部署和服务，并且已经启动并运行。

```
$kubectl get deployment
$kubectl get pod
$kubectl get svc 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，已经为 CI/CD 管道创建了所有 Kubernetes 组件，可以通过执行命令或使用 Kubernetes 仪表板来验证。在下面的部分中，我将使用 Kubernetes 仪表板来验证我已经创建的组件。

### Kubernetes 集群组件

下面是 Kubernetes 仪表板，其中包含作为该集群设置的一部分而创建的组件的详细信息。

#### 持久卷

下面是包含持久卷列表的屏幕截图

[![alt text](img/eab666435b2ba8ba59e393b4b110e57a.png "Persistence Volumes")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6xitzTIv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/io9fmoge2y2h3cbot53e.png)

存储类别–下面是存储类别列表
[![alt text](img/e20411f3ba93eec28fc56fa4deda2a2d.png "Storage Classes")](https://res.cloudinary.com/practicaldev/image/fetch/s--sBMrD0JA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/goimsgrhxan82ww4t08d.png)

部署–下面是部署列表
[![alt text](img/f0bed7c08d580f0add3fa52e1376d308.png "Deployments")](https://res.cloudinary.com/practicaldev/image/fetch/s--PPlwkUXK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/35dol31hs59crz8fch9c.png)

服务–下面是服务列表
[![alt text](img/e264a05bff93cf49204eacdc58a81413.png "Services")](https://res.cloudinary.com/practicaldev/image/fetch/s--HbB2NaaR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gejiin3vxkdvyw8fd4vy.png)

### 结论

到目前为止，我已经介绍了整个 CI/CD 工具集——Jenkins、使用 PostgreSQL 的 sonarqube 和使用 kubernetes 集群上的单个副本设置的 nexus。这可用于在 kubernetes 集群上建立 DevOps 管道。