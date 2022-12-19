# Apollo 如何帮助我们快速启动 GraphQL 服务器

> 原文：<https://dev.to/iwilsonq/how-apollo-saves-us-effort-in-jump-starting-a-graphql-server-273g>

## Apollo 如何帮助我们快速启动 GraphQL 服务器

几年前，我用 Express 启动了我的第一个节点 HTTP 服务器。我这边只用了 5 行代码。

```
const express = require('express')
const app = express()

app.get('/', function(req, res) {
    res.send({ hello: 'there' })
})

app.listen(3000, () => 'Listening at http://localhost:3000') 
```

Enter fullscreen mode Exit fullscreen mode

这大大减少了构建服务器端应用程序的必要工作，尤其是考虑到我们可以使用我们熟悉的 JavaScript！

我必须说，一两年前，建立一个 GraphQL 服务器并不容易。需要决定是用脸书的火炬还是阿波罗来建造它。对于如何构建和使用 GraphQL 服务器，两个框架都有自己的观点。

一开始，从 Apollo 开始比 Relay 简单一点，但是仍然需要大量的配置。为了编写相当基本的查询，需要导出和使用许多 GraphQL 内部机制。

现在不同了——阿波罗生态系统从几年前开始有了巨大的发展。设置一个服务器变得如此简单，我们可以用上面的例子中的几行代码来完成

```
import express from 'express'
import { ApolloServer } from 'apollo-server-express'
import { resolvers, typeDefs } from './schema'

const PORT = process.env.PORT || 3500
const app = express()

const server = new ApolloServer({
  typeDefs,
  resolvers,
  playground: true
})

server.applyMiddleware({ app })

app.listen(PORT, () =>
  console.log(`Listening at http://localhost:${PORT}/graphql`)
) 
```

Enter fullscreen mode Exit fullscreen mode

我们只需在模式中定义类型定义和解析器函数，就可以开始了。这类似于为我们想要支持的每个操作设置路由处理程序，比如`GET /users/:id`或`POST /articles`。

下面是一些类型定义的例子:

```
export const typeDefs = gql`
  type User {
    id: ID
    name: String
    age: Int
    email: String
    friends: [User]
  }

  type Query {
    users: [User]
  }

  input CreateUserInput {
    id: Int
    name: String
    age: Int
    email: String
    friends: [Int]
  }

  type Mutation {
    createUser(input: CreateUserInput!): User
    updateUser(id: Int!, input: CreateUserInput!): User
    deleteUser(id: Int!): User
  }
` 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们没有定义 CRUD 端点来收集和操纵我们的用户，而是声明数据的形状和它们之间的关系。

为了接收查询或变异并调用数据库，我们定义了访问数据模型的解析器，如下:

```
export const resolvers = {
  Query: {
    users(source, args, context) {
      return userModel.list()
    }
  },
  User: {
    friends(source, args, context) {
      if (!source.friends || !source.friends.length) {
        return
      }

      return Promise.all(source.friends.map(({ id }) => userModel.find(id)))
    }
  },
  Mutation: {
    createUser(source, args, context) {
      return userModel.create(args.input)
    },
    updateUser(source, args, context) {
      return userModel.update(args.id, args.input)
    },
    deleteUser(source, args, context) {
      return userModel.delete(args.id)
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

该模型处理数据库查询或第三方 api 调用。这样，我们可以让解析器不知道数据来自哪里。

## 阿波罗服务器，内部

甚至在几个月前，在你真正能够做任何事情之前，有更多的包要处理，更多的配置要研究。

定义解析器需要花费更多的精力来设置内部 GraphQL 类型。

定义和修补模式也需要开发人员更多的创造力。Apollo 基本上查看了所有这些配置“繁忙的工作”,并将其抽象成一个更容易开始的包——而没有牺牲您可能仍然想要的配置。

在这里，我们简单地初始化了一个服务器实例，并将我们的 express server 作为一个中间件来应用。

```
server.applyMiddleware({ app }) 
```

Enter fullscreen mode Exit fullscreen mode

当我们将 Express 实例传递给 Apollo 服务器时会发生什么？

通常，当我们设置一个快速服务器时，我们需要安装

*   读取 JSON 有效负载的主体解析器
*   CORS 应对跨产地资源共享
*   multer 或其他一些多部分中间件来读取文件上传

它在我们的服务器中变成了一堆`app.use(someMiddleware)`命令。默认情况下，Apollo Server 提供了所有这些功能，这就是为什么我们几乎不需要编写任何额外的代码或安装更多的依赖项。

# 即将到来

我写了另一篇关于用 Apollo 设置 GraphQL 服务器的文章。希望它会很快下降，但示例回购[在这里]的情况下，你想检查出伴随它的源代码。

与 Apollo 和 GraphQL 一起工作确实改善了我的开发体验，使我更有效率，希望你也会发现这一点！

如果你喜欢这篇文章，并希望在未来看到更多，请在评论中告诉我，并在 [Twitter](https://twitter.com/iwilsonq) 和 [Medium](https://medium.com/@iwilsonq) 上给我一个关注！