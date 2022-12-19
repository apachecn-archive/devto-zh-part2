# Go 中的 gRPC 快速入门

> 原文：<https://dev.to/dannypsnl/grpc-quick-start-in-go-399l>

RPC 是什么？RPC 的意思是“远程过程调用”。这个概念被称为作为本地功能的远程功能。

然后 gRPC？它是一个帮助你创建 RPC 的框架。

传统 RPC 有一些问题，使它很难使用。比如，你怎么知道，你得到的是什么类型的信息？

通常，我们使用 JSON 或其他格式。但是编组和解组很快成为一个大问题。因为随着时间的推移，我们实际上不知道哪个服务使用哪个字段，因此我们不能减少任何东西。

出于同样的原因，服务器和客户端将会走得更远。

这些都不会成为 gRPC 中的问题。

在 gRPC 中，您为您的服务定义了一个`*.proto`文件。

在这里，我们将创建一个名为`UserService`
的

```
syntax = "proto3";

package user;

service UserService {
  rpc GetUser (Empty) returns (User) {}
}

// define message type
message Empty {}
message User {
  string name = 1;
  int32 age = 2;
} 
```

Enter fullscreen mode Exit fullscreen mode

为了生成代码，我通常使用`go generate ./...`

所以让我们有一个文件`gen.go`，只留下一个关于你想执行什么命令的注释。

```
//go:generate sh -c "protoc -I./user --go_out=plugins=grpc:./user ./user/*.proto" 
```

Enter fullscreen mode Exit fullscreen mode

实现服务:

```
import "path/to/grpc_generated/user"

type UserService struct{}

func (us *UserService) GetUser(ctx context.Context, u *user.Empty) (*user.User, error) {
    return &user.UserName{
        Name: "Danny",
        Age: 21,
    }, nil
} 
```

Enter fullscreen mode Exit fullscreen mode

创建服务器:

```
import (
    "net"
    "path/to/grpc_generated/user"
    "google.golang.org/grpc"
)

func main() {
    l, err := net.Listen("tcp", ":50051")
    // handle err
    server := grpc.NewServer()

    service := user.UserServiceServer(&UserService{})
    user.RegisterUserServiceServer(server, service)

    err = server.Serve(l)
    // handle err
} 
```

Enter fullscreen mode Exit fullscreen mode

最终是我们的客户:

```
import (
    "fmt"
    "net"
    "path/to/grpc_generated/user"
    "google.golang.org/grpc"
)

func main() {
    conn, err := grpc.Dial("localhost:50051", grpc.WithInsercure())
    // handle err
    defer conn.Close()
    client := user.NewUserServiceClient(conn)
    u, err := client.GetUser(context.Background(), &user.Empty{})
    // handle err
    fmt.Printf("Name: %s, Age: %d", u.Name, u.Age)
} 
```

Enter fullscreen mode Exit fullscreen mode

之后，从项目根目录运行`go generate ./...`。

然后`go run server.go`，打开另一个终端`go run client.go`

我通常不会提交生成的代码(除非提交它有意义)，所以我通常会在文件`.gitignore`中写`*.pb.go`

更多信息:

*   [grpc.io](https://grpc.io/)