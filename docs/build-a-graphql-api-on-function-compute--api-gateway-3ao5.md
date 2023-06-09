# 在函数计算+ API 网关上构建一个 GraphQL API

> 原文：<https://dev.to/asmsuechan/build-a-graphql-api-on-function-compute--api-gateway-3ao5>

# 什么是 GraphQL？

> GraphQL 是来自[https://graphql.org/](https://graphql.org/)的 API
> 的查询语言

我们通过向**的一个端点**抛出 **POST** 请求得到了一个结果。

下面的`curl`命令是获取消息的一个例子。

```
$ curl -H "Content-Type: application/json" -X POST -d '
{
  "query": "{ messages { name body } }"
}
' http://xxxxxxxxxxxxxxxxxx-cn-shanghai.alicloudapi.com/
 {"data":{"messages":[{"body":"Hello","name":"asmsuechan"},{"body":"World","name":"suechan"}]}} 
```

Enter fullscreen mode Exit fullscreen mode

更多信息，请谷歌 graphql。在这篇文章中我不去详细解释它。

# 最小代码

首先，我编写了最小的 GraphQL 代码，并让它在 Function Compute 上工作。

我期望查询和返回值的组合如下:

```
Query: "{ hello }"
Return Value: { data: { hello: "world" } } 
```

Enter fullscreen mode Exit fullscreen mode

下面这个样本来自[官方 GitHub](https://github.com/graphql/graphql-js) 。

```
// index.js
const { hook } = require('fc-helper');
const {
  graphql,
  GraphQLSchema,
  GraphQLObjectType,
  GraphQLString
} = require('graphql');

const schema = new GraphQLSchema({
  query: new GraphQLObjectType({
    name: 'RootQueryType',
    fields: {
      hello: {
        type: GraphQLString,
        resolve() {
          return 'world';
        }
      }
    }
  })
});

const query = '{ hello }';

module.exports.handler = (event, context, callback) => {
  graphql(schema, query).then((result) => {
    callback(null, { statusCode: 200, body: result });
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

很简单。只需在处理函数中执行`graphql()`。

# 更详细的 API

接下来，我改进了 API。我期望下面的查询和返回值的组合"

```
Query: "{ messages { name body } }"
Return value: {"data":{"messages":[{"body":"Hello","name":"asmsuechan"},{"body":"World","name":"suechan"}]}} 
```

Enter fullscreen mode Exit fullscreen mode

我定义了一个名为`message`的类型来获得复数`message`。

```
// index.js
const { hook } = require('fc-helper');
const {
  graphql,
  GraphQLSchema,
  GraphQLObjectType,
  GraphQLList,
  GraphQLString
} = require('graphql');
const atob = require('atob');

const messages = [
  {
    name: 'asmsuechan',
    body: 'Hello'
  },
  {
    name: 'suechan',
    body: 'World'
  }
];

const schema = new GraphQLSchema({
  query: new GraphQLObjectType({
    name: 'RootQueryType',
    fields: {
      messages: {
        type: GraphQLList(
          new GraphQLObjectType({
            name: 'message',
            fields: {
              name: { type: GraphQLString },
              body: { type: GraphQLString },
            },
          }),
        ),
        resolve() {
          return messages;
        }
      }
    }
  })
});

module.exports.handler = (event, context, callback) => {
  const request = JSON.parse(event.toString('utf8'))
  const query = JSON.parse(atob(request["body"]))["query"]
  graphql(schema, query).then((result) => {
    callback(null, { statusCode: 200, body: result });
  });
}; 
```

Enter fullscreen mode Exit fullscreen mode

我看到通过执行`curl`它工作得很好。

```
$ curl -H "Content-Type: application/json" -X POST -d '
{ "query": "{ messages { name body } }"}
'http://xxxxxxxxxxxxxxxxxx-cn-shanghai.alicloudapi.com/

{"data":{"messages":[{"body":"Hello","name":"asmsuechan"},{"body":"World","name":"suechan"}]}} 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

在函数 Compute + API Gateway 上用 GraphQL 代替 REST API 是很开心的，因为我们可以不用很多 API Gateway 就可以创建复杂的 API。

但是，作为生产环境运行速度太快。

这是我的文章库:

[https://github.com/asmsuechan/fc_graphql_api](https://github.com/asmsuechan/fc_graphql_api)