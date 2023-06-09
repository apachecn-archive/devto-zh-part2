# 使用 GraphQL 进行服务器到服务器的通信

> 原文：<https://dev.to/rhymes/server-to-server-communication-using-graphql-4b01>

我几周前“记录”的 [Go API 服务器](https://dev.to/rhymes/what-ive-learned-in-7-days-of-go-5e3)的开发已经向前推进了。

从那以后，我添加了测试(主要是功能性的)，重构了一些部分，并对 Go 有了更多的了解。

API 服务器拥有自己的数据库。现在，对于同一个应用程序，我们正在构建一个前端 web 应用程序，它的一些功能需要与 API 服务器通信并交换数据。

API 服务器目前为移动客户端公开了一个 REST API。

我的第一个想法是在 API 中添加一些端点，但是我立刻拒绝了这个想法，因为我绝对不希望移动客户端能够意外地访问这些端点；-).

我的第二个想法是在同一台服务器上创建一个单独的 REST API，并以不同的方式保护它。基本上将移动客户端的端点与此 web 应用程序使用的端点分开，并通过不同的身份验证机制调用它们，然后就到此为止。

web 应用程序在很大程度上处于概念和设计阶段，我们不确定最终的应用程序会如何，所以我们不能确定 web 应用程序将需要或使用多少 Go API 域。

我选择使用 GraphQL，它或多或少地让我摆脱了设计另一个 API 的负担。它也给予了自由的内省，这在这个阶段是完美的。

## Go GraphQL 服务器

第一步是在 Go 中设置一个 GraphQL 服务器。经过一番研究后，我决定使用 gqlgen。gqlgen 生成(！！)给定类型定义和模式的 GraphQL 服务器。

一个模式示例:

```
type  Query  {  todos:  [Todo!]!  todo(todoID:  ID!):  Todo  }  type  Mutation  {  createTodo(todo:  TodoInput!):  Todo  }  type  Todo  {  todoID:  ID!  title:  String!  text:  String!  }  input  TodoInput  {  title:  String!  text:  String!  } 
```

Enter fullscreen mode Exit fullscreen mode

在这里，按照标准的 [graphql 模式](https://graphql.org/learn/schema/)，我们定义了两种类型的查询(一种检索所有 todo，一种检索特定的 todo)和一种变异(请求变异状态)来创建新的 todo。变异需要一个`TodoInput`，它基本上是 todo 模型减去自动生成的 ID。

通常情况下,`gqlgen`会生成在 GraphQL 中使用的实际结构/模型。因为我已经有了它(REST API 中使用的那个)，幸运的是我可以告诉生成器从业务逻辑中“导入”它。更多细节在 [gqlgen 文档](https://gqlgen.com/getting-started/)中。

下一步是创建`resolvers`，由 GraphQL 服务器调用以返回或创建数据的方法，例如:

```
package gql

type Resolver struct {
    DB *DataStore
}

func (r *Resolver) Query_todos(ctx context.Context) ([]models.Todo, error) {
    todos, err := r.DB.Todos()
    return todos, err
}

func (r *Resolver) Query_todo(ctx context.Context, todoID string) (*models.Todo, error) { 
    todo, err := r.DB.Todo(todoID)
    return todo, err
}

func (r *Resolver) Mutation_createTodo(ctx context.Context, todo models.Todo) (*models.Todo, error) {
    todo.TodoID = uuid.NewV4().String()
    err := r.DB.CreateTodo(&todo)
    return &todo, err
} 
```

Enter fullscreen mode Exit fullscreen mode

第一个调用业务逻辑来检索所有 todo，第二个检索单个 todo，第三个创建一个新的 todo。我 100%重用了一些与 REST API 相同的业务逻辑。

最后一步是创建服务器本身:

```
gqlServer := &gql.Resolver{DB: db}
router.Handle("/gql-playground", handler.Playground("Todo", "/graphql"))
router.Handle("/graphql", handler.GraphQL(gql.MakeExecutableSchema(gqlServer))) 
```

Enter fullscreen mode Exit fullscreen mode

这三行神奇的代码展示了一个测试查询的平台和一个实际的 graphql 服务器。我不建议在生产:D 中启用游乐场

## Python GraphQL 客户端

现在我们有了一个服务器，我们需要一个 Python 的客户端来查看一切是否正常。

我用的是 [pygql](https://pypi.org/project/pygql/) ，像这样:

```
from pygql import Client, gql
from pygql.transport.requests import RequestsHTTPTransport

# the URL is the url of the Go server transport = RequestsHTTPTransport('http://localhost:8080/graphql', use_json=True)
# we tell Python to fetch the schema by itself! client = Client(transport=transport, fetch_schema_from_transport=True)

# let's build a query to get all the ids and the titles of the todos in the db query = gql("""
  query {
    todos {
      todoID
      title
    }
  }
""")

client.execute(query) 
```

Enter fullscreen mode Exit fullscreen mode

这是输出:

```
{
    'todos': [{
        'todoID': '403d9f8c-cdc3-4784-9582-aa0677681f4a',
        'title': 'I need to remember'
    }, {
        'todoID': '90b0edbc-75a8-4ae3-b345-11c520578f26',
        'title': 'There is something I need to remember'
    }]
} 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，GraphQL 服务器只发送我们请求的内容，调用正确的解析器并执行所有正确的转换。

通过给查询添加一个突变

```
query = gql("""
mutation createTodo {
  createTodo(todo: {
    title: "Another thing I need to remember", 
    text: "A very long text about everything I need to do"
  }) {
    todoID
    title
    text
  }
}
""") 
```

Enter fullscreen mode Exit fullscreen mode

我们在服务器上创建一个新的 todo，并在需要时取回它的 id 和其余的字段。

## 结论

通过这种方式，我们可以在两个服务器之间“增量地”构建一个 API，而无需“提交”一个明确的模式。在服务器上更改模式非常容易，客户端会知道会发生什么。如果我们请求的字段不在模式中，Python 客户端也会引发异常，而不必往返服务器以获取错误。最重要的是由客户端决定获取什么数据。

几个注意事项:

*   这两者之间没有授权，这不是一个好主意，原因显而易见。我目前正在决定如何正确地做这件事。我在考虑使用[客户端凭证 OAuth 2 流程](https://www.digitalocean.com/community/tutorials/an-introduction-to-oauth-2#grant-type-client-credentials)(大声喊出来，数字海洋的伟大 OAuth 2 教程！).这样，我可以控制令牌的生命周期以及谁可以访问。如果你有建议，我们非常欢迎。

*   它不全是彩虹和独角兽，GraphQL 在日志中是不透明的，它不能与 HTTP 缓存一起工作。你可以在这里阅读这两种方法的全面对比:[https://philsturgeon . uk/API/2017/01/24/graph QL-vs-rest-overview/](https://philsturgeon.uk/api/2017/01/24/graphql-vs-rest-overview/)