# 使用 Kubernetes 扩展 Python 微服务

> 原文：<https://dev.to/apcelent/scaling-python-microservices-with-kubernetes-b9m>

在我们之前的一篇文章中，我们曾深入讨论过如何建立[微服务。在这篇文章中，我们将讨论构建一个简单的微服务，用 docker 将其容器化，并使用](https://blog.apcelent.com/setup-microservices-architecture-in-python-with-zeromq-docker.html) [Kubernetes](https://kubernetes.io/) 扩展这些容器。

假设读者对[烧瓶](http://flask.pocoo.org/)、[雷迪斯](https://redis.io/)和[码头工人](https://www.docker.com/)有第一手经验。

源代码库可以在这里找到[。](https://github.com/codecraf8/kubernetes-flask-redis-microservice)

## 使用 Flask 和 Redis 构建微服务

在这篇文章中，让我们开始用 Flask 编写微服务。微服务有一个单一的入口点，允许用户使用 PUT 请求创建一个资源，对应于他们选择的 URL，GET 请求服务于该请求。过时的项目会在一年后删除。

将帮助我们构建这个微服务的代码要点如下:

```
db = Redis(host='redis', port=6379)

@app.route('/')
def hello():
    db.incr('count')
    return 'Count is %s.' % db.get('count')

@app.route('/<path:path>', methods = ['PUT', 'GET'])
def home(path):

    if (request.method == 'PUT'):
       event = request.json
       event['last_updated'] = int(time.time())
       event['ttl'] = ttl
       db.delete(path) #remove old keys
       db.hmset(path, event)
       db.expire(path, ttl)
       return flask.jsonify(event), 201

    if not db.exists(path):
        return "Error: thing doesn't exist"

    event = db.hgetall(path)
    event["ttl"] = db.ttl(path)
    return flask.jsonify(event), 200 
```

Enter fullscreen mode Exit fullscreen mode

如果将一个 PUT 请求发送到任何 URL，并带有一个额外的键-值对，那么这个请求将与当前时间戳一起保存在 redis 中。如果我们点击了基本 url，我们会得到该 URL 被点击的次数。

## 将微服务容器化

### 集装箱化缰绳

我们将使用 [docker compose](https://docs.docker.com/compose/) 来容器化我们的微服务。我们的微服务由一个与 redis 数据库交互的简单 flask 应用程序组成。

compose 文件将创建两个不同的容器，第一个用于 flask 应用程序，第二个用于 redis。

```
web:
  build: .
  command: python app.py
  ports:
   - "5000:5000"
  volumes:
   - .:/code
  links:
   - redis
redis:
  image: redis 
```

Enter fullscreen mode Exit fullscreen mode

flask 应用程序的 *Dockerfile* 相当简单，它创建一个工作目录并安装所有的需求。

```
FROM python:2.7
ADD . /code
WORKDIR /code
RUN pip install -r requirements.txt 
```

Enter fullscreen mode Exit fullscreen mode

我们都准备好运行容器了。
 `docker-compose up -d.` 
一旦容器开始运行，我们就可以测试代码是否工作。我们使用
 `curl -H "Content-Type: application/json" -X PUT -d '{"hello":999}' http://localhost:5000/testurl` 
发出一个 PUT 请求。我们收到如下输出:

{ "last_updated": 1485246805，" ttl": 31104000，" hello": 999 }

向同一个 URL
 `curl http://localhost:5000/testurl` 发出 GET 请求时

我们收到如下成功回复:

{ "last_updated": "1485246805 "，" ttl": 31103997，" hello": "999" }

一个相当直接的微服务！

## 为什么是立方？

现在我们已经构建了我们的服务，容器正在为我们运行，让我们深入到生产场景中。我们通常将容器视为更快/更简单的虚拟机，在其他工作环境中，每个容器都包含特定于执行程序的功能，具有唯一的生命周期并依赖于其他容器。

这种方法在早期阶段是有用的，并且改进了开发环境，但是，当我们迁移到生产环境时，它会产生很大的困难。

我们开始意识到这样的挑战:

*   多个容器如何相互对话？

*   如何横向扩展我的容器服务？

*   多个容器服务如何发现彼此？

*   我们如何保护我们的集装箱网络？

*   我们如何部署新的容器并回滚以前的容器？

Kubernetes 试图解决上述这类问题。让我们深入研究一下。

## Kubernetes 101

Kubernetes 是 Google 开发的一个系统，用于管理集群环境中的容器化应用程序。大多数现代应用程序都遵循微服务模式，Kubernetes 帮助管理这些不同的服务，就像在同一台主机上以简单的方式配置相关组件一样。

Kubernetes 主要帮助

*   组件的复制
*   自动缩放
*   负载平衡
*   滚动更新
*   跨组件记录日志
*   监控和运行状况检查
*   服务发现
*   证明

## 核心立方概念

Kubernetes 集群中的控制服务称为主组件或控制面板组件。它们作为主管理点运行，并为工作节点提供集群式系统。节点服务器有一些要求，这些要求是与主组件通信、为容器配置网络以及运行分配给它们的工作负载所必需的。

Kubernetes API 提供了大量的原语，对于我们的目的来说，比较重要的有:

*   pod:集群的基本单元，它们是运行 docker 容器的机器，应该作为单个“应用程序”来控制。

*   服务:它是一组执行相同功能并构成单个实体的 pod 的逻辑分组。不管底层 pod 的数量/状态/健康状况如何，服务的 IP 地址都保持稳定。服务支持 pod 之间的通信。

*   标签:它们是键值对，作为任意标签来关联一个或多个 kubernetes 资源。可以有多个标签，并且键是唯一的。然后可以选择这些标签用于管理目的和行动目标。

*   部署:它们确保指定数量的(特定种类的)pod 在任何给定时间都在运行。它们是用于定义水平扩展的单元的框架，并保持单元的数量不变。如果一个容器当前关闭，另一个将被启动。

详细描述见[正式文件](https://kubernetes.io/docs/concepts/overview/components/)。

## 本地运行 Kubernetes

Kubernetes 是管理容器化应用程序的最佳工具之一，并且已经可以投入生产，但是开发人员很难在本地进行测试。Minikube 是作为本地开发平台的拼图中缺失的一块。

Minikube 不是为可伸缩性或弹性而设计的。它有助于在小型单节点上开始使用 Kubernetes CLI 和 API 工具。没有比用 Kubernetes 测试前几个命令更简单的方法了。

Minikube 在本地启动一个虚拟机，并运行必要的 Kubernetes 组件。然后，虚拟机通过一个名为 localkube 的二进制文件配置 Docker 和 Kubernetes，从而形成一个本地端点，可用于 Kubernetes 客户端 *kubectl* 。

[本地安装 Minikube】](https://github.com/kubernetes/minikube#installation)

[快速启动迷你库](https://github.com/kubernetes/minikube#quickstart)

## 使用 Minikube

让我们从本地的 Kubernetes 集群开始:

minikube 开始启动本地 Kubernetes 集群...Kubernetes 在
 `https://192.168.99.101:443` 上市

### 部署缰绳

我们首先必须创建一个 redis 部署文件。

```
 apiVersion: extensions/v1beta1
  kind: Deployment
  metadata:
    creationTimestamp: null
    name: redis
  spec:
    replicas: 1
    strategy: {}
    template:
      metadata:
        creationTimestamp: null
        labels:
          app: redis
      spec:
        containers:
        - image: redis
          name: redis
          resources: {}
        restartPolicy: Always
  status: {} 
```

Enter fullscreen mode Exit fullscreen mode

我们进一步使用 kubectl 来创建部署

 `$ kubectl create -f redis-deployment.yaml` 

### 通过创建服务来公开 Redis 部署

下面是服务文件:

```
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app: redis
  name: redis
spec:
  clusterIP: None
  ports:
  - name: headless
    port: 55555
    targetPort: 0
  selector:
    service.name: redis
status:
  loadBalancer: {} 
```

Enter fullscreen mode Exit fullscreen mode

我们公开服务如下:
 `$ kubectl create -f redis-service.yaml` 

## 展开烧瓶

类似地，我们需要为 flask 创建另一个部署，这里是部署文件

```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  creationTimestamp: null
  name: flask
spec:
  replicas: 1
  strategy:
    type: Recreate
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: flask
    spec:
      containers:
      - args:
        - python
        - app.py
        name: flask
        ports:
        - containerPort: 5000
        resources: {}
        volumeMounts:
        - mountPath: /code
          name: .
      restartPolicy: Always
      volumes:
      - name: .
        persistentVolumeClaim:
          claimName: .
status: {} 
```

Enter fullscreen mode Exit fullscreen mode

部署可以创建如下:
 `$ kubectl create -f flask-deployment.yaml` 

## 暴露烧瓶服务

我们首先创建服务文件:

```
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    io.kompose.service: flask
  name: flask
spec:
  ports:
  - name: "5000"
    port: 5000
    targetPort: 5000
  selector:
    service.name: flask
status:
  loadBalancer: {} 
```

Enter fullscreen mode Exit fullscreen mode

然后我们创建服务
 `$ kubectl create -f flask-service.yaml` 

您已经在集群中所有节点的外部端口上公开了您的服务。如果您要将此服务公开给外部 internet，您可能需要为服务端口(tcp:30321)设置防火墙规则以服务流量。

```
$ kubectl get service <service_name> --output='jsonpath={.spec.ports[0].NodePort}'30321%

$ port=$(kubectl get service <sevice_name> --output='jsonpath={.spec.ports[0].NodePort}') 
```

Enter fullscreen mode Exit fullscreen mode

为了便于以后参考，我们构造了这个地址:

```
$ address="$(minikube ip):$port"
$ echo $address
192.168.99.101:31637
$ open "http://${address}/?start=23&stop=31" 
```

Enter fullscreen mode Exit fullscreen mode

我希望这篇文章有所帮助。一定要在评论中提到，关于你想听到更多的信息。

这篇文章最初出现在 [Apcelent 科技博客](https://blog.apcelent.com/scaling-python-microservices-kubernetes.html)上。