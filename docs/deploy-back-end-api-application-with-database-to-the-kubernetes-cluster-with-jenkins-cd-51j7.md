# 使用 Jenkins CD 将带有数据库的后端 api 应用程序部署到 kubernetes 集群

> 原文：<https://dev.to/pongsatt/deploy-back-end-api-application-with-database-to-the-kubernetes-cluster-with-jenkins-cd-51j7>

我们将创建一个连接到 mongodb 的简单 graphql api 应用程序。然后使用 Jenkins CD 部署到 kubernetes 集群。

此外，我们将创建支持 https 的入口，这样我们就可以安全地访问这个应用程序。

这篇文章是系列文章[“在虚拟机上设置你自己的 kubernetes 集群”](https://dev.to/pongsatt/setup-your-own-kubernetes-cluster-on-vms-5ln)的一部分。

**注:**

> 这篇文章假设你是从[上一篇文章](https://dev.to/pongsatt/deploy-static-front-end-application-to-kubernetes-cluster-using-jenkins-cd-and-minio-with-automatic-https-cert-44gk)开始的

## 先决条件:

*   具有存储级支持的运行中的 kubernetes 集群
*   具有管道支持的 Jenkins 服务器
*   Docker 私有存储库

# 构建应用程序

我们将创建一个名为“simpleapi”的项目，这是一个使用 nodejs 的 mongodb 应用程序的 graphql。

### 1。创建项目

```
mkdir simpleapi
cd simpleapi
yarn init -y 
```

### 2。构建 graphql api

```
yarn add graphql-yoga mongodb 
```

在根文件夹中创建“db.js ”,内容如下。

```
const mongoClient = require('mongodb').MongoClient

module.exports = {
    users: () => execute((coll) => coll.find().toArray()
        .then(results => results.map(idToString))),
    createUser: (name) => execute((coll) => coll.insertOne({ name })
        .then(result => idToString(result.ops[0])))
}

function execute(fn) {
    return mongoClient.connect(process.env.DB_URL || 'mongodb://localhost:27017/test')
        .then(client => {
            const result = fn(client.db().collection('users'))
            client.close()
            return result
        })
}

function idToString(doc) {
    const {_id, ...rest} = doc
    return {_id: _id.toString(), ...rest}
} 
```

这个程序通过 url“MongoDB://localhost:27017/test”或来自环境名“DB_URL”的 URL(如果提供的话)连接 mongo。

它还包含查询用户列表和创建新用户两个功能。我们将在下面的“index.js”中使用这些函数。

在根文件夹中创建“index.js ”,内容如下。

```
const { GraphQLServer } = require('graphql-yoga')
const db = require('./db')

const typeDefs = `
  type User {
      _id: ID
      name: String
  }

  type Query {
    users: [User]
  }

  type Mutation {
      createUser(name: String!): User!
  }
`

const resolvers = {
  Query: {
    users: (_, { }) => db.users()
  },
  Mutation: {
    createUser: (_, {name}) => db.createUser(name)
  }
}

const server = new GraphQLServer({ typeDefs, resolvers })
server.start(() => console.log('Server is running on http://localhost:4000')) 
```

在这个程序中，我们定义了 graphql schema 函数来查询用户和用户创建，并在端口 4000 启动服务器。

### 3。测试 graphql api

**注:**

> 您需要在“mongodb://localhost:27017”上运行 mongodb 才能进行测试

通过运行`node index.js`启动服务器，打开 URL“[http://localhost:4000](http://localhost:4000)”。

创建用户。

[![](../Images/49388388ffd6f32b28864aa564646590.png "Running graphql server")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vtgtKM1M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f5ooc7mf9sjsjk80zng3.png)

查询用户。

[![](../Images/6a58895393676e968b2ccfa0e828b6eb.png "User list")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--w5FWoqiE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a7ku76k7rf9bm3hlekfv.png)

# 将詹金斯管道添加到项目中

api 应用程序管道将需要 Dockerfile、kubernetes 和 Jenkins 管道配置。

### 1。Dockerfile 文件

下面是基于 nodejs 的 Dockerfile 配置。

```
FROM node:8.11.0-alpine

WORKDIR /usr/src

COPY ./*.js package.json yarn.lock ./
RUN yarn install

EXPOSE 4000
CMD [ "node", "index.js"] 
```

### 1。用于数据库的 Kubernetes

用下面的内容创建文件“k8s/db.yml”。

```
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: simpleapi-db-claim
  annotations:
    volume.beta.kubernetes.io/storage-class: "ssdnfs"
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 1Gi
---

apiVersion: v1
kind: ReplicationController
metadata:
  labels:
    name: simpleapi-db
  name: simpleapi-db
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: simpleapi-db
    spec:
      containers:
      - image: mongo
        name: mongo
        ports:
        - name: mongo
          containerPort: 27017
        volumeMounts:
            - name: mongo-persistent-storage
              mountPath: /data/db
      volumes:
        - name: mongo-persistent-storage
          persistentVolumeClaim:
            claimName: simpleapi-db-claim
---
apiVersion: v1
kind: Service
metadata:
  labels:
    name: simpleapi-db
  name: simpleapi-db
spec:
  ports:
    - port: 27017
      targetPort: 27017
  selector:
    app: simpleapi-db 
```

此配置将创建永久卷声明，该声明使用我们在[上一篇](https://dev.to/pongsatt/setup-dynamic-storage-class-provisioner-based-on-nfs-1d4i)文章中创建的存储类“ssdnfs”。我们还定义了 mongodb 实例及其服务。

我们可以使用“simpleapi-db:27017”在集群中访问这个数据库。

### 2。应用程式库

创建包含内容的文件“k8s/api.yml”。

```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: simpleapi
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: simpleapi
    spec:
      containers:
        - name: simpleapi
          image: "<imageTag>"
          env:
          - name: DB_URL
            value: "mongodb://simpleapi-db:27017/test"
          ports:
          - name: http
            containerPort: 4000
          readinessProbe:
            httpGet:
              path: /
              port: 4000
            initialDelaySeconds: 5
            periodSeconds: 10
          livenessProbe:
            httpGet:
              path: /
              port: 4000
            initialDelaySeconds: 3
            periodSeconds: 20
---
apiVersion: v1
kind: Service
metadata:
  name: simpleapi
spec:
  ports:
    - name: http
      port: 80
      targetPort: 4000
  selector:
    app: simpleapi 
```

这个配置由部署和服务组成。我们可以通过服务“simpleapi:80”来访问这个应用程序。

我们还用环境“DB_url”覆盖数据库 URL。

将在构建过程中被替换，因为它是以后生成的。

### 3。忽必烈为进

正如承诺的那样，这个 api 将能够通过入口配置(从[前一篇文章](https://dev.to/pongsatt/set-up-nginx-ingress-with-letsencrypt-certificate-on-vms-or-bare-metal-kubernetes-cluster-j25)开始设置)通过 https 访问。

创建文件“k8s/ingress.yml”。

**备注:**

> 如果您使用 http01 协议应用 letsencrypt 证书，您将需要取消注释/注释批注。
> 将“simpleapi.yourdomain.com”替换为你的真实域名
> 你需要为“simpleapi.yourdomain.com”创建 DNS 记录，以便它可以申请证书(详见[之前的帖子](https://dev.to/pongsatt/deploy-static-front-end-application-to-kubernetes-cluster-using-jenkins-cd-and-minio-with-automatic-https-cert-44gk))

```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: simpleapi-ingress
  annotations:
    kubernetes.io/ingress.class: "nginx"

    # use http01 protocol. uncomment line below to use http01
    # kubernetes.io/tls-acme: "true"

    # use dns01 protocol. comment 2 lines below if use http01
    certmanager.k8s.io/acme-challenge-type: "dns01"
    certmanager.k8s.io/acme-dns01-provider: "aws-route53"

spec:
  tls:
  - hosts:
    - simpleapi.yourdomain.com
    secretName: simpleapi-tls
  rules:
  # The host must be identical to the above one
  - host: simpleapi.yourdomain.com
    http:
      paths:
      - path: /
        backend:
          # The name of your service
          serviceName: simpleapi
          servicePort: 80 
```

### 4\. Jenkinsfile

在根文件夹中创建“Jenkinsfile”。

**备注:**

> 将“192.168.1.105:8082”替换为您的私有注册表
> 您需要创建“用户/密码”凭证名称“myregUserPass”来访问您在 Jenkins 的 Docker 存储库

```
pipeline {
    environment {
        IMAGE_TAG = "${env.BUILD_NUMBER}"
        REGISTRY = "192.168.1.105:8082"
        APP = "simpleapi"
    }
    agent { label 'docker' }

    stages {
        stage('build and push image') {
            steps {
                script {
                    docker.withRegistry("http://${REGISTRY}", 'myregUserPass') {
                        docker.build("${APP}").push("${IMAGE_TAG}")
                    }
                }
            }
        }
        stage('deploy') {
            steps {
                sh("sed -i.bak 's|<imageTag>|${REGISTRY}/${APP}:${IMAGE_TAG}|' ./k8s/api.yml")
                sh("kubectl apply -f k8s/")
            }
        }

    }

} 
```

这条管道有两个阶段。

1.  构建并推送映像:构建并推送新映像到 docker 私有回购
2.  部署:替换并应用所有 yml 文件

此时，您的项目文件夹应该如下所示。

[![](../Images/ab6dea2316763be7c1bb39473100703c.png "Folders")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--c7STW7l4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/32chi85vb33g66fjr71a.png)

### 5。将项目发布到 git 存储库

你可以推给任何一个饭桶。我们将在下一步使用它来配置 Jenkins。

# 为 docker 私有注册表配置 kubernetes

Kubernetes 集群需要知道用户和密码，以便从中提取映像。我们可以通过修补提取映像的服务帐户(在本例中为默认服务帐户)来设置它。

### 创造秘密

在您的主节点上。

**备注:**

> 替换为您的私有注册服务器
> 替换为您的注册数据

```
kubectl create secret docker-registry dockerconfig --docker-server=<your-registry-server> --docker-username=<your-name> --docker-password=<your-pword> --docker-email=<your-email>

kubectl patch serviceaccount default -p '{"imagePullSecrets": [{"name": "dockerconfig"}]}' 
```

# 创建詹金斯管道

现在，我们将在 Jenkins 创建名为“simpleapi”的新管道项目。

[![](../Images/3a9c7c0403cee632d04d40cc611ff057.png "Pipeline")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0EOtGEbK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e6bcn552z36tsvhh9nlo.png)

设置 git 存储库和凭据。

[![](../Images/9730e1cc0e7bddb6dd6f8375964665be.png "Git repo and credential")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9exopFf1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cnh2c1qzayuknhz126yu.png)

运行 build 并等待直到成功。

[![](../Images/6fc8913f6010f910976206a89598bfad.png "build success")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hCfoF-yz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fcabu5re3q0nalq7lm4y.png)

运行`kubectl get secret`。如果你看到“simpleapi-tls”，意味着我们可以通过“[https://simpleapi.yourdomain.com](https://simpleapi.yourdomain.com)访问我们的应用程序。

[![](../Images/823024f37be56cf11e5a0b37e786a1b0.png "api result")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3M20Yy6n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0ki3zdgvokc3ohpg6i1i.png)

# 总结

这是这个[系列](https://dev.to/pongsatt/setup-your-own-kubernetes-cluster-on-vms-5ln)的最后一个帖子。我们学习了如何设置集群，直到创建具有入口和动态存储的前端和后端。希望这些帖子能为你提供一些价值。谢了。