# gRPC 和开源大使 API 网关

> 原文：<https://dev.to/datawireio/grpc-and-the-open-source-ambassador-api-gateway--55k5>

[gRPC](https://grpc.io/) 是建立在 HTTP/2 之上的高性能 RPC 协议。我们看到越来越多的公司[将他们的 API](https://www.sajari.com/blog/grpc-and-displacement-of-rest-apis)从 HTTP/JSON 转移到 gRPC。与 HTTP/JSON 相比，gRPC 有很多优点:

*   性能。gRPC 使用 HTTP/2，支持流式传输、多路复用等等([参见动作差异](https://imagekit.io/demo/http2-vs-http1))。此外，gRPC 拥有对 protobuf 的原生支持，这在序列化/反序列化方面比 JSON 快得多。
*   流式传输。尽管名为 gRPC，但它也支持流式传输，这为更广泛的用例提供了支持。
*   端点的代码生成。gRPC 使用来自 API 定义(`.proto`文件)的代码生成来清除您的端点。

gRPC 确实有一些缺点，其中最大的缺点可能是它比 HTTP/REST 更新，因此，围绕 gRPC 的工具和库的成熟度远不及 HTTP。

其中一个缺口是在 API 网关中。许多 API 网关不支持 gRPC。幸运的是，[大使](https://www.getambassador.io/)[支持 gRPC](https://www.getambassador.io/how-to/grpc) ，这要感谢它使用[特使](https://www.envoyproxy.io/)作为其核心代理引擎。

### API 网关和 gRPC

API 网关实现交叉功能，如认证、日志记录、速率限制和负载平衡。通过将 API 网关与您的 gRPC APIs 一起使用，您能够在核心 gRPC 服务之外部署此功能。此外，Ambassador 能够为您的 HTTP 和 gRPC 服务提供这种功能。

### 大使和 gRPC

使用 Ambassador 部署 gRPC 服务非常简单。在[安装大使](https://www.getambassador.io/user-guide/getting-started)之后，为您的 gRPC 服务创建一个大使映射。映射示例如下:

```
--------
apiVersion: ambassador/v0
kind: Mapping
name: grpc_mapping
grpc: true
prefix: /helloworld.Greeter/
rewrite: /helloworld.Greeter/
service: grpc-greet 
```

Enter fullscreen mode Exit fullscreen mode

请注意，gRPC 服务不像 HTTP 服务那样被路由。相反，gRPC 请求包括包和服务名。该信息用于将请求路由到适当的服务。我们根据[相应地设置`prefix`和`rewrite`字段。原型](https://github.com/grpc/grpc-go/blob/master/examples/helloworld/helloworld/helloworld.proto)文件。另外，请注意，我们设置了`grpc: true`来告诉 Ambassador 该服务使用 gRPC。

### 部署 gRPC 服务

我们可以部署一个简单的 Hello，World gRPC 服务来演示所有这些功能。将下面完整的 YAML 复制粘贴到一个名为`helloworld.yaml` :
的文件中

```
--------
apiVersion: v1
kind: Service
metadata:
  labels:
    service: grpc-greet
  name: grpc-greet
  annotations:
    getambassador.io/config: |
      ---
      apiVersion: ambassador/v0
      kind: Mapping
      name: grpc_mapping
      grpc: true
      prefix: /helloworld.Greeter/
      rewrite: /helloworld.Greeter/
      service: grpc-greet
spec:
  type: ClusterIP
  ports:
  - port: 80
    name: grpc-greet
    targetPort: grpc-api
  selector:
    service: grpc-greet
--------
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: grpc-greet
spec:
  replicas: 1
  template:
    metadata:
      labels:
        service: grpc-greet
    spec:
      containers:
      - name: grpc-greet
        image: enm10k/grpc-hello-world
        ports:
        - name: grpc-api
          containerPort: 9999
        env:
          - name: PORT
            value: "9999"
        command:
          - greeter_server
      restartPolicy: Always 
```

Enter fullscreen mode Exit fullscreen mode

然后，运行`kubectl apply -f helloworld.yaml`。

### Hello World 客户端

为了测试 Ambassador 和服务是否正常工作，我们需要运行一个 gRPC 客户机。首先，获取大使的外部 IP 地址。你可以用`kubectl get svc ambassador`来做到这一点。

我们将使用一个 Docker 映像，它已经包含了适当的 Hello World gRPC 客户端。键入，其中$AMBASSADOR_IP 设置为上面的外部 IP 地址，$AMBASSADOR_PORT 设置为 80(对于基于 HTTP 的大使)或 443(对于配置了 TLS 的大使)。

```
docker run -e ADDRESS=${AMBASSADOR_IP}:${AMBASSADOR_PORT} enm10k/grpc-hello-world greeter_client 
```

Enter fullscreen mode Exit fullscreen mode

您应该会看到如下内容:

```
$ docker run -e ADDRESS=35.29.51.15:80 enm10k/grpc-hello-world greeter_client
2018/02/02 20:34:35 Greeting: Hello world 
```

Enter fullscreen mode Exit fullscreen mode

### 总结

[大使](https://www.getambassador.io/)使向您的消费者发布 gRPC 服务变得容易，这要感谢特使[强大的 gRPC 支持](https://www.envoyproxy.io/docs/envoy/v1.5.0/intro/arch_overview/grpc.html)。通过 Ambassador 发布服务，您可以为 gRPC 服务添加身份验证、速率限制和其他功能。

如果您有兴趣在 gRPC 中使用 Ambassador，请加入我们的 [Gitter chat](https://gitter.im/datawire/ambassador) 或访问[https://www . getambassador . io .](https://www.getambassador.io./)