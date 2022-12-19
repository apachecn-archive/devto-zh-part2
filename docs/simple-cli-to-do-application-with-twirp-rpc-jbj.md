# 使用 Twirp RPC 的简单 CLI 待办应用程序

> 原文：<https://dev.to/williamhgough/simple-cli-to-do-application-with-twirp-rpc-jbj>

## 简介

你在推特上看到过，在媒体上读到过，在你所有的简讯里都有...那么什么是 RPC 呢？在这篇文章中，我将向你介绍使用 TwitchTv 神奇的 RPC 库( [Twirp](https://twitchtv.github.io/twirp/docs/intro.html) )的技术，并消除你的好奇心。我们将构建一个经典的教程应用程序...待办事项列表，但是这一次，使用 CLI 进行交互。如果你不用框架来建立任务清单，你知道如何使用它吗？！玩笑归玩笑，待办事项清单是一个完美的尺寸，只需要简单的 CRUD 工具就能工作，这是一个尝试新事物的好方法。

#### 为什么是 RPC？

远程过程调用(RPC)主要用于系统到系统的通信，例如微服务到微服务。如果你只是想要一个普通的公共 API，那么你还是想用 REST。但是，如果您有一个 API 需要轮询来自其他地方的数据，您可以使用 RPC 与外部服务通信，如果它也实现了它的话。许多大型组织在他们所有的系统通信中使用它，例如谷歌用一个叫做 Stubby 的基础设施来做这个，它包括一个你可能已经熟悉的开源版本- [gRPC](https://grpc.io) 。

##### RPC 的好处:

*   HTTP/2 传输上的双向异步数据流。
*   能够为大多数通用语言生成客户机和服务器存根。
*   更容易序列化结构化数据。
*   可以比 XML/JSON 小 3 - 10 倍。
*   可以快 20 - 100 倍。

## 设置&先决条件

在我们做任何事情之前，我们需要安装一些东西:

```
// Install Twirp protoc generator
go get github.com/twitchtv/twirp/protoc-gen-twirp

// Install Protoc - The protobuf compiler
https://github.com/google/protobuf/releases/tag/v3.6.0

// Mac users can install the above with HomeBrew
brew update && brew install protobuf

// Install Golang Protobuf support
go get -u github.com/golang/protobuf/protoc-gen-go 
```

## 就这样开始了

现在我们已经安装了所有的东西，我们可以开始有趣的部分了，写代码！首先，我们需要创建项目目录并充实结构:

```
cd $GOPATH/src/github.com/<your_username>
mkdir rpc-todo && cd $_
mkdir rpc
cd rpc && mkdir todo && cd $_ 
```

我们在这里做了一些事情来分离我们的应用程序结构，我将在教程中解释每一部分。现在，让我解释一下`rpc`目录。这是一个重要的约定，有时会出现在不同的名称空间下；比如，你可能会看到它被命名为`proto`。该约定充当 protobuf 服务定义及其生成代码的位置。在我们的`rpc`目录中，我们创建了第一个服务目录`todo` *。现在我们可以在一个新文件中写出我们的服务定义，`service.proto`:*

```
syntax = "proto3";

package <your_github_username>.rpctodo.todo;
option go_package = "todo";

service Todo {
    rpc MakeTodo(Todo) returns (Empty);
    rpc GetTodo(TodoQuery) returns (Todo);
    rpc AllTodos(Empty) returns (TodoResponse);
    rpc RemoveTodo(TodoQuery) returns (Empty);
    rpc MarkComplete(TodoQuery) returns (Empty);
}

message TodoQuery {
    int32 id = 1;
}

message TodoResponse {
    repeated Todo todos = 1;
}

message Todo {
    int32 ID = 1;
    string title = 2;
    bool complete = 3;
}

message Empty {
} 
```

这里发生了一些事情，所以让我们从头开始。首先，我们将 protobuf 文件的语法版本声明为版本 3。其次，我们声明原型包。这以下列格式工作:

```
package <user/organisation>.<repository_name>.<service_name>;

// For example, mine is: package williamhgough.commando.todo; 
```

然后，我们提供希望在作为 Go 包导入服务时出现的名称。一旦所有这些都完成了，我们就可以开始定义我们的服务了。可以认为这是为我们的 API 定义接口。我们服务中的每个方法都接受参数并返回值，这些值必须是`message`类型。同样，我们将关键字`service`等同于一个接口，我们可以将消息视为 Go 中的`struct`类型。我们只是定义了一个新的类型，并概述了它的字段。尽管如此，还是有一些明显的区别。您可能已经注意到了每个消息字段后面的`= 1, = 2, = 3`，它们代表了字段的顺序。这允许我们稍后回来添加更多字段或更改顺序。

注意`Empty`消息类型和我们的`TodoResponse`中的关键字`repeated`也很重要。由于 protobuf 不使用假定的返回类型`void`或`null`，我们必须定义 Empty 来代替。在 TodoResponse 中，`repeated`关键字用于返回多个 Todo 类型的项目。由于 protobufs 在 Golang 结构中没有用于比较的数组类型，所以您应该使用 Todo 的一部分，就像这样`[]Todo`。有关 protobuf 语法的更多信息，请查看这里的文档。

现在我们已经了解了原型定义，让我们使用`protoc`命令生成 Golang 服务代码。在项目的根目录中，执行以下操作:

`protoc --proto_path=$GOPATH/src:. --twirp_out=. --go_out=. ./rpc/todo/service.proto`

现在你应该看到，在你的`rpc/todo`服务目录中，有两个新文件`service.pb.go`和`service.twirp.go`。这些是生成的文件。不要编辑它们！Twirp 和 Protoc 编译器已经生成了用于与 RPC 服务交互的客户机和服务器存根。

[![image](img/681c82fb535becb791ddc539ae262baf.png)T2】](https://i.giphy.com/media/l41lNp8dHpfaJE25O/giphy.gif)

我们将要使用生成的文件，所以花一点时间通读它们并检查生成的 Go 代码。下一步是实现我们的 Todo 服务接口。在项目目录的根目录下，执行以下命令:

```
mkdir internal && cd $_
mkdir todoserver && cd $_
touch server.go 
```

在这里，我们创建了我们的`internal`目录，这是为了保存我们的服务实现，每个实现一个目录，正如你所看到的，我们已经将我们的**命名为 todoserver** 。在您刚刚创建的`server.go`文件中，让我们添加以下代码:

```
package  todoserver  import  (  "context"  "github.com/twitchtv/twirp"  // Import our protobuf package, namespace it to pb. This is a convention!  pb  "github.com/<your_username>/rpc-todo/rpc/todo"  )  // Create our Server type  type  Server  struct{}  // Seed our application with one To-Do to start with.  // The map holds the To-Do ID as the key to a To-Do value.  var  todos  =  map[int]*pb.Todo{  1:  &pb.Todo{ID:  1,  Title:  "hello world",  Complete:  false},  }  // MakeTodo creates a To-Do  func  (s  *Server)  MakeTodo(ctx  context.Context,  todo  *pb.Todo)  (*pb.Empty,  error)  {  return  nil,  nil  }  // GetTodo returns a single To-Do for given ID.  func  (s  *Server)  GetTodo(ctx  context.Context,  query  *pb.TodoQuery)  (*pb.Todo,  error)  {  return  nil,  nil  }  // AllTodos returns all To-Dos.  func  (s  *Server)  AllTodos(ctx  context.Context,  e  *pb.Empty)  (*pb.TodoResponse,  error)  {  return  nil,  nil  }  // RemoveTodo deletes the To-Do with the given ID.  func  (s  *Server)  RemoveTodo(ctx  context.Context,  q  *pb.TodoQuery)  (*pb.Empty,  error)  {  return  nil,  nil  }  // MarkComplete sets the To-Do with given ID to completed.  func  (s  *Server)  MarkComplete(ctx  context.Conext,  q  *pb.TodoQuery)  (*pb.Empty,  error)  {  return  nil,  nil  } 
```

上面的代码创建了一个新的服务器类型，并让它实现我们的 Todo 服务接口。目前，我们的代码只是满足了接口，并不会真正发挥作用。现在我们有了基本的实现，让我们用我们需要的实际代码来完成它们。请务必通读代码中的注释，因为它们应该解释每一点发生了什么:

```
func  (s  *Server)  MakeTodo(ctx  context.Context,  todo  *pb.Todo)  (*pb.Empty,  error)  {  // Validate the given ID, return error if not > 0  if  todo.ID  <  1  {  return  nil,  twirp.InvalidArgumentError("ID:",  "No ID provided")  }  // Validate Title, should not add empty To-Do.  if  todo.Title  ==  ""  {  return  nil,  twirp.InvalidArgumentError("Title:",  "No title given")  }  // Set To-Do in the Todos map, passing ID as key.  todos[todo.ID]  =  todo  // Return Empty response and nil error.  return  &pb.Empty{},  nil  }  func  (s  *Server)  GetTodo(ctx  context.Context,  q  *pb.TodoQuery)  (*pb.Todo,  error)  {  // Validate the given ID, return error if not > 0  if  q.Id  <  1  {  return  nil,  twirp.InvalidArgumentError("ID:",  "Must be greater than zero")  }  // Return To-Do with given ID from map and nil error  return  todos[q.Id],  nil  }  func  (s  *Server)  AllTodos(ctx  context.Context,  e  *pb.Empty)  (*pb.TodoResponse,  error)  {  // Create new slice of To-Dos  t  :=  []*pb.Todo{}  // for each one in our map, add to slice  for  _,  v  :=  range  todos  {  t  =  append(t,  v)  }  // If there are no todos, return error  if  len(t)  <  1  {  return  nil,  twirp.InternalError("No todos found!")  }  // Otherwise return Todos and nil error  return  &pb.TodoResponse{  Todos:  t,  },  nil  }  func  (s  *Server)  RemoveTodo(ctx  context.Context,  q  *pb.TodoQuery)  (*pb.Empty,  error)  {  // Validate ID is greater than 0  if  q.Id  <  1  {  return  nil,  twirp.InvalidArgumentError("ID:",  "Must be greater than zero")  }  // Remove To-Do with given ID from map  delete(todos,  q.Id)  // Both return nil  return  &pb.Empty{},  nil  }  func  (s  *Server)  MarkComplete(ctx  context.Context,  q  *pb.TodoQuery)  (*pb.Empty,  error)  {  // Validate ID greater than 1  if  q.Id  <  1  {  return  nil,  twirp.InvalidArgumentError("ID:",  "Must be greater than zero")  }  // Find To-Do with given ID in Todos map and set complete to true  todos[q.Id].Complete  =  true  // Return nil for both  return  &pb.Empty{},  nil  } 
```

好了，我们快到了。这个应用程序的最后阶段涉及到构建我们的 CLI 程序。我们需要一个运行服务器的程序和一个与之交互的命令行界面。这两个应用程序都非常简单。让我们继续创建服务器。在项目根目录中，执行以下操作:

```
mkdir cmd && cd $_
mkdir todo-server && cd $_
touch main.go 
```

在这里，我们已经创建了我们的客户端应用程序目录和主程序。如果你不熟悉 Go 应用程序的结构和使用`cmd`目录，这是一个使用命令行界面构建应用程序的简单模式。我们用期望的程序名命名`cmd`中的目录，这样当我们运行`go build`时，我们会得到一个名为`todo-server`的二进制文件，而不是`main`。在`main.go`中，添加以下代码:

```
package  main  import  (  "fmt"  "net/http"  "github.com/<your_username>/rpc-todo/internal/todoserver"  pb  "github.com/<your_username>/rpc-todo/rpc/todo"  )  func  main()  {  // Create new Server from our server package.  server  :=  &todoserver.Server{}  // Create Twirp Handler for our server from generated server stub.  // This takes the form pb.New<SERVICE_NAME>Server()  twirpHandler  :=  pb.NewTodoServer(server,  nil)  // Start HTTP server on 8080, passing our twirpHandler as the servemux.  http.ListenAndServe(":8080",  twirpHandler)  fmt.Println("To-Do server listening on :8080")  } 
```

接下来，我们创建客户端程序:

```
cd cmd
mkdir todo && cd $_
touch main.go 
```

客户端应用程序比我们的服务器有更多的功能，因为我们需要解析任何命令行标志，验证它们，然后执行所需的过程。所有这些都可以通过`flags`包和一个简单的`switch`语句来实现。简单明了:

```
package  main  import  (  "context"  "flag"  "fmt"  "net/http"  "github.com/<your_username>/rpc-todo/rpc/todo"  )  // Create global To-Do counter  var  todoCount  int32  =  1  func  main()  {  // Define our interaction flags.  action  :=  flag.String("command",  "list",  "Command to interact with Todos. create, get, list, delete")  title  :=  flag.String("title",  "",  "The todo title")  id  :=  flag.Int("id",  1,  "The ID of the todo you wish to retrieve")  flag.Parse()  // Create the generated Twirp Protobuf Client for our Todo service  client  :=  todo.NewTodoProtobufClient("http://localhost:8080",  &http.Client{})  switch  *action  {  case  "create":  _,  err  :=  client.MakeTodo(context.Background(),  &todo.Todo{  ID:  todoCount  +  1,  Title:  *title,  Complete:  false,  })  if  err  !=  nil  {  fmt.Println("could not create todo:",  err)  }  case  "list":  res,  err  :=  client.AllTodos(context.Background(),  &todo.Empty{})  if  err  !=  nil  {  fmt.Println("could not fetch todos:",  err)  }  for  i,  t  :=  range  res.Todos  {  fmt.Printf("%d. %s [%v]\n",  i+1,  t.Title,  t.Complete)  }  case  "get":  todo,  err  :=  client.GetTodo(context.Background(),  &todo.TodoQuery{  Id:  int32(*id),  })  if  err  !=  nil  {  fmt.Println("could not fetch todo:",  err)  }  fmt.Printf("%d. %s [%v]\n",  todo.ID,  todo.Title,  todo.Complete)  case  "delete":  _,  err  :=  client.RemoveTodo(context.Background(),  &todo.TodoQuery{  Id:  int32(*id),  })  if  err  !=  nil  {  fmt.Println("could not remove todo:",  err)  }  case  "complete":  _,  err  :=  client.MarkComplete(context.Background(),  &todo.TodoQuery{  Id:  int32(*id),  })  if  err  !=  nil  {  fmt.Println("could not complete todo:",  err)  }  default:  fmt.Println("invalid command, please try again.")  }  } 
```

瞧啊。就是这样！看看我们如何自动生成 Twirp 客户端，使用与我们的服务器代码相同的 API？这使得它的使用和交互非常直观。我们剩下要做的就是在命令行中验证它是否正常工作。在项目根目录中打开两个终端窗口:

*窗口 1 -启动待办服务服务器。*

```
cd cmd/todo-server
go run main.go

// Output
fmt.Println("To-Do server listening on :8080") 
```

*窗口 2——与待办客户端互动。*

```
cd cmd/todo

// list To-Dos
go run main.go
1\. hello world [false]

// create To-Do
go run main.go -command="create" -title="Hello DevTheWeb"

// get specific To-Do
go run main.go -command="get" -id=2
2\. Hello DevTheWeb [false]

// Complete To-Do
go run main.go -command="complete" -id=1

// Remove To-Do
go run main.go -command="delete" -id=1 
```

就这样结束了！你做到了！你现在可以添加基本的 RPC 到你的技能中。

好了，这是一个很大的进步，希望我能够以一种让你感觉清晰和自信的方式来解释！我还想推荐尝试一下谷歌的 gRPC 库。我在这里选择了 Twirp，因为我发现它更直观、更易于使用。我能给你的最好建议是，继续尝试用这些新获得的知识编写另一个 RPC 服务。也许是一个用来找书的图书馆或者是一个使用 [OMDB](http://www.omdbapi.com/) 的电影搜索工具？古老的谚语中有一个不可否认的真理:

> *熟能生巧*

再次感谢您的阅读，请考虑分享或通过 twitter [@whg_codes](https://twitter.com/whg_codes) 联系我。

本教程的原始应用程序源代码可以在我的 GitHub 上找到:[github.com/williamhgough/comman-do](https://github.com/williamhgough/comman-do)*