# 无服务器 Go 入门

> 原文：<https://dev.to/yos/getting-started-with-serverless-go--1lff>

> 在 [yos.io](https://yos.io/) 找到更多有趣的文章

[![Getting Started with Serverless Go](../Images/d21885af73cb5108b26fcabd836f5a98.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Xra-gLSz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/LHLrXQZ.jpg)

2018 年 1 月，AWS Lambda 发布了对 Go 语言的[官方支持](https://aws.amazon.com/blogs/compute/announcing-go-support-for-aws-lambda/)。

在本指南中，您将了解如何开始使用无服务器框架在 AWS Lambda 上构建 Go 应用程序。这个简要的指南由两部分组成:一个关于 Go 语言的简要部分和一个实践部分，在实践部分您将构建一个无服务器的 Go CRUD API。

> [最终应用](https://github.com/yosriady/serverless-crud-go)在 Github 上有。只需点击部署！

我们开始吧！

# 围棋语言

首先，让我们在您的机器上设置 Go，并简要了解一下 Go 语言。

## 设置

[下载 Go](https://golang.org/dl/) 和[遵循安装说明](https://golang.org/doc/install)。

> 在 OSX 上，你可以下载`go1.9.3.darwin-amd64.pkg`包文件，打开它，按照提示安装 Go 工具。这个包将 Go 发行版安装到`/usr/local/go`。

要测试您的 Go 安装，请打开一个新的终端并输入:

```
$ go version
go version go1.9.2 darwin/amd64 
```

Enter fullscreen mode Exit fullscreen mode

然后，将以下内容添加到您的`~/.bashrc`中，以设置您的`GOROOT`和`GOPATH`环境变量:

```
export GOROOT=/usr/local/go
export GOPATH=/Users/<your.username>/gopath
export PATH=$PATH:$GOROOT/bin:$GOPATH/bin% 
```

Enter fullscreen mode Exit fullscreen mode

```
source ~/.bashrc 
```

Enter fullscreen mode Exit fullscreen mode

## 基础知识

接下来，尝试建立一个工作区:在`$GOPATH/src/learn-go/`中创建一个目录，并在该目录中创建一个名为`hello.go`的文件。

```
$ mkdir learn-go
$ cd learn-go
$ touch hello.go 
```

Enter fullscreen mode Exit fullscreen mode

```
// hello.go

package main

import "fmt"

func main() {
   fmt.Printf("hello, world\n")
} 
```

Enter fullscreen mode Exit fullscreen mode

通过调用`go run hello.go`运行您的代码。
你也可以`go build`将程序转换成二进制文件，这让我们可以直接执行构建好的二进制文件:

```
$ go build hello.go 
```

Enter fullscreen mode Exit fullscreen mode

上面的命令将在源代码旁边的目录中构建一个名为`hello`的可执行文件。执行它以查看问候语:

```
$ ./hello
hello, world 
```

Enter fullscreen mode Exit fullscreen mode

如果您看到“hello，world”消息，那么您的 Go 安装正在工作！

## 套餐管理

[`dep`](https://github.com/golang/dep) 是一款针对 Go 的依赖管理工具。

在 MacOS 上，您可以使用 Homebrew 安装或升级到最新发布的版本:

```
$ brew install dep
$ brew upgrade dep 
```

Enter fullscreen mode Exit fullscreen mode

首先，在您的`$GOPATH/src` :
中创建一个新目录`learn-dep/`

```
$ mkdir learn-dep
$ cd learn-dep 
```

Enter fullscreen mode Exit fullscreen mode

用`dep init` :
初始化项目

```
$ dep init
$ ls
Gopkg.lock Gopkg.toml vendor 
```

Enter fullscreen mode Exit fullscreen mode

`dep init`将创建以下内容:

*   `Gopkg.lock`是您在项目中使用的所有包的精确版本的记录。
*   `Gopkg.toml`是您的项目所依赖的包列表。
*   `vendor/`是安装项目依赖项的目录。

您可以使用`-add`标志添加新的依赖项:

```
$ dep ensure -add github.com/pkg/errors 
```

Enter fullscreen mode Exit fullscreen mode

> 有关详细的使用说明，请查看官方的 [`dep`文档](https://golang.github.io/dep/docs/daily-dep.html)。

## AWS Lambda Go 编程模型

你用 AWS Lambda 支持的语言之一为 Lambda 函数编写代码。无论您选择哪种语言，为 Lambda 函数编写代码都有一个通用模式，其中包括以下核心概念:

*   **处理程序**–处理程序是 AWS Lambda 调用的函数，用于开始执行您的 Lambda 函数。您的处理程序应该处理传入的事件数据，并可能调用代码中的任何其他函数/方法。

*   **上下文对象**–AWS Lambda 还将一个上下文对象传递给处理函数，它允许您检索元数据，例如 AWS Lambda 终止 Lambda 函数之前剩余的执行时间。

*   **日志**–你的 Lambda 函数可以包含日志语句。AWS Lambda 将这些日志写入 CloudWatch 日志。

*   **异常**–成功结束请求或通知 AWS Lambda 执行过程中出现错误有不同的方式。如果您同步调用该函数，那么 AWS Lambda 会将结果转发回客户端。

您的 Lambda 函数代码必须以无状态风格编写，并且与底层计算基础设施没有任何关联。您的代码应该期望本地文件系统访问、子进程和类似的工件被限制在请求的生存期内。
持久状态应该存储在亚马逊 S3、亚马逊 DynamoDB 或其他云存储服务中。

### 走λ功能

您的 Go 程序被编译成静态链接的二进制文件，打包成 Lambda 部署包，然后上传到 AWS Lambda。

您通过包含[github.com/aws/aws-lambda-go/lambda](https://github.com/aws/aws-lambda-go/lambda)包和一个`main()`函数:
来编写您的 Go 处理器函数代码

```
package main

import (
  "fmt"
  "context"
  "github.com/aws/aws-lambda-go/lambda"
)

type MyEvent struct {
  Name string `json:"name"`
}

func HandleRequest(ctx context.Context, name MyEvent) (string, error) {
  return fmt.Sprintf("Hello %s!", name.Name ), nil
}

func main() {
  lambda.Start(HandleRequest)
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意以下几点:

*   **包主**:在 Go 中，包含`func main()`的包必须一直命名为`main`。

*   **import** :使用它来包含 Lambda 函数需要的库。

    *   **上下文**:上下文对象。
    *   **fmt** :用于格式化函数返回值的 Go 格式化对象。
    *   **github.com/aws/aws-lambda-go/lambda**:如前所述，为 Go 实现了 Lambda 编程模型。
*   **func HandleRequest(ctx 上下文。Context，name string) (string，error)** :这是您的 Lambda 处理程序签名，包含将要执行的代码。此外，包含的参数表示以下内容:

    *   **ctx 上下文。Context** :为 Lambda 函数调用提供运行时信息。ctx 是您声明的变量，用于利用通过上下文对象获得的信息。
    *   **name string** :输入类型，变量名为 name，其值将在 return 语句中返回。
    *   **字符串错误**:返回标准错误信息。
    *   **返回 fmt。Sprintf("你好%s！"，name)，nil** :简单地用您在处理程序签名中提供的名字返回一个格式化的“Hello”问候。`nil`表示没有错误，功能执行成功。
*   **func main()** :执行 Lambda 函数代码的入口点。这是必需的。通过在`func main(){}`代码括号之间添加`lambda.Start(HandleRequest)`，你的 Lambda 函数将被执行。

每个 AWS 事件源(API 网关、DynamoDB 等。)有自己的输入/输出结构。
例如，由 API 网关事件触发的 lambda 函数使用`events.APIGatewayProxyRequest`输入结构和
`events.APIGatewayProxyResponse`输出结构:

```
package main

import (
    "context"
    "fmt"

    "github.com/aws/aws-lambda-go/events"
    "github.com/aws/aws-lambda-go/lambda"
)

func handleRequest(ctx context.Context, request events.APIGatewayProxyRequest) (events.APIGatewayProxyResponse, error) {
    fmt.Printf("Body size = %d.\n", len(request.Body))
    fmt.Println("Headers:")
    for key, value := range request.Headers {
        fmt.Printf("    %s: %s\n", key, value)
    }

    return events.APIGatewayProxyResponse{Body: request.Body, StatusCode: 200}, nil
}

func main() {
    lambda.Start(handleRequest)
} 
```

Enter fullscreen mode Exit fullscreen mode

> 有关处理来自 AWS 事件源的事件的更多信息，请参见 [aws-lambda-go/events](https://github.com/aws/aws-lambda-go/tree/master/events) 。

# 构建一个 Go CRUD API

在本节中，您将使用 Go、AWS Lambda 和无服务器框架创建一个 HTTP CRUD API。

## 先决条件

在我们继续之前，请确保您已经:

*   去把`serverless`安装在你的机器上。
*   您的 AWS 帐户已设置。

> 不熟悉无服务器？获取[无服务器](https://www.goingserverless.co)！

## 设计

对于我们后端的 HTTP API 中的每个端点，您可以创建一个对应于一个动作的函数。例如:

```
`GET /todos`          ->      `listTodos`

`POST /todos`         ->      `addTodo`

`PATCH /todos/{id}`   ->      `completeTodo`

`DELETE /todos/{id}`  ->      `deleteTodo` 
```

Enter fullscreen mode Exit fullscreen mode

`listTodos`函数返回所有的 todos，`addTodo`向 todos 表中添加一个新行，依此类推。在设计函数时，请记住单一责任原则。

## 动手

> 最终的 [`serverless-crud-go`](https://github.com/yosriady/serverless-crud-go) 示例应用程序可以在 Github 上找到作为参考。

从克隆 [`serverless-go-boilerplate`](https://github.com/yosriady/serverless-go-boilerplate) 脚手架开始，它提供了构建无服务器 Go 项目的起点。

将整个项目文件夹复制到您的`$GOPATH/src`中，并将目录重命名为您自己的项目名称。记得将`serverless.yml`中的项目名称更新为自己的项目名称！

`serverless-boilerplate-go`项目的结构如下:

```
.
+-- scripts/
+-- src/
      +-- handlers/
+-- .gitignore
+-- README.md
+-- Gopkg.toml
+-- serverless.yml 
```

Enter fullscreen mode Exit fullscreen mode

在这个样板文件中，我们有以下内容:

*   `scripts`包含一个`build.sh`脚本，可以用来编译 lambda 部署包的二进制文件。
*   是你的处理函数将要存在的地方。
*   `Gokpkg.toml`用于与`dep`工具进行 Go 依赖管理。
*   `serverless.yml`是一个无服务器的项目配置文件。
*   `README.md`包含分步设置说明。

在您的终端中，导航到项目的根目录并安装样板文件中定义的依赖项:

```
cd <your-project-name>
dep ensure 
```

Enter fullscreen mode Exit fullscreen mode

设置好之后，让我们开始构建我们的 CRUD API 吧！

### 第一步:创建`POST /todos`端点

#### 事件

首先，在`serverless.yml` :
中定义`addTodo`函数的 [HTTP 事件](https://serverless.com/framework/docs/providers/aws/events/apigateway/)触发器

```
// serverless.yml

package:
 individually: true
 exclude:
   - ./**

functions:
  addTodo:
    handler: bin/handlers/addTodo
    package:
      include:
        - ./bin/handlers/addTodo
    events:
      - http:
          path: todos
          method: post
          cors: true 
```

Enter fullscreen mode Exit fullscreen mode

在上面的配置中，请注意两件事:

*   在 [`package`](https://serverless.com/framework/docs/providers/aws/guide/packaging/) 块中，我们告诉无服务器框架只将编译好的二进制文件打包在`bin/handlers`中，并排除其他所有东西。
*   `addTodo`函数有一个设置为`POST /todos`端点的 HTTP 事件触发器。

#### 功能

在`src/handlers/`目录下创建一个名为`addTodo.go` :
的新文件

```
// src/handlers/addTodo.go

package main

import (
    "context"
    "fmt"
    "os"
    "time"
    "encoding/json"

    "github.com/aws/aws-lambda-go/lambda"
    "github.com/aws/aws-lambda-go/events"
    "github.com/aws/aws-sdk-go/aws"
    "github.com/aws/aws-sdk-go/aws/session"
    "github.com/aws/aws-sdk-go/service/dynamodb"
    "github.com/aws/aws-sdk-go/service/dynamodb/dynamodbattribute"

    "github.com/satori/go.uuid"
)

type Todo struct {
    ID          string  `json:"id"`
    Description string  `json:"description"`
    Done        bool    `json:"done"`
    CreatedAt   string  `json:"created_at"`
}

var ddb *dynamodb.DynamoDB
func init() {
    region := os.Getenv("AWS_REGION")
    if session, err := session.NewSession(&aws.Config{ // Use aws sdk to connect to dynamoDB
        Region: &region,
    }); err != nil {
        fmt.Println(fmt.Sprintf("Failed to connect to AWS: %s", err.Error()))
    } else {
        ddb = dynamodb.New(session) // Create DynamoDB client
    }
}

func AddTodo(ctx context.Context, request events.APIGatewayProxyRequest) (events.APIGatewayProxyResponse, error) {
    fmt.Println("AddTodo")

    var (
        id = uuid.Must(uuid.NewV4(), nil).String()
        tableName = aws.String(os.Getenv("TODOS_TABLE_NAME"))
    )

    // Initialize todo
    todo := &Todo{
        ID:                 id,
        Done:               false,
        CreatedAt:          time.Now().String(),
    }

    // Parse request body
    json.Unmarshal([]byte(request.Body), todo)

    // Write to DynamoDB
    item, _ := dynamodbattribute.MarshalMap(todo)
    input := &dynamodb.PutItemInput{
        Item: item,
        TableName: tableName,
    }
    if _, err := ddb.PutItem(input); err != nil {
        return events.APIGatewayProxyResponse{ // Error HTTP response
            Body: err.Error(),
            StatusCode: 500,
        }, nil
    } else {
        body, _ := json.Marshal(todo)
        return events.APIGatewayProxyResponse{ // Success HTTP response
            Body: string(body),
            StatusCode: 200,
        }, nil
    }
}

func main() {
    lambda.Start(AddTodo)
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的处理函数中:

*   在`init()`函数中，我们执行一些初始化逻辑:建立到 DynamoDB 的数据库连接。`init()`在`main()`之前自动调用。
*   `addTodo`处理函数解析请求体中的`string`描述。
*   然后，它调用带有环境变量`TODOS_TABLE_NAME`的`ddb.PutItem`来向我们的 DynamoDB 表中插入一个新行。
*   最后，它向客户机返回 HTTP 成功或错误响应。

#### 资源

我们的处理函数将数据存储在 DynamoDB 表中。让我们在`serverless.yml` :
中定义这个表资源

```
# serverless.yml

custom:
  todosTableName: ${self:service}-${self:provider.stage}-todos
  todosTableArn: # ARNs are addresses of deployed services in AWS space
    Fn::Join:
    - ":"
    - - arn
      - aws
      - dynamodb
      - Ref: AWS::Region
      - Ref: AWS::AccountId
      - table/${self:custom.todosTableName}

provider:
  ...
  environment:
    TODOS_TABLE_NAME: ${self:custom.todosTableName}
  iamRoleStatements: # Defines what other AWS services our lambda functions can access
    - Effect: Allow # Allow access to DynamoDB tables
      Action:
        - dynamodb:Scan
        - dynamodb:GetItem
        - dynamodb:PutItem
        - dynamodb:UpdateItem
        - dynamodb:DeleteItem
      Resource:
        - ${self:custom.todosTableArn}

resources:
  Resources: # Supporting AWS services
    TodosTable: # Define a new DynamoDB Table resource to store todo items
      Type: AWS::DynamoDB::Table
      Properties:
        TableName: ${self:custom.todosTableName}
        ProvisionedThroughput:
          ReadCapacityUnits: 1
          WriteCapacityUnits: 1
        AttributeDefinitions:
          - AttributeName: id
            AttributeType: S
        KeySchema:
          - AttributeName: id
            KeyType: HASH 
```

Enter fullscreen mode Exit fullscreen mode

在`resources`块中，我们使用 [AWS CloudFormation](https://aws.amazon.com/cloudformation/) 定义了一个新的`AWS::DynamoDB::Table`资源。

然后，我们通过在
`provider.environment`块中将提供的表的名称作为[环境变量](https://serverless.com/framework/docs/providers/aws/guide/variables/)公开，使其对我们的处理函数可用。

为了让我们的函数访问 AWS 资源，我们还定义了一些 [IAM role](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html) 语句，允许我们的函数执行某些操作，比如对我们的表资源执行`dynamodb:PutItem`操作。

#### 总结

运行`./scripts/build.sh`和`serverless deploy`。如果一切顺利，您将收到一个 HTTP 端点 url，您可以用它来触发您的 Lambda 函数。

通过使用下面的正文向 URL 发出 HTTP POST 请求来验证您的函数:

```
{
  "description": "Hello world"
} 
```

Enter fullscreen mode Exit fullscreen mode

如果一切顺利，您将收到一个成功的`201` HTTP 响应，并能够通过 AWS 控制台在 AWS DynamoDB 表中看到一个新行。

### 第二步:创建`GET /todos`端点

#### 事件

首先，在`serverless.yml` :
中定义`listTodos`函数的 [HTTP 事件](https://serverless.com/framework/docs/providers/aws/events/apigateway/)触发器

```
// serverless.yml

functions:
  listTodos:
    handler: bin/handlers/listTodos
    package:
     include:
       - ./bin/handlers/listTodos
    events:
      - http:
          path: todos
          method: get
          cors: true 
```

Enter fullscreen mode Exit fullscreen mode

#### 功能

在`src/handlers/`目录下创建一个名为`listTodos.go` :
的新文件

```
// src/handlers/listTodos.go

package main

import (
    "context"
    "fmt"
    "encoding/json"
    "os"

    "github.com/aws/aws-lambda-go/lambda"
    "github.com/aws/aws-lambda-go/events"
    "github.com/aws/aws-sdk-go/aws"
    "github.com/aws/aws-sdk-go/aws/session"
    "github.com/aws/aws-sdk-go/service/dynamodb"
    "github.com/aws/aws-sdk-go/service/dynamodb/dynamodbattribute"
)

type Todo struct {
    ID          string  `json:"id"`
    Description string  `json:"description"`
    Done        bool    `json:"done"`
    CreatedAt   string  `json:"created_at"`
}

type ListTodosResponse struct {
    Todos       []Todo  `json:"todos"`
}

var ddb *dynamodb.DynamoDB
func init() {
    region := os.Getenv("AWS_REGION")
    if session, err := session.NewSession(&aws.Config{ // Use aws sdk to connect to dynamoDB
        Region: &region,
    }); err != nil {
        fmt.Println(fmt.Sprintf("Failed to connect to AWS: %s", err.Error()))
    } else {
        ddb = dynamodb.New(session) // Create DynamoDB client
    }
}

func ListTodos(ctx context.Context, request events.APIGatewayProxyRequest) (events.APIGatewayProxyResponse, error) {
    fmt.Println("ListTodos")

    var (
        tableName = aws.String(os.Getenv("TODOS_TABLE_NAME"))
    )

    // Read from DynamoDB
    input := &dynamodb.ScanInput{
        TableName: tableName,
    }
    result, _ := ddb.Scan(input)

    // Construct todos from response
    var todos []Todo
    for _, i := range result.Items {
        todo := Todo{}
        if err := dynamodbattribute.UnmarshalMap(i, &todo); err != nil {
            fmt.Println("Failed to unmarshal")
            fmt.Println(err)
        }
        todos = append(todos, todo)
    }

    // Success HTTP response
    body, _ := json.Marshal(&ListTodosResponse{
        Todos: todos,
    })
    return events.APIGatewayProxyResponse{
        Body: string(body),
        StatusCode: 200,
    }, nil
}

func main() {
    lambda.Start(ListTodos)
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的处理函数中:

*   首先，从环境变量中检索`tableName`。
*   然后，调用`ddb.Scan`从 todos DB 表中检索行。
*   最后，根据结果返回成功或错误的 HTTP 响应。

#### 总结

运行`./scripts/build.sh`和`serverless deploy`。您将收到一个 HTTP 端点 url，您可以用它来触发您的 Lambda 函数。

通过向 URL 发出 HTTP GET 请求来验证您的函数。
如果一切顺利，您将收到一个成功的`200` HTTP 响应，并看到一个 todo JSON 对象列表:

```
> curl https://<hash>.execute-api.<region>.amazonaws.com/dev/todos
{
    "todos": [
        {
            "id": "d3e38e20-5e73-4e24-9390-2747cf5d19b5",
            "description": "buy fruits",
            "done": false,
            "created_at": "2018-01-23 08:48:21.211887436 +0000 UTC m=+0.045616262"
        },
        {
            "id": "1b580cc9-a5fa-4d29-b122-d20274537707",
            "description": "go for a run",
            "done": false,
            "created_at": "2018-01-23 10:30:25.230758674 +0000 UTC m=+0.050585237"
        }
    ]
} 
```

Enter fullscreen mode Exit fullscreen mode

### 第三步:创建`PATCH /todos/{id}`端点

#### 事件

首先，在`serverless.yml` :
中定义`completeTodo`函数的 HTTP 事件触发器

```
// serverless.yml

functions:
  completeTodo:
    handler: bin/handlers/completeTodo
    package:
     include:
       - ./bin/handlers/completeTodo
    events:
      - http:
          path: todos
          method: patch
          cors: true 
```

Enter fullscreen mode Exit fullscreen mode

#### 功能

在`src/handlers/`目录下创建一个名为`completeTodo.go` :
的新文件

```
package main

import (
    "fmt"
    "context"
    "os"
    "github.com/aws/aws-lambda-go/lambda"
    "github.com/aws/aws-lambda-go/events"
    "github.com/aws/aws-sdk-go/aws/session"
    "github.com/aws/aws-sdk-go/service/dynamodb"
    "github.com/aws/aws-sdk-go/aws"
)

var ddb *dynamodb.DynamoDB
func init() {
    region := os.Getenv("AWS_REGION")
    if session, err := session.NewSession(&aws.Config{ // Use aws sdk to connect to dynamoDB
        Region: &region,
    }); err != nil {
        fmt.Println(fmt.Sprintf("Failed to connect to AWS: %s", err.Error()))
    } else {
        ddb = dynamodb.New(session) // Create DynamoDB client
    }
}

func CompleteTodo(ctx context.Context, request events.APIGatewayProxyRequest) (events.APIGatewayProxyResponse, error) {
    fmt.Println("CompleteTodo")

    // Parse id from request body
    var (
        id = request.PathParameters["id"]
        tableName = aws.String(os.Getenv("TODOS_TABLE_NAME"))
        done = "done"
    )

  // Update row
    input := &dynamodb.UpdateItemInput{
        Key: map[string]*dynamodb.AttributeValue{
            "id": {
                S: aws.String(id),
            },
        },
        UpdateExpression: aws.String("set #d = :d"),
        ExpressionAttributeNames: map[string]*string{
            "#d": &done,
        },
        ExpressionAttributeValues: map[string]*dynamodb.AttributeValue{
            ":d": {
                BOOL: aws.Bool(true),
            },
        },
        ReturnValues:     aws.String("UPDATED_NEW"),
        TableName: tableName,
    }
    _, err := ddb.UpdateItem(input)

    if err != nil {
        return events.APIGatewayProxyResponse{ // Error HTTP response
            Body: err.Error(),
            StatusCode: 500,
        }, nil
    } else {
        return events.APIGatewayProxyResponse{ // Success HTTP response
            Body: request.Body,
            StatusCode: 200,
        }, nil
    }
}

func main() {
    lambda.Start(CompleteTodo)
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的处理函数中:

*   首先，从请求的路径参数中检索`id`，从环境变量中检索`tableName`。
*   然后，用`id`、`tableName`和`UpdateExpression`调用`ddb.UpdateItem`，将 todo 的`done`列设置为`true`。
*   最后，根据结果返回成功或错误的 HTTP 响应。

#### 总结

运行`./scripts/build.sh`和`serverless deploy`。您将收到一个 HTTP 补丁端点 url，您可以使用它来触发`completeTodo` Lambda 函数。

通过向`/todos/{id}` url 发出 HTTP 补丁请求，传入一个 todo ID，来验证您的函数。
您应该看到待办事项的`done`状态从`false`更新为`true`。

### 第四步:创建`DELETE /todos/{id}`端点

#### 事件

首先，在`serverless.yml` :
中定义`deleteTodo`函数的 HTTP 事件触发器

```
// serverless.yml

functions:
  deleteTodo:
    handler: bin/handlers/deleteTodo
    package:
     include:
       - ./bin/handlers/deleteTodo
    events:
      - http:
          path: todos
          method: delete
          cors: true 
```

Enter fullscreen mode Exit fullscreen mode

#### 功能

在`src/handlers/`目录下创建一个名为`deleteTodo.go` :
的新文件

```
package main

import (
    "fmt"
    "context"
    "os"
    "github.com/aws/aws-lambda-go/lambda"
    "github.com/aws/aws-lambda-go/events"
    "github.com/aws/aws-sdk-go/aws/session"
    "github.com/aws/aws-sdk-go/service/dynamodb"
    "github.com/aws/aws-sdk-go/aws"
)

var ddb *dynamodb.DynamoDB
func init() {
    region := os.Getenv("AWS_REGION")
    if session, err := session.NewSession(&aws.Config{ // Use aws sdk to connect to dynamoDB
        Region: &region,
    }); err != nil {
        fmt.Println(fmt.Sprintf("Failed to connect to AWS: %s", err.Error()))
    } else {
        ddb = dynamodb.New(session) // Create DynamoDB client
    }
}

func DeleteTodo(ctx context.Context, request events.APIGatewayProxyRequest) (events.APIGatewayProxyResponse, error) {
    fmt.Println("DeleteTodo")

    // Parse id from request body
    var (
        id = request.PathParameters["id"]
        tableName = aws.String(os.Getenv("TODOS_TABLE_NAME"))
    )

    // Delete todo
    input := &dynamodb.DeleteItemInput{
        Key: map[string]*dynamodb.AttributeValue{
            "id": {
                S: aws.String(id),
            },
        },
        TableName: tableName,
    }
    _, err := ddb.DeleteItem(input)

    if err != nil {
        return events.APIGatewayProxyResponse{ // Error HTTP response
            Body: err.Error(),
            StatusCode: 500,
        }, nil
    } else {
        return events.APIGatewayProxyResponse{ // Success HTTP response
            StatusCode: 204,
        }, nil
    }
}

func main() {
    lambda.Start(DeleteTodo)
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的处理函数中:

*   首先，从请求的路径参数中检索`id`，从环境变量中检索`tableName`。
*   然后，你用`id`和`tableName`调用`ddb.DeleteItem`。
*   最后，根据结果返回成功或错误的 HTTP 响应。

##### 总结

运行`./scripts/build.sh`和`serverless deploy`。您将收到一个 HTTP 删除端点 url，您可以使用它来触发`completeTodo` Lambda 函数。

通过向`/todos/{id}` url 发出 HTTP DELETE 请求，传入一个 todo ID 来验证您的函数。
您应该看到 todo 项已经从 DB 表中删除。

# 在关闭

**恭喜你！**你已经没有服务器了！

在本指南中，您学习了如何将 API 设计和开发为一组单一用途的函数、事件和资源。您还学习了如何使用 AWS Lambda 和无服务器框架构建一个简单的 Go CRUD 后端。

> [最终应用](https://github.com/yosriady/serverless-crud-go)在 Github 上有。

谢谢你的阅读！

## 了解更多

有兴趣了解更多信息吗？获得免费电子书！

 [![Serverless Go book](../Images/3a146d20e2de9351bc49f4f9ccb4fe55.png "Going Serverless: Building Scalable Applications with the Serverless Framework and AWS Lambda")
T4】](https://leanpub.com/serverless-go)

**[无服务器 Go:实用指南](https://leanpub.com/serverless-go)** 教你如何用 Go 语言、无服务器框架和 AWS Lambda 构建可伸缩的应用。您将学习如何设计、开发和测试从计划到生产的无服务器 Go 应用程序。

> 在 [yos.io](https://yos.io/) 找到更多有趣的文章