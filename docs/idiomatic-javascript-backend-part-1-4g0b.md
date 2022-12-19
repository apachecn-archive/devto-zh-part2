# 惯用的 JavaScript 后端。第一部分

> 原文：<https://dev.to/k1r0s/idiomatic-javascript-backend-part-1-4g0b>

大家好！系列的这一部分**惯用 JavaScript 后端**。

[2/3 部分](https://dev.to/k1r0s/idiomatic-javascript-backend-part-2-4lhe)
3/3 部分

### 重要信息

为了获得最佳体验，请复制此回购:[https://github.com/k1r0s/ritley-tutorial](https://github.com/k1r0s/ritley-tutorial)。它包含了 **git 标签**，你可以用它来遍历不同的提交，以便正确地遵循本教程:

```
$ git tag

1.preparing-the-env
2.connecting-a-persistance-layer
3.improving-project-structure
4.creating-entity-models
5.handling-errors
6.creating-and-managing-sessions
7.separation-of-concerns
8.everybody-concern-scalability 
```

Enter fullscreen mode Exit fullscreen mode

转到特定标签

```
$ git checkout 1.preparing-the-env 
```

Enter fullscreen mode Exit fullscreen mode

转到最近一次提交

```
$ git checkout master 
```

Enter fullscreen mode Exit fullscreen mode

查看文件夹`src`
上标签之间的差异

```
$ git diff 1.preparing-the-env 2.connecting-a-persistance-layer src 
```

Enter fullscreen mode Exit fullscreen mode

* * *

# 0。什么

大家好！今天的话题是关于用 NodeJS 构建一个 App。

我们要做什么？我们将构建一项服务，允许用户:

*   创建自己的个人资料
*   创建会话
*   列出其他用户
*   编辑自己的用户

还有…

我们要用`cURL`！

这与检查无关，但你可以点击这里查看这个应用程序应该满足的完整[要求](https://github.com/k1r0s/ritley-tutorial/blob/master/REQUIREMENTS.md)。

现在我要从零开始慢慢打造！

* * *

# 1。准备环境

让我们从[瑞特里](https://github.com/k1r0s/ritley)的《你好，世界》开始:

```
.
├── .babelrc
├── package.json
└── src
    └── index.js 
```

Enter fullscreen mode Exit fullscreen mode

在本教程中，我们将使用[巴别塔](https://babeljs.io/)。为了用 nodejs 做到这一点，我们需要`babel-node`来运行我们的应用程序。所以这是我们的 package.json:

```
{  "name":  "tutorial",  "version":  "1.0.0",  "description":  "",  "main":  "index.js",  "scripts":  {  "start":  "babel-node src"  },  "keywords":  [],  "author":  "",  "license":  "ISC",  "dependencies":  {  "@ritley/core":  "^0.3.3",  "@ritley/standalone-adapter":  "^0.2.0",  },  "devDependencies":  {  "@babel/core":  "^7.0.0-beta.55",  "@babel/node":  "^7.0.0-beta.55",  "@babel/plugin-proposal-class-properties":  "^7.0.0-beta.55",  "@babel/plugin-proposal-decorators":  "^7.0.0-beta.55",  "@babel/plugin-transform-async-to-generator":  "^7.0.0-rc.1",  "@babel/preset-env":  "^7.0.0-beta.55"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

为什么是`@ritley/core`和`@ritley/standalone-adapter`？:|

由于 **ritley** 非常小，许多特性被分离在不同的封装上。因为核心确实是必需的，所以独立适配器也是必需的，因为我们将在这里自己运行一个节点服务器。*如果你在 firebase 这样的无服务器环境中，没有它你也能继续运行*。

这将是我们的`.babelrc` :

```
{  "presets":  [["@babel/preset-env",  {  "targets":  {  "node":  "current"  }  }]],  "plugins":  [  ["@babel/plugin-proposal-decorators",  {  "legacy":  true  }],  ["@babel/plugin-proposal-class-properties",  {  "loose":  false  }],  ["@babel/plugin-transform-async-to-generator"]  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

还有我们的*你好世界* `src/index.js` :

```
import { setAdapter, AbstractResource } from "@ritley/core";
import Adapter from "@ritley/standalone-adapter";

setAdapter(Adapter, {
  "port": 8080
});

class SessionResource extends AbstractResource {
  constructor() {
    super("/sessions");
  }

  get(req, res) {
    res.statusCode = 200;
    res.end("Hello from sessions!");
  }
}

class UserResource extends AbstractResource {
  constructor() {
    super("/users");
  }

  get(req, res) {
    res.statusCode = 200;
    res.end("Hello from users!");
  }
}

new SessionResource;
new UserResource; 
```

Enter fullscreen mode Exit fullscreen mode

在前面的代码片段中，我们导入了`standalone-adapter`，并通过调用`setAdapter(<adapter> [, <options>])`将其绑定到核心。这将创建一个新的 [HttpServer](https://nodejs.org/api/http.html#http_class_http_server) 并将其绑定到任何`AbstractResource`子类。[你可以检查一下它是如何工作的](https://github.com/k1r0s/ritley#how-it-works)。

构建 ritley 应用程序时，你必须选择一个适配器。它定义如何将请求发送给资源。

**瑞特里**用[https://nodejs.org/api/http.html](https://nodejs.org/api/http.html)用`(req, res)` api 所以可能你已经很熟悉了。

注意，我们已经创建了两个相似的类，我们可以这样做:

```
import { setAdapter, AbstractResource } from "@ritley/core";
import Adapter from "@ritley/standalone-adapter";

setAdapter(Adapter, {
  "port": 8080
});

class DefaultResource extends AbstractResource {
  get(req, res) {
    res.statusCode = 200;
    res.end(`Hello from ${this.$uri}`);
  }
}

new DefaultResource("/sessions");
new DefaultResource("/users"); 
```

Enter fullscreen mode Exit fullscreen mode

无论如何，我们将保持它的分离，因为两种资源将很快开始分离。

现在您可以`$ npm start`然后运行一些 curl 命令来查看是否一切正常:

`$ curl localhost:8080/users`
T1】

这是我们的第一步！

* * *

# 2。连接持久层

我们需要某种持久层。我们将安装 [lowdb](https://github.com/typicode/lowdb) ，因为我们现在不需要太多的开销。

大家最喜欢的部分:*是时候安装新的依赖项了！*:

`$ npm install lowdb shortid`

然而，我们需要记住，任何依赖，无论我们附加到项目上的是什么，都应该是容易替换的。那就是我们将把 lowdb 包装成一个具有“CRUD alike”方法的接口，以保持事物的可扩展性。

让我们继续使用 **lowdb** :
来实现我们的`database.service.js`

```
import low from "lowdb";
import FileAsync from "lowdb/adapters/FileAsync";
import config from "./database.config";
import shortid from "shortid";

export default class DataService {
  onConnected = undefined

  constructor() {
    this.onConnected = low(new FileAsync(config.path, {
      defaultValue: config.defaults
    }))
  }

  create(entity, newAttributes) {
    return this.onConnected.then(database =>
      database
      .get(entity)
      .push({ uid: shortid.generate(), ...newAttributes })
      .last()
      .write()
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

目前我们只实现了`create`方法。现在好了。

```
.
└── src
    ├── database.config.js
    ├── database.service.js
    ├── index.js
    └── lowdb.json 
```

Enter fullscreen mode Exit fullscreen mode

我们的项目发展很快！我们也创建了`database.config.js`，它包含可能经常被替换的重要数据，所以我们把它放在这里:

```
export default {
  path: `${__dirname}/lowdb.json`,
  defaults: { sessions: [], users: [] }
}; 
```

Enter fullscreen mode Exit fullscreen mode

**如果你已经用过 lowdb** ，可以跳过这一段。基本上，您需要指定数据库物理位置的实际路径，因为它不需要像其他数据库引擎那样的服务。因此，lowdb 更简单，也更有趣，尽管功能不太强大，不应该用于构建企业项目。这就是为什么我将整个 lowdb 实现包装在一个公开 crud 方法的类上，因为它随时都可能被替换。

现在，我们已经更改了我们的`src/index.js`来正确地将数据库连接到控制器:

```
@@ -1,5 +1,6 @@
 import { setAdapter, AbstractResource } from "@ritley/core";
 import Adapter from "@ritley/standalone-adapter";
+import DataService from "./database.service"; 
 setAdapter(Adapter, {
   "port": 8080
@@ -17,15 +18,18 @@ class SessionResource extends AbstractResource {
 }

 class UserResource extends AbstractResource {
   constructor() {
     super("/users");
+    this.database = new DataService;
   }

-  get(req, res) {
-    res.statusCode = 200;
-    res.end("Hello from users!"); +  post(req, res) {
+    this.database.create("users", { name: "Jimmy Jazz" }).then(user => {
+      res.statusCode = 200;
+      res.end(JSON.stringify(user));
+    });
   }
 }

 new SessionResource;
 new UserResource; 
```

Enter fullscreen mode Exit fullscreen mode

我们还将 get 方法更改为一个 **post** 来模拟一个真实的创建请求案例。通过运行这个命令，我们得到了新创建的数据！

`$ curl -X POST localhost:8080/users`

检查`src/lowdb.json`以查看变化！

好了，我们刚刚连接了 **lowdb** 并运行了我们的第一次插入！

* * *

# 3。改善项目结构

我们需要组织一下我们的项目。

首先我们要这样排列我们的文件夹:

```
// forthcoming examples will only show src/ folder
src/
├── config
│   ├── database.config.js
│   └── lowdb.json
├── index.js
├── resources
│   ├── session.resource.js
│   └── user.resource.js
└── services
    └── database.service.js 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们从`src/index.js`中删除一点代码，这样就只有下面的代码:

```
import { setAdapter } from "@ritley/core";
import Adapter from "@ritley/standalone-adapter";

import SessionResource from "./resources/session.resource"
import UserResource from "./resources/user.resource"

setAdapter(Adapter, {
  "port": 8080
});

new SessionResource;
new UserResource; 
```

Enter fullscreen mode Exit fullscreen mode

所以基本上我们把我们的控制器(又名资源)转移到一个名为`resources`的独立文件夹中。

下一步是在`src/resources/user.resource.js`上设置依赖注入，以便能够注入我们的数据库服务的实例。

为此，我们将安装一个名为`@ritley/decorators`的扩展包:

`$ npm install @ritley/decorators`

然后，让我们对`src/services/database.service.js`做一些修改，以作为单例提供者导出:

```
 import config from "../config/database.config";
+import { Provider } from "@ritley/decorators"; 
+@Provider.singleton
 export default class DataService {
   onConnected = undefined 
```

Enter fullscreen mode Exit fullscreen mode

通过添加`@Provider.singleton`,我们将能够在每次执行提供者时只构造一个实例。这意味着所有将其声明为依赖关系的类都将共享同一个实例。

让我们把它加到`src/resources/user.resource.js` :

```
 import DataService from "../services/database.service";
+import { Dependency, ReqTransformBodySync } from "@ritley/decorators"; 
+@Dependency("database", DataService)
 export default class UserResource extends AbstractResource {
   constructor() {
     super("/users");
-    this.database = new DataService;
   }

+  @ReqTransformBodySync
   post(req, res) {
+    const payload = req.body.toJSON();
+    this.database.create("users", payload).then(user => { -    this.database.create("users", { name: "Jimmy Jazz" }).then(user => {
       res.statusCode = 200; 
```

Enter fullscreen mode Exit fullscreen mode

`@Dependency`执行 DataService(现在它是一个提供者),然后接收一个实例，并在类的本地构造函数被执行后，将它指定为一个命名属性**。**

因此，基本上我们消除了涉及控制器上服务实例化的复杂性。我猜你对这些做法很熟悉。

你可能注意到了，我们也移除了硬编码的有效载荷，并且在 post 方法上放置了`@ReqTransformBodySync`。

这个装饰器允许通过**延迟方法执行直到它被完全接收**来访问请求体或有效负载。就像[的主体解析器](https://www.npmjs.com/package/body-parser)做的那样，但是更加明确，因为你不需要费心去阅读方法内容来知道它需要有效载荷才能正常工作，而且它更具可插拔性，因为你可以在**的方法级别**进行配置。

现在尝试执行以下命令:

`$ curl -d '{ "name": "Pier Paolo Pasolini" }' localhost:8080/users`

> 如果提供了-d(有效负载),则假定为-X POST。

您应该收到一个 HTTP 200 OK 响应，表明您的新用户已经创建！检查数据库内容:)

伙计们，现在就到这里吧！在本系列的下一章，我们将看到 ritley 如何设法将模型与控制器链接起来，处理异常和管理会话。

[![ritley from metroid](../Images/6c96b2fa78b770f0acbb0e69af3afa58.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mCwlMtvz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7q8l002pngb479ra00l3.jpeg)