# 通过一个具体的例子了解 Kubernetes

> 原文：<https://dev.to/chuck_ha/understand-kubernetes-through-a-concrete-example-7d9>

```
Date: July 12th, 2018
Kubernetes Version: v1.11.0
📝 is a sidebar
⚠️ is a warning 
```

Enter fullscreen mode Exit fullscreen mode

Kubernetes 是一个容器编排系统。您可以将一个容器视为一个应用程序，将多个容器视为一个 pod。编排部分意味着你告诉 Kubernetes 你想要运行哪些容器，它将负责在你的集群中实际运行容器，将流量路由到正确的 pod，以及[许多其他功能](https://kubernetes.io/docs/concepts/)。

Kubernetes 为您的 pod 定义提供了一个模式。这意味着您要填写一个记录良好的模板，并将其交给 Kubernetes 集群。Kubernetes 然后计算出什么将在哪里运行，运行您的 pods 并配置集群网络。如果一个 pod 崩溃，Kubernetes 会注意到系统不再处于正确的状态。Kubernetes 将采取行动使系统返回到规定的状态。

让我们看一个例子:在 Kubernetes 集群上的一个名为 devtoo.com 的博客平台。

## 组件

第一步是弄清楚 devtoo.com 的组成部分。假设这些都是必需的组件:

1.  接受来自互联网的 HTTP 流量的 web 服务器。网络服务器的例子包括 [nginx](https://www.nginx.com/) 和 [apache](https://httpd.apache.org/)
2.  将 rails 应用程序加载到内存并为请求提供服务的应用服务器。这将是支持 devtoo.com 的 rails 应用程序。
3.  一个储存我们所有精彩帖子的数据库。 [Postgres](https://www.postgresql.org/) 、 [mysql](https://www.mysql.com/) 和 [MongoDB](https://www.mongodb.com/) 都是数据库的例子。
4.  绕过应用程序和数据库并立即返回结果的缓存。缓存的例子包括 [redis](https://redis.io/) 和 [memcached](https://memcached.org/) 。

## 最终目标

下一步是弄清楚最终的系统应该是什么样子。Kubernetes 在这里给你很多选择。这些组件可以各自在自己的 pod 中运行，也可以全部放入一个 pod 中。我喜欢从最简单的地方开始，如果不尽如人意，就去解决它。对我来说，这意味着每个组件都将在自己的 pod 中运行。一个典型的 web 请求将进入系统并到达 web 服务器。web 服务器将询问缓存是否有该端点的结果。如果是，结果会立即返回。如果没有，请求将被传递给应用服务器。应用服务器被配置为与数据库对话，并生成动态内容，这些内容被发送回 web 浏览器。

## 定义系统

Kubernetes 将服务映射到 pod。每个 pod 将有一个服务。这将允许您引用具有 DNS 的其他 pod。

```
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app: web
spec:
  containers:
  - name: nginx
    image: registry.hub.docker.com/library/nginx:1.15
    ports:
    - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: web
spec:
  selector:
    app: web
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80 
```

Enter fullscreen mode Exit fullscreen mode

服务定义了一个选择器`app: web`。该服务会将流量路由到与该选择器匹配的任何 pod。如果查看 pod 定义，您会看到 pod 上定义了一个`app: web`标签。这意味着流量在端口 80 进入服务，并被发送到`targetPort`上的 nginx pod，在本例中也是 80。`targetPort`和`containerPort`必须匹配。

在这里，您使用您的魔棒生成一个 nginx 配置，它嵌入在 nginx 映像中，将流量发送到缓存，如果没有结果，则发送到应用服务器。

下面是缓存定义:

```
apiVersion: v1
kind: Pod
metadata:
  name: redis
  labels:
    app: cache
spec:
  containers:
  - name: redis
    image: registry.hub.docker.com/library/redis:4.0
    ports:
    - containerPort: 6379
---
apiVersion: v1
kind: Service
metadata:
  name: cache
spec:
  selector:
    app: cache
  ports:
  - protocol: TCP
    port: 6379
    targetPort: 6379 
```

Enter fullscreen mode Exit fullscreen mode

和数据库定义:

```
apiVersion: v1
kind: Pod
metadata:
  name: postgres
  labels:
    app: db
spec:
  containers:
  - name: db
    image: registry.hub.docker.com/library/postgres:10.4
    ports:
    - containerPort: 6379
---
apiVersion: v1
kind: Service
metadata:
  name: database
spec:
  selector:
    app: cache
  ports:
  - protocol: TCP
    port: 6379
    targetPort: 6379 
```

Enter fullscreen mode Exit fullscreen mode

这些都是部署 devtoo.com 时考虑的依赖项。接下来，必须配置应用程序本身。 [Rails 可以使用环境变量来连接数据库](http://guides.rubyonrails.org/configuring.html#configuring-a-database)。您可以在 pod YAML 中这样定义它:

⚠️This 超级没有安全感！Kubernetes 有更好的方法来做到这一点，但我省略了它们，以保持这篇文章的范围“小”。

```
apiVersion: v1
kind: Pod
metadata:
  name: app
  labels:
    app: app
spec:
  containers:
  - name: devtoo-com
    env:
    - name: DATABASE_URL
      value: postgresql://user1:password1@database/dev_to_db
    image: registry.hub.docker.com/devtoo.com/app:v9001
    ports:
    - containerPort: 3001
---
apiVersion: v1
kind: Service
metadata:
  name: devtoo-com
spec:
  selector:
    app: app
  ports:
  - protocol: TCP
    port: 3001
    targetPort: 3001 
```

Enter fullscreen mode Exit fullscreen mode

最后需要的是一个入口点，一个流量可以从外界进入集群的地方。

📝我忽略了 IngressControllers，因为虽然它们是必需的，但在这个理解层次上，它们是一个被忽略的实现细节。

```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: dev-to
spec:
  backend:
    serviceName: web
    servicePort: 80 
```

Enter fullscreen mode Exit fullscreen mode

这意味着在这个入口点收到的任何流量都将被发送到端口 80 上名为`web`的服务。

现在您的集群已经设置好了，让我们跟踪一个数据包来获得这个 blogpost。你在浏览器中输入[http://devtoo.com/chuck_ha/this-post](http://devtoo.com/chuck_ha/this-post)。http://devtoo.com 在 DNS 中解析为某个 IP 地址，这是您的 kubernetes 集群前面的一个负载平衡器。负载平衡器将流量发送到您的入口点。由于入口上只有一个服务，因此流量被发送到映射到 nginx pod 的 web 服务。nginx 容器检查数据包，并将其发送到映射到 redis pod 的缓存服务。redis pod 以前从未见过这个 URL，所以从 nginx 继续执行。请求被发送到应用服务器，在那里生成、缓存并返回到您的 web 浏览器。

然后你点击🦄按钮！

📝我跳过的一系列事情，这样你就可以专注于肉，而不会迷失在细节中:

*   [构建您的应用程序容器](https://docs.docker.com/engine/reference/builder/)
*   [安全](https://kubernetes.io/docs/concepts/configuration/secret/)，包括 [TLS](https://kubernetes.io/docs/tasks/tls/managing-tls-in-a-cluster/)
*   [真实世界的入口](https://kubernetes.io/docs/concepts/services-networking/ingress/)
*   [工作量管理](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)