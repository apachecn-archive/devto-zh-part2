# 露出一片羽毛。Kubernetes 中使用 Ingress 的 JS HTTP API

> 原文：<https://dev.to/andrejus/exposing-a-feathersjs-http-api-in-kubernetes-using-ingress-ai1>

## 前奏

这篇文章是我在 Think Engineer 工作期间发表的，我在那里为我们的原型物联网(IoT)项目开发了云后端。在调试我们的内部物联网平台时，我们解决了一个具有挑战性的网络问题，之后产生了这篇文章的想法。原帖[此处](https://think-engineer.com/blog/cloud-computing/exposing-a-feathers-js-http-api-in-kubernetes-using-ingress)。

## 背景

构建物联网项目通常依赖于成熟的客户端-服务器架构。当这个术语第一次被使用时，它将*用户*称为客户端，请求从*中央计算机*，即服务器运行作业。[ [1](https://tools.ietf.org/html/rfc5) 如今，借助物联网，客户端是请求服务器存储/处理数据的*边缘设备*。虽然从理论上讲，一台服务器可以处理一小批物联网设备，但突然激增的设备请求很容易使服务器不堪重负，并影响可用性和数据完整性。

这就是分布式计算结合微服务架构的用武之地。使用这种设计方法，设备可以发送的每种类型的请求都可以被建模为服务，并部署在一个服务器集群上，也称为云。微服务通常被公开为*RESTful HTTP API*，这意味着它们允许客户端通过 GET、POST、PUT 和 DELETE 等请求来访问和操作数据。

### 羽毛。射流研究…

Feathers 是一个开源的 Node.js web 框架，充当现代应用程序的 REST 和实时 API 层。[ [2](https://github.com/feathersjs/feathers) 后一点意味着它非常适合构建 RESTful APIs，尤其是使用微服务架构。RESTful API 本质上是无状态的，这意味着可能会有多个 API 副本，客户端不应该关心他们在与哪个副本对话。这对于可扩展性来说是完美的，因为设备端点的副本数量可以根据整个系统的繁忙程度进行调整。

### Kubernetes

Kubernetes 是云计算领域的最新进展，允许无缝部署、管理和扩展容器化的应用程序。它的工作原理是让您定义想要运行的工作负载和想要公开的服务。这为微服务和客户端如何相互对话提供了细粒度的控制。在此基础上，可以根据应用程序的运行情况更新、扩大或缩小工作负载。Kubernetes 一举消除了对设置、管理和扩展服务器/应用程序的担忧，这对快速原型开发非常有帮助。

#### 入口

最后，如果设备不知道与谁对话，那么 API 还有什么意义呢？Kubernetes 中使用 Ingress 来允许入站连接到达集群服务。[ [3](https://kubernetes.io/docs/concepts/services-networking/ingress/) ]在这种情况下，它使用 Google Kubernetes 引擎(GKE)进行配置，为服务提供外部可访问的 URL，并对流量进行负载平衡。

## 问题

默认情况下，GKE 上的 Ingress 为您使用控制器公开的任何服务提供了内置的健康检查。这对于基本工作负载来说很好，比如 nginx 容器，在这种情况下，提供内部服务器错误消息可能会导致敏感数据泄露。

然而，对于 RESTful API 来说，Ingress 默认的方式有点太简单了:

> 所有 Kubernetes 服务必须在“/”上提供 200 页

默认情况下，Ingress 会向`'/'`发送一个`HTTP GET`请求。这对于 nginx 这样的容器来说是没问题的，因为它只会触及公共目录的根目录。

从 RESTful API 的角度来看，Ingress 正在向`'/'`服务发送一个`find()`请求(斜杠前没有指定任何内容)。虽然拥有一个支持`'/'`上请求的包罗万象的`index`服务是有意义的。JS 明确不允许这样做。

半官方的原因是服务名中的斜杠被截断了，所以它会产生一个空字符串，这是一个不好的服务标识符，尤其是当你用它做一些有趣的事情的时候。[ [4](https://github.com/feathersjs/feathers/issues/728#issuecomment-355350349)

## 修罗

### 在 Kubernetes 中定义自定义健康检查

只有在工作负载定义中没有指定自定义运行状况检查时，GKE 入口才会回退到默认运行状况检查。Ingress 的 GKE 实现从部署的容器规范中的`readinessProbe`定义中提取定制的健康检查。这可以定义如下:

```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: restfulapi-deployment
  labels:
    app: restfulapi
spec:
  replicas: 3
  selector:
    matchLabels:
      app: restfulapi
  template:
    metadata:
      labels:
        app: restfulapi
    spec:
      containers:
      - name: bitbucket-thinkengineer-restfulapi
        image: gcr.io/restful-example/bitbucket-thinkengineer-restfulapi:latest
        ports:
        - containerPort: 3030
        envFrom:
         - configMapRef:
             name: config
        # Custom healthcheck for Ingress
        readinessProbe:
          httpGet:
            path: /health
            port: 3030
          initialDelaySeconds: 5
          timeoutSeconds: 5 
```

据报道，一些论坛帖子称定义一个`containerPort`也有帮助。从这里开始，您的工作负载服务可以使用 Ingress 正常公开:

```
apiVersion: v1
kind: Service
metadata:
  name: restfulapi-service
spec:
  selector:
    app: restfulapi
  type: NodePort
  ports:
  - name: restfulapi
    protocol: TCP
    port: 3030
    targetPort: 3030 
```

```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: ingress
  annotations:
    kubernetes.io/ingress.global-static-ip-name: ingress
    ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: rest.think-engineer.com
    http:
      paths:
      - path: /*
        backend:
          serviceName: restfulapi-service
          servicePort: 3030 
```

### 在羽毛上实现自定义健康检查。射流研究…

在上面的定义中，你可以看到我们现在检查的是`'/health'`而不是`'/'`。由于服务名不再是空字符串，我们可以创建一个 Feathers 服务来响应发送到那里的请求。这可以按如下方式完成:

```
/* Create Feathers appication */
const restAPI = express(feathers())
restAPI.use(express.json())
.use(express.urlencoded({ extended: true }))
.configure(express.rest())
.configure(socketio())
.use(express.errorHandler())
.use('/rest', restService)

/* Register health API */
.use('/health', {
    async find(params) { 
        return `restAPI still alive`
    }
})

/* Start the server */
.listen(API_PORT).on('listening', () =>
    console.log(`restAPI listening on ${API_URL}`)
) 
```

新创建的`'/health'`服务非常简单——它只是返回一个字符串，以及一个 200 状态代码。这足以满足入口并通过健康检查，从而将流量路由到您的服务/工作负载。

## 结论

总的来说，在构建原型时，你倾向于使用新技术，而这些新技术并不一定是为协同工作而设计的。我知道在这种情况下，我唯一要做的事情就是 GitHub 的问题。虽然这看起来像是一个简单的解决方案，但是解决这个问题是一个巨大的挑战。从那以后，我和一些 Kubernetes 的开发者谈论过 Ingress，他们似乎都同意它在 GKE 上的实现会导致很多这样的问题。

## 参考文献

[ [1](https://tools.ietf.org/html/rfc5) ] - J. Rulifson，“解码编码语言(DEL)”，网络工作组 RFC-5，1969 年 6 月 2 日。

[ [2](https://github.com/feathersjs/feathers) ] -羽毛投稿人，羽毛。JS，羽毛。

[[3](https://kubernetes.io/docs/concepts/services-networking/ingress/)——忽必烈的作者，《入门》，忽必烈。

[ [4](https://github.com/feathersjs/feathers/issues/728#issuecomment-355350349) ] - keesee，daffl，“服务于根。/’“，羽毛。