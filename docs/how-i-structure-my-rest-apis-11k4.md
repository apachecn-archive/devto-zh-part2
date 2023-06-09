# 我如何构建我的 REST APIs

> 原文：<https://dev.to/larswaechter/how-i-structure-my-rest-apis-11k4>

这篇文章最初发表在我的博客上。

* * *

当我开始使用 [Node.js](https://nodejs.org/en/) 在服务器端构建 REST APIs 时，我反复纠结于同一个问题:

> 文件夹结构应该是什么样子？

显然，这个问题没有完美或 100%正确的答案，但在阅读了一些关于这个主题的文章后，我发现一个文件夹结构和架构非常适合我的需求。所以今天我想向您展示我是如何构建和组织 Node.js REST APIs 的。

我还发布了一个 GitHub [资源库](https://github.com/larswaechter/expressjs-api)，其中包括一个示例应用程序，您可以将它用作自己项目的模板。

值得一提的是，我使用 [Express.js](https://expressjs.com/de/) 作为 web 框架，使用 [TypeORM](https://typeorm.io/#/) 作为 ORM。将这种文件夹结构应用于其他框架应该不难。

该架构主要是基于组件的 T2，这使得只请求我们真正需要的数据变得更加容易。例如，我们有一个包含所有用户信息的`User`组件。

让我们从`root`目录开始。

### 目录:根目录

```
expressjs-api
└───db
│
└───dist
│
└───logs
│
└───node_modules
│
└───src
│
│   README.md
│   ... 
```

Enter fullscreen mode Exit fullscreen mode

这个结构没什么特别的，对你来说不应该是新的。这实际上是一个基本的 Node.js 设置。这里有趣的部分是这篇文章所涉及的`src`文件夹的内容。

我们这里有什么？

```
expressjs-api
└───src
   │
   └───api
   │   │
   │   └───components
   │   │
   │   └───middleware
   │   │
   │   │   routes.ts
   │   │   server.ts
   │
   └───config
   │
   └───services
   │
   └───test
   |
   │   app.ts 
```

Enter fullscreen mode Exit fullscreen mode

从这里开始，我们将自上而下地浏览文件/目录，我将解释每一个。让我们从`api`目录开始，这是应用程序最重要的部分。

### 目录:src/api/components

```
expressjs-api
└───src
    │
    └───api
        │
        └───components
            │
            └───article
            │
            └───auth
            │
            └───country
            │
            └───user
            │   helper.ts
            │   index.ts 
```

Enter fullscreen mode Exit fullscreen mode

这里我们有了基于组件的节点 API 的核心。每个组件都有自己的**路线**、**控制器**、**模型**、**仓库**、**策略**、**测试**和**模板**。

让我们进入`User`组件看一看。

### 目录:src/api/components/user

```
expressjs-api
└───src
    │
    └───api
        │
        └───components
            │
            └───user
                │
                └───services
                |   │   mail.ts
                └───templates
                |   │   confirmation.html
                |   |   invitation.html
                │   controller.ts
                │   model.ts
                │   policy.json
                │   repository.ts
                │   routes.ts
                │   user.spec.ts 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，一个组件由我刚才提到的文件组成。它们中的大多数代表一个被导出的**单个类**。当然，您可以在这里添加更多特定于组件的内容。

由于我有多个组件，并且它们的类大部分时间都有相同的结构，所以我也创建了在类中实现的接口。这有助于我保持组件的结构清晰。

此外，我们在这里有`services`目录，其中包括本地组件服务，例如`mail`。全球服务部的那些白人。

`templates`目录包含给定组件的邮件 HTML 模板。对于动态渲染 HTML 代码，我强烈推荐 [ejs](https://www.npmjs.com/package/ejs) 。

#### 控制器. ts

控制器类处理传入的请求，并将响应数据发送回客户端。它使用`repository`类与数据库交互。请求验证在几个步骤之前通过中间件发生

一个简短的例子:

```
export class UserController {
  private readonly repo: UserRepository = new UserRepository()

  async readUser(
    req: Request,
    res: Response,
    next: NextFunction
  ): Promise<Response | void> {
    try {
      const { userID } = req.params

      const user: User | undefined = await this.repo.read({
        where: {
          id: +userID,
        },
      })

      return res.json(user)
    } catch (err) {
      return next(err)
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

#### model.ts

该模型表示其组件的数据库模型。在我的例子中，它是一个[类型的 ORM](https://typeorm.io/) 类。它主要由`repository`类使用。

#### policy.json

这个 json 文件包括每个用户角色对给定组件的访问权限。它是基于[访问控制列表](https://de.wikipedia.org/wiki/Access_Control_List)的系统的一部分。

示例:

```
{  "Admin":  [{  "resources":  "user",  "permissions":  "*"  }],  "User":  [{  "resources":  "user",  "permissions":  ["read"]  }]  } 
```

Enter fullscreen mode Exit fullscreen mode

#### repository.ts

repository 类就像数据库的包装器。在这里，我们向数据库读写数据。此外，我们可以实现缓存。

您可以将`repository`类导入到任何其他文件中，并从数据库中查询该组件的数据。此外，它还能防止我们编写多余的代码，因为我们不必多次重写 SQL 语句。

因为大多数组件库需要相同的基本访问方法，如`readAll`、`read`、`save`和`delete`，所以我使用了一个包含所有这些方法的通用父类。这节省了大量代码。

实现见 [AbsRepository](https://github.com/larswaechter/expressjs-api/blob/main/src/api/components/helper.ts) 。

#### [T1】routes . ts](#routests)

在这里，我们为相应的组件定义 API **端点**，并为它们分配`controller`方法。此外，我们可以添加更多的东西，如

*   授权(如 JWT)
*   权限检查(ACL)
*   请求正文验证
*   特定于组件的中间件。

一个简短的例子:

```
class UserRoutes implements IComponentRoutes<UserController> {
  readonly name: string = "user"
  readonly controller: UserController = new UserController()
  readonly router: Router = Router()
  authSerivce: AuthService

  constructor(defaultStrategy?: PassportStrategy) {
    this.authSerivce = new AuthService(defaultStrategy)
    this.initRoutes()
  }

  initRoutes(): void {
    this.router.get(
      "/:userID",
      this.authSerivce.isAuthorized(),
      this.authSerivce.hasPermission(this.name, "read"),
      param("userID").isNumeric(),
      this.authSerivce.validateRequest,
      this.controller.readUser
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 用户规范 ts

这是用于测试组件及其端点的测试文件。你可以在这里阅读更多关于测试这个架构的信息[。](https://dev.to/blog/nodejs-rest-api-testing/)

### 目录:src/API/中间件/

```
expressjs-api
└───src
    │
    └───api
        │
        └───middleware
            │   compression.ts
            │   loggin.ts 
```

Enter fullscreen mode Exit fullscreen mode

这个文件夹包括所有 API 的全局中间件，如**压缩**，请求**日志**等。

### 文件:src/api/routes.ts

```
expressjs-api
└───src
    │
    └───api
        │   routes.ts 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们注册了所有组件和中间件路由。这些是后来在`server`类中使用的。

### 文件:src/api/server.ts

```
expressjs-api
└───src
    │
    └───api
       │   server.ts 
```

Enter fullscreen mode Exit fullscreen mode

这里我们声明了 Express.js 服务器所需的一切:

*   导入中间件
*   导入路线
*   错误处理

稍后，我们也可以为单元测试导入`server`类。

### [T1】目录:src/config](#directory-srcconfig)

```
expressjs-api
└───src
    │
    └───config
       │   globals.ts
       │   logger.ts
       │   permissions.ts 
```

Enter fullscreen mode Exit fullscreen mode

这个目录包括 API 的配置文件。例如，这可能是:

*   全局变量
*   记录器配置
*   [ACL](https://www.npmjs.com/package/acl) 权限
*   SMTP config

请随意将任何与**配置相关的**文件放在这里。

### 目录:src/services/

这个目录包含了我们可能需要的全局服务，例如授权，发送**邮件**，**缓存**，或者**助手**方法。

```
expressjs-api
└───src
    │
    └───services
        │   auth.ts
        │   helper.ts
        │   mail.ts
        |   redis.ts 
```

Enter fullscreen mode Exit fullscreen mode

#### 授权

在这里，我们设置应用程序的 passport 策略，并定义授权方法。

#### helper.ts

助手类包含用于**散列**、**uuid**等等的助手方法。

#### mail.ts

该服务用于发送**邮件**和渲染组件模板。再次推荐 [ejs](https://www.npmjs.com/package/ejs) 的`renderFile`功能。

### 目录:src/test/

这个目录包括一个用于运行组件测试的测试工厂。你可以在这里了解更多信息。

### 文件:src/app.ts

这是我们应用程序的启动文件。它初始化数据库连接并启动 express 服务器。

```
expressjs-api
└───src
    │   app.ts 
```

Enter fullscreen mode Exit fullscreen mode

### 都在一起

最后但同样重要的是，项目结构的完整概述:

```
expressjs-api
└───src
    │
    └───config
    │   │   globals.ts
    │   │   logger.ts
    │   │   permissions.ts
    │
    └───api
    │   │
    │   └───components
    │   │   │
    │   │   └───article
    │   │   │
    │   │   └───user
    |   │   │   │
    |   │   │   └───templates
    |   │   │   |   │   confirmation.html
    |   │   │   |   │   invitation.html
    │   │   |   │   controller.ts
    │   │   |   │   model.ts
    │   │   |   │   policy.json
    │   │   |   │   repository.ts
    │   │   |   │   routes.ts
    │   │   |   │   user.spec.ts
    │   │
    │   └───middleware
    │   │   │   compression.ts
    │   │   │   logging.ts
    │   │
    │   │   routes.ts
    │   │   server.ts
    │
    └───services
    │
    └───test
    |
    │   app.ts 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！希望这对不知道如何构造自己的 Node.js 应用程序或者不知道如何开始的人有一点帮助。我认为还有很多事情你可以做得更好或者更有效率。

如果你对编写 Node.js REST APIs 的单元测试感兴趣，可以看看这篇覆盖相同架构的文章。

我还发布了一个 GitHub 资源库，其中包括一个示例应用程序。[看一看](https://github.com/larswaechter/expressjs-api)。