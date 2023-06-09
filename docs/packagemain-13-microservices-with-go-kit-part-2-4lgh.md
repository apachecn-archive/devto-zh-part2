# 带有 go-kit 的微服务。第二部分

> 原文：<https://dev.to/plutov/packagemain-13-microservices-with-go-kit-part-2-4lgh>

这是“packagemain #13:带 go-kit 的微服务”的文本版本。第二部"视频。

*   [第一部分](https://dev.to/plutov/microservices-with-go-kit-part-1-13dd)

[![Microservices with go-kit. Part 2](img/2683a541db7d52a9efbd2cf8591dbb81.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qXz9o9Yr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y7y7xvd68ysi6wk3ew81.jpg)

在上一个视频中，我们使用工具包命令行工具为我们的服务准备了一个本地环境。在这个视频中，我们将继续使用这些代码。

让我们首先通过编写原型定义来实现我们的 Notificator 服务，因为它应该是一个 gRPC 服务。我们已经有了预生成的文件`notificator/pkg/grpc/pb/notificator.pb`，让我们把它变得非常简单。

```
syntax = "proto3";

package pb;

service Notificator {
    rpc SendEmail (SendEmailRequest) returns (SendEmailReply);
}

message SendEmailRequest {
    string email = 1;
    string content = 2;
}

message SendEmailReply {
    string id = 1;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要生成服务器和客户端存根，我们可以使用 kit tool 已经给我们的`compile.sh`脚本，它基本上包含了`protoc`命令。

```
cd notificator/pkg/grpc/pb
./compile.sh 
```

Enter fullscreen mode Exit fullscreen mode

如果我们检查`notificator.pb.go`——它被更新了。

现在我们需要实现服务本身。让我们只生成一个 uuid 并返回它，假装它已经发送出去，而不是发送一封真正的电子邮件。但是首先我们必须编辑一点服务来匹配我们的请求/响应格式(新的`id` return 参数)。

notificator/pkg/service/service . go:

```
import (
    "context"

    uuid "github.com/satori/go.uuid"
)

// NotificatorService describes the service.
type NotificatorService interface {
    // Add your methods here
    SendEmail(ctx context.Context, email string, content string) (string, error)
}

type basicNotificatorService struct{}

func (b *basicNotificatorService) SendEmail(ctx context.Context, email string, content string) (string, error) {
    id, err := uuid.NewV4()
    if err != nil {
        return "", err
    }

    return id.String(), nil
} 
```

Enter fullscreen mode Exit fullscreen mode

notificator/pkg/service/middleware . go:

```
func (l loggingMiddleware) SendEmail(ctx context.Context, email string, content string) (string, error) {
    defer func() {
        l.logger.Log("method", "SendEmail", "email", email, "content", content)
    }()
    return l.next.SendEmail(ctx, email, content)
} 
```

Enter fullscreen mode Exit fullscreen mode

notificator/pkg/endpoint/endpoint . go

```
// SendEmailResponse collects the response parameters for the SendEmail method.
type SendEmailResponse struct {
    Id string
    E0 error `json:"e0"`
}

// MakeSendEmailEndpoint returns an endpoint that invokes SendEmail on the service.
func MakeSendEmailEndpoint(s service.NotificatorService) endpoint.Endpoint {
    return func(ctx context.Context, request interface{}) (interface{}, error) {
        req := request.(SendEmailRequest)
        id, e0 := s.SendEmail(ctx, req.Email, req.Content)
        return SendEmailResponse{Id: id, E0: e0}, nil
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们搜索 TODO `grep -R "TODO" notificator`，我们可以看到我们仍然需要为 gRPC 请求和响应实现编码器和解码器。

notificator/pkg/grpc/handler . go:

```
func decodeSendEmailRequest(_ context.Context, r interface{}) (interface{}, error) {
    req := r.(*pb.SendEmailRequest)
    return endpoint.SendEmailRequest{Email: req.Email, Content: req.Content}, nil
}

func encodeSendEmailResponse(_ context.Context, r interface{}) (interface{}, error) {
    reply := r.(endpoint.SendEmailResponse)
    return &pb.SendEmailReply{Id: reply.Id}, nil
} 
```

Enter fullscreen mode Exit fullscreen mode

### 服务发现

SendEmail 将被用户服务调用，因此用户服务需要知道 Notificator 的地址，这是典型的服务发现问题。当然，在我们的本地环境中，当我们使用 Docker Compose 时，我们知道如何连接到服务，但是在真正的分布式环境中可能更困难。

让我们从在 etcd 中注册 Notificator 服务开始。基本上，etcd 是一个分布式的可靠的键值存储，广泛用于服务发现。go-kit 支持服务发现的其他技术:eureka、consul、zookeeper 等。

让我们将它添加到 Docker Compose 中，这样它就可以用于我们的服务器了。从互联网复制:

坞站-化合物. yml:

```
 etcd:
        image: 'quay.io/coreos/etcd:v3.1.7'
        restart: always
        ports:
            - '23791:2379'
            - '23801:2380'
        environment:
            ETCD_NAME: infra
            ETCD_INITIAL_ADVERTISE_PEER_URLS: 'http://etcd:2380'
            ETCD_INITIAL_CLUSTER: infra=http://etcd:2380
            ETCD_INITIAL_CLUSTER_STATE: new
            ETCD_INITIAL_CLUSTER_TOKEN: secrettoken
            ETCD_LISTEN_CLIENT_URLS: 'http://etcd:2379,http://localhost:2379'
            ETCD_LISTEN_PEER_URLS: 'http://etcd:2380'
            ETCD_ADVERTISE_CLIENT_URLS: 'http://etcd:2379' 
```

Enter fullscreen mode Exit fullscreen mode

让我们在 etcd 中注册 Notificator，Notificator/cmd/service/service . go:

```
registrar, err := registerService(logger)
if err != nil {
    logger.Log(err)
    return
}

defer registrar.Deregister()

func registerService(logger log.Logger) (*sdetcd.Registrar, error) {
    var (
        etcdServer = "http://etcd:2379"
        prefix     = "/services/notificator/"
        instance   = "notificator:8082"
        key        = prefix + instance
    )

    client, err := sdetcd.NewClient(context.Background(), []string{etcdServer}, sdetcd.ClientOptions{})
    if err != nil {
        return nil, err
    }

    registrar := sdetcd.NewRegistrar(client, sdetcd.Service{
        Key:   key,
        Value: instance,
    }, logger)

    registrar.Register()

    return registrar, nil
} 
```

Enter fullscreen mode Exit fullscreen mode

当我们的程序停止或崩溃时，我们应该记住取消注册服务。现在 etcd 知道了我们的服务，在这个例子中我们只有一个实例，但是在现实生活中当然可以更多。

现在让我们测试我们的 Notificator 服务，检查它是否能够在 etcd:
中注册

```
docker-compose up -d etcd
docker-compose up -d notificator 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们回到我们的用户服务并调用 Notificator 服务，基本上我们将在创建后向用户发送一个虚构的通知。

因为 Notificator 是一个 gRPC 服务，所以我们需要与我们的客户机共享一个客户机存根文件，在我们的例子中是用户服务。

protobuf 客户端存根代码位于`notificator/pkg/grpc/pb/notificator.pb.go`中，我们可以将这个包导入到我们的客户端。

users/pkg/service/service . go:

```
import (
    "github.com/plutov/packagemain/13-go-kit-2/notificator/pkg/grpc/pb"
    "google.golang.org/grpc"
)

type basicUsersService struct {
    notificatorServiceClient pb.NotificatorClient
}

func (b *basicUsersService) Create(ctx context.Context, email string) error {
    reply, err := b.notificatorServiceClient.SendEmail(context.Background(), &pb.SendEmailRequest{
        Email:   email,
        Content: "Hi! Thank you for registration...",
    })

    if reply != nil {
        log.Printf("Email ID: %s", reply.Id)
    }

    return err
}

// NewBasicUsersService returns a naive, stateless implementation of UsersService.
func NewBasicUsersService() UsersService {
    conn, err := grpc.Dial("notificator:8082", grpc.WithInsecure())
    if err != nil {
        log.Printf("unable to connect to notificator: %s", err.Error())
        return new(basicUsersService)
    }

    log.Printf("connected to notificator")

    return &basicUsersService{
        notificatorServiceClient: pb.NewNotificatorClient(conn),
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

但是当我们在 etcd 中注册 Notificator 时，我们可以通过从 etcd 中获取它来替换硬编码的 Notificator 地址。

```
var etcdServer = "http://etcd:2379"

client, err := sdetcd.NewClient(context.Background(), []string{etcdServer}, sdetcd.ClientOptions{})
if err != nil {
    log.Printf("unable to connect to etcd: %s", err.Error())
    return new(basicUsersService)
}

entries, err := client.GetEntries("/services/notificator/")
if err != nil || len(entries) == 0 {
    log.Printf("unable to get prefix entries: %s", err.Error())
    return new(basicUsersService)
}

conn, err := grpc.Dial(entries[0], grpc.WithInsecure()) 
```

Enter fullscreen mode Exit fullscreen mode

我们得到第一个条目，因为我们只有一个，但在实际系统中，它可能是数百个条目，所以我们可以应用一些逻辑进行实例选择，例如循环。

现在，让我们启动我们的用户服务并进行测试:

```
docker-compose up users 
```

Enter fullscreen mode Exit fullscreen mode

我们将调用 http 端点来创建一个用户:

```
curl -XPOST http://localhost:8802/create -d '{"email": "test"}' 
```

Enter fullscreen mode Exit fullscreen mode

### 结论

在这个视频中，我们实现了虚构的 Notificator gRPC 服务，在 etcd 中注册它，并从另一个服务用户调用它。

在下一个视频中，我们将回顾通过 JWT (SON Web 令牌)进行的服务授权。