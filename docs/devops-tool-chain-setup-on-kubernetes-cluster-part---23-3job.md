# Kubernetes 集群上的 DevOps 工具链设置。第二部分/第三部分

> 原文：<https://dev.to/sr_balaji/devops-tool-chain-setup-on-kubernetes-cluster-part---23-3job>

[![alt text](img/5d339f1dbd1e21f29694d92d2181cda4.png "Kubernetes")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cemFoOyb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/298l0v54pjqkldy2j34e.png)

### 简介

这是我上一篇文章 Kubernetes 集群上的 DevOps 工具链设置——的延续。第三部分。在本文中，我介绍了如何在 Kubernetes 集群上设置 SonarQube 和 PostgreSQL。

### Kubernetes 集群上的 SonarQube 设置

SonarQube 是一个基于网络的开源平台，用于测量和分析源代码质量。代码质量分析使您的代码更加可靠和可读。它可以分析和管理 20 多种编程语言的代码。

SonarQube 需要一个数据库，它支持 MySQL、PostgreSQL、MSSQL 等数据库。在本文中，我使用 PostgreSQL 作为 SonarQube 的数据库。我遵循了创建 PostgreSQL DB kubernetes 组件，然后创建 SonarQube Kubernetes 组件的顺序。

让我使用下面的脚本在 Kubernetes 集群上配置 PostgreSQL 和 SonarQube。

### Kubernetes 集群上的 PostgreSQL 设置

在本节中，我使用下面的脚本在 Kubernetes 集群上设置了 PostSQL。

#### PostgreSQL 的存储类

我已经为 PostgreSQL 数据库创建了存储类。

```
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: sonar-storage
  labels: 
    app: sonar-storage
provisioner: kubernetes.io/aws-ebs
parameters:
  type: gp2
  zone: eu-west-2a
allowVolumeExpansion: true 
```

Enter fullscreen mode Exit fullscreen mode

#### PostgreSQL 的持久卷

我已经创建了持久卷，并分配了 4GB 作为 PostgreSQL 的存储空间。

```
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: sonar-postgres-data
  labels: 
    app: Sonar-Postgres-Data
  annotations:
    volume.beta.kubernetes.io/storage-class: "sonar-storage"
spec:
  accessModes: 
    - ReadWriteOnce
  resources:
    requests: 
      storage: 4Gi 
```

Enter fullscreen mode Exit fullscreen mode

#### 为 PostgreSQL 部署

我已经创建了一个部署脚本，如果映像不在 Kubernetes 集群中，它将提取 PostgreSQL 映像。我已经用端口 5432 配置了 PostgreSQL。它的数据保存在上一步中创建的持久卷上。此部署是使用单个副本创建的。PostgreSQL 数据库凭据(用户名和密码)以明文形式提及。在生产环境中，不建议使用明文形式的密码。

```
kind: Deployment
apiVersion: extensions/v1beta1
metadata:
  name: postgresql-sonar
spec:
  replicas: 1
  template:
    metadata: 
      name: postgresql-sonar
      labels:
        app: postgresql-sonar
    spec:
      containers:
        - name: postgreqsl-sonar
          image: postgres:9.6
          imagePullPolicy: IfNotPresent
          ports:
            - containerPort: 5432
              name: postgresql-port
          env:
            - name: POSTGRES_USER
              value: sonar
            - name: POSTGRES_PASSWORD
              value: password
            - name: PGDATA
              value: /var/lib/postgresql/data/pgdata
          volumeMounts:
            - name: db-data
              mountPath: /var/lib/postgresql/data
      volumes:
        - name: db-data
          persistentVolumeClaim:
            claimName: sonar-postgres-data 
```

Enter fullscreen mode Exit fullscreen mode

#### 为 PostgreSQL 服务

我已经在端口 5432 上为 PostgreSQL 创建了服务。

```
kind: Service
apiVersion: v1
metadata:
  name: postgresql-db
  labels:
    name: postgresql-db
spec:
  type: NodePort
  ports:
  - port: 5432
    targetPort: 5432
    protocol: TCP
    name: postgresql-db-port
  selector:
    app: postgresql-sonar 
```

Enter fullscreen mode Exit fullscreen mode

#### 声纳立方体配置

在前面几节中，我已经为 SonarQube 数据库创建了所有必需的 Kubernetes 组件。现在，我将为 SonarQube 创建 Kubernetes 组件，它将使用所有的数据库组件。

#### 为 SonarQube 部署

我已经创建了一个部署脚本，它提取 SonarQube 映像，如果它不存在于 Kubernetes 集群中并配置在端口 9000 上的话。我已经配置了 SonarQube 来连接 PostgreSQL DB，它通过提供用户凭证暴露在端口 5432 上。

```
kind: Deployment
apiVersion: extensions/v1beta1
metadata:
  name: sonar
spec:
  replicas: 1
  template:
    metadata:
      name: sonar
      labels: 
        app: sonar
    spec: 
      containers:
      - name: sonar
        image: sonarqube:6.7
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 9000
          name: sonarport
        env: 
          - name: SONARQUBE_JDBC_USERNAME
            value: sonar
          - name: SONARQUBE_JDBC_PASSWORD
            value: password
          - name: SONARQUBE_JDBC_URL
            value: jdbc:postgresql://postgresql-db:5432/sonar 
```

Enter fullscreen mode Exit fullscreen mode

#### 为 SonarQube 服务

我已经为 SoanrQube 在其默认端口 9000 上创建了一个服务。

```
kind: Service
apiVersion: v1
metadata:
  name: sonar
  labels:
    app: sonar
spec:
  type: NodePort
  ports:
    - port: 9200
      targetPort: 9000
      name: sonarport
  selector:
    app: sonar
  type: LoadBalancer 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，我已经为 SonarQube 和 PostgreSQL DB 创建了存储类、持久卷、部署和服务，并且它已经启动并运行了。

```
$kubectl get deployment
$kubectl get pod
$kubectl get svc 
```

Enter fullscreen mode Exit fullscreen mode

### 结论

到目前为止，我已经介绍了如何在 Kubernetes 集群上用单个副本集设置 SonarQube 和 PostgreSQL。我将在最后一篇文章中讨论 Nexus 的设置。

注意:
yaml 文件是空间敏感的，本文中可用的脚本可能有制表符而不是空格。如果您在复制和粘贴时没有将制表符更改为空白，这些脚本将会失败。