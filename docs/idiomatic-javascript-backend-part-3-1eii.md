# 惯用的 JavaScript 后端。第三部分

> 原文：<https://dev.to/k1r0s/idiomatic-javascript-backend-part-3-1eii>

大家好！系列的这一部分**惯用 JavaScript 后端**。

[1/3 部分](https://dev.to/k1r0s/idiomatic-javascript-backend-part-1-4g0b)
[2/3 部分](https://dev.to/k1r0s/idiomatic-javascript-backend-part-2-4lhe)

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

# 6。创建和管理会话

ritley 没有提供一个正统的方法来处理会议。

结束了！

不是开玩笑..是的，但是 ritley 做得最好的是为你提供工具，从一个方法到另一个方法、模块甚至项目，减少行为和需求。

现在，对于其余的[需求](https://github.com/k1r0s/ritley-tutorial/blob/master/REQUIREMENTS.md)，比如显示用户列表或更新用户，需要客户端允许使用其凭证创建会话。

因此，如果您没有用户，让我们创建几个用户！

`$ curl -d '{ "name": "Randell Kovalsky", "mail": "rk@foo.b", "pass": "asd123"}' localhost:8080/users`

使用前面的命令创建几个用户，比如说 3 或 4 个用户就足够了。

现在，还记得`src/resources/session.resource.js`吗？添加功能的时间:

```
import { AbstractResource } from "@ritley/core";
import SessionModel, { SessionInvalidCredentialsError } from "../models/session.model";

import {
  Default,
  MethodNotAllowed,
  Created,
  Throws,
  Unauthorized,
  BadRequest,
  Dependency,
  ReqTransformBodyAsync
} from "@ritley/decorators";

@Dependency("sessionModel", SessionModel)
export default class SessionResource extends AbstractResource {
  @Default(MethodNotAllowed) get() {}
  @Default(MethodNotAllowed) put() {}
  @Default(MethodNotAllowed) delete() {}

  constructor() {
    super("/sessions");
  }

  @Throws(SyntaxError, BadRequest)
  @Throws(SessionInvalidCredentialsError, Unauthorized)
  @Default(Created)
  @ReqTransformBodyAsync
  async post(req) {
    const body = await req.body;
    const payload = body.toJSON();
    const user = await this.sessionModel.validateCredentials(payload);
    return this.sessionModel.upsertSession(user);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们回顾一下需求和之前的片段:

*   post 方法应该通过用户凭据标识客户端来创建会话
*   validateCredentials 只是通过比较邮件和密码(加密)从数据库返回用户。
*   upsertSession 方法创建或更新会话。它总是接收一个用户并返回一个过期日期为+30 分钟的会话。
*   HTTP 405 必须明确拒绝 get、put 和 delete 方法
*   在每种情况下记录正确的响应:成功、有效负载上的错误 json、错误凭证、会话创建期间的错误。

您可能已经意识到我们在解析来自`src/resources/user.resource.js`的有效载荷时复制了代码。这只是暂时的，我们以后再解决。

我们还添加了一个名为`sessionModel`的依赖项，其中应该放置特定的非传输层逻辑，例如创建、更新、管理过期、凭证验证...我不打算粘贴，但你可以在这里查看[代码](https://github.com/k1r0s/ritley-tutorial/commit/038234f2b00851bf0509f2910b88b9e5a85bbd59#diff-cf4f3ffbea6a6329c14f3b4ae92264c5)。

现在，再次运行项目并对术语执行以下命令:

`$ curl -d '{ "mail": "<mail>", "pass": "<pass>"}' localhost:8080/sessions`

现在服务器会这样回答你:

`{"uid":"TjVNZy8yk","expiration":1535293179182,"userUid":"xqR16Gi7w"}`

嗯，这里的会议非常简单和非正统。成功的身份验证是一个 json，带有会话的`uid`、过期时间戳和作为`userUid`的相关用户的 uid。

根据[需求](https://github.com/k1r0s/ritley-tutorial/blob/master/REQUIREMENTS.md)的定义，为了使用新创建的会话，客户端应该在每个需要会话的调用上提供一个头`-H "x-session: <session_uid>"`。

例如，如果我们要列出应用程序上的所有注册用户，我们应该这样做:

`$ curl -H "x-session: TjVNZy8yk" localhost:8080/users`

现在我们必须修改我们的`src/resources/user.resource.js`来实现**得到**调用:

```
 import { AbstractResource } from "@ritley/core";
 import UserModel, { UserValidationError, UserMailInUseError } from "../models/user.model";
+import SessionModel, { SessionNotCreatedError, SessionExpiredError } from "../models/session.model"; 
 import {
   Dependency,
   ReqTransformBodyAsync,
   Default,
   Throws,
   BadRequest,
   Conflict,
   Created
+  Ok,
+  Unauthorized
 } from "@ritley/decorators"; 

 @Dependency("userModel", UserModel)
+@Dependency("sessionModel", SessionModel)
 export default class UserResource extends AbstractResource {
   constructor() {
     super("/users");
@@ -23,11 +26,21 @@ export default class UserResource extends AbstractResource {
   @Throws(UserMailInUseError, Conflict)
   @Default(Created)
   @ReqTransformBodyAsync
   async post(req) {
     const body = await req.body;
     const payload = body.toJSON();
     await this.userModel.validate(payload);
     await this.userModel.isUnique(payload);
     return this.userModel.create(payload);
   }
+
+  @Throws(SessionNotCreatedError, Unauthorized)
+  @Throws(SessionExpiredError, Unauthorized)
+  @Default(Ok)
+  async get(req) {
+    const uid = req.headers["x-session"];
+    const session = await this.sessionModel.sessionExists({ uid });
+    await this.sessionModel.revalidate(session);
+    return this.userModel.searchBy();
+  }
 } 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，我们刚刚添加了`get`方法。

现在，用户能够:

*   创建用户并定义凭据
*   使用凭据创建会话
*   列出使用会话的用户

尽管这里有一些问题:

*   `user.resource.js`包含处理来自不明确相关的 sessionModel 的调用的代码。尽管业务规则隐含了这种关系。系统工程师需要更含蓄地处理这种关系。因为现在涉及到混合不同的领域问题。

*   此外，会话管理也可能涉及与用户无关的错误。

*   下一个开发周期可能包括新的资源，比如说/城市或其他什么，我们是否要复制和粘贴代码来管理会话、有效载荷解析及其所有特定错误处理？(其实很多人都有)

* * *

# 7。关注点分离

如何解决复制问题？

老派的 OOP 程序员会尝试将共享行为分组到上层类中，然后将特定行为作为子类，但这很难扩展，因为有太多的强关系和复杂的代码库。

另一方面，FP 程序员将创建一个中间件管道(req，res，next ),不允许太多的构建抽象，这可能成为所谓的**中间件地狱**。从某种意义上来说，函数是独一无二的，一般来说，由于错误的封装和副作用问题，它不能很好地扩展。

我在这里不是要反对范例，我只是试图总体评估企业广泛使用的解决方案，如 Spring。NET 和 expressJS。

我的观点是，每种范式或技术都是好的，但像以前的框架那样用它来解决所有问题是没有意义的。

上面定义的两个范例都没有避免你显式地调用它们，从而在某个时候处理重复。完全消除重复是不可能的，但是，重复的问题不仅仅是你必须在你的代码库中粘贴大量的代码...是关于处理代码行，包括调用、赋值、发送参数...更多的代码，需要阅读、跟踪、测试、维护等等。通过阅读某个方法的代码，你如何判断所有放置的代码是否与主要关注点相关？例如:

```
increasePrice(amount) {
  ...requiredStuff1(amount)
  ...callAnotherService(this)
  ...etc
  this.price += amount;
  ...logThatPriceCorrectlyIncreases(this.price)
} 
```

Enter fullscreen mode Exit fullscreen mode

有多少次你读到一段代码，然后问:**这段代码到底在做什么？**，还没有吧？

让我们试着将共享行为分离成更具可移植性和声明性的东西。

回到我们的项目，让我们通过编写我认为是这个问题的解决方案来说明这一点:`src/resource/user.resource.js`和`src/resource/session.resource.js`都需要接收主体有效载荷，实际上是通过使用`@ReqTransformBodyAsync`来完成的，但是我们仍然需要将主体字符串解析为 JSON，并处理任何错误。

如果能解决整个问题就好了，对吗？**然后我们就可以在我们真正需要的时候，把我们出色的装饰器放在每一个需要那部分行为的方法上。**我们不想叫`app.use()`。

当然`@ritley/decorators`不知道我们在错误处理方面需要做什么，每个项目都是不同的，你可能需要找一个记录者，谁知道呢。它只提供请求缓冲连接，这是一个库能为你做的最基本的事情！

但是，它允许你**扩展装饰者**。让我们在`src/decorators/req-body-json.decorator.js` :
创建一个文件

```
import { beforeMethod } from "kaop-ts";

import {
  BadRequest,
  ReqTransformBodyAsync
} from "@ritley/decorators";

function parseRequestBody(meta) {
  const [req, res] = meta.args;
  req.body.then(body => {
    try {
      const payload = body.toJSON();
      meta.commit(payload);
    } catch (e) {
      BadRequest(res, e.message);
    }
  })
}

export default beforeMethod(
  ...ReqTransformBodyAsync.advices(),
  parseRequestBody
) 
```

Enter fullscreen mode Exit fullscreen mode

[![wat-decorators](img/3ce3585a120aa7263ae9333cdbee9aef.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Lhyd_BAN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/600/0%2Aju1bVBvFGchlK2YF)

嗯，首先我们需要了解`@ReqTransformBodyAsync`实际上做什么:

> 创建一个承诺，该承诺将在请求有效负载被完全接收时解析，并将其分配给`req.body`。

我们只是通过在第一个行为结束后添加一个行为来扩展这个装饰器。

因此，这个新的装饰器包含了`@ReqTransformBodyAsync`的行为和我们的自定义函数，该函数基本上试图将请求有效载荷解析到 json 中，如果有错误，它将提交一个 HTTP 400 错误请求，如果成功了**，它将执行使用一个额外的参数进行装饰的方法，该参数解析为 json 的有效载荷。**

如果这听起来像西班牙语，不要惊慌。我给你看这个:

```
 import { AbstractResource } from "@ritley/core";
 import SessionModel, { SessionInvalidCredentialsError } from "../models/session.model";
+import ParseReqBody from "../decorators/req-body-json.decorator"; 
 import {
   Default,
@@ -7,9 +8,7 @@ import {
   Created,
   Throws,
   Unauthorized,
   BadRequest,
   Dependency,
-  ReqTransformBodyAsync } from "@ritley/decorators";

@@ -25,14 +24,10 @@ export default class SessionResource extends AbstractResource {
     super(SessionResource.URI);
   }

-  @Throws(SyntaxError, BadRequest)
   @Throws(SessionInvalidCredentialsError, Unauthorized)
   @Default(Created)
-  @ReqTransformBodyAsync +  @ParseReqBody -  async post(req) { +  async post(req, res, payload) { -    const body = await req.body;
-    const payload = body.toJSON();
     const user = await this.sessionModel.validateCredentials(payload);
     return this.sessionModel.upsertSession(user);
   }
 } 
```

Enter fullscreen mode Exit fullscreen mode

这意味着`src/resources/user.resource.js`和`src/resources/session.resource.js`都将使用它，所以我们也可以摆脱`SyntaxError`检查。

现在，有效负载需求是在方法代码之外定义的，就像一个依赖项，但不是一个服务，**您正在注入一个行为**。这就像打了兴奋剂的中间件。

我想知道您是否可以在某个方法之上声明`@ResolveUploadedFile("/path/:filename")`,并在后台接收带有`multiplart/form-data`有效负载(一个文件)的请求，将文件保存到预定义的路径中，等等，然后执行您装饰的方法，就像什么都没发生一样？*是的，我们可以*。

让我们创建一个名为`@ValidateSession`的装饰器，从不相关的用户域
中清理我们的`src/resources/user.resource.js`

```
 import { AbstractResource } from "@ritley/core";
 import UserModel, { UserValidationError, UserMailInUseError } from "../models/user.model";
-import SessionModel, { SessionNotCreatedError, SessionExpiredError } from "../models/session.model"; +import ParseReqBody from "../decorators/req-body-json.decorator";
+import ValidateSession from "../decorators/validate-session.decorator"; 
 import {
   Dependency,
   Default,
   Throws,
   BadRequest,
   Conflict,
   Created,
   Ok,
-  ReqTransformBodyAsync,
-  Unauthorized
 } from "@ritley/decorators";

 @Dependency("userModel", UserModel)
-@Dependency("sessionModel", SessionModel)
 export default class UserResource extends AbstractResource {
   constructor() {
     super("/users");
   }

-  @Throws(SyntaxError, BadRequest)
   @Throws(UserValidationError, BadRequest)
   @Throws(UserMailInUseError, Conflict)
   @Default(Created)
-  @ReqTransformBodyAsync +  @ParseReqBody -  async post(req) { +  async post(req, res, payload) { -    const body = await req.body;
-    const payload = body.toJSON();
     await this.userModel.validate(payload);
     await this.userModel.isUnique(payload);
     return this.userModel.create(payload);
   }

-  @Throws(SessionNotCreatedError, Unauthorized)
-  @Throws(SessionExpiredError, Unauthorized)
   @Default(Ok)
+  @ValidateSession -  async get(req) { +  get(req) { -    const uid = req.headers["x-session"];
-    const session = await this.sessionModel.sessionExists({ uid });
-    await this.sessionModel.revalidate(session);
     return this.userModel.searchBy();
   }
 } 
```

Enter fullscreen mode Exit fullscreen mode

现在，还记得与会话管理相关的代码吗？我们把这个移到了一个合适的地方。我们已经创建了另一个文件夹:

```
src/
├── config
│   ├── database.config.js
│   └── lowdb.json
├── decorators
│   ├── req-body-json.decorator.js
│   └── validate-session.decorator.js
├── index.js
├── models
│   ├── session.model.js
│   └── user.model.js
├── resources
│   ├── session.resource.js
│   └── user.resource.js
└── services
    ├── database.service.js
    └── encrypt.service.js 
```

Enter fullscreen mode Exit fullscreen mode

概括地说，会话和用户`resources`都共享有效负载管理，所以我们创建了一个装饰器，封装了解析请求体所需的行为，然后我们定义了两个资源。我们做了类似的会话要求，因为下一个功能*编辑用户*将依赖它。

因此，正如你所看到的，ritley 提供了 OO 标准来处理基本的架构和关注点分离，但是用 FP 扩展来增强它，作为中间件装饰器，如果方法抛出错误，可以在方法之前和之后插入。所以这很棒。

* * *

# 8。每个人都关心的可扩展性

我需要向你解释如何编写可扩展的应用程序吗？如果你还在这里，你可能知道这方面的一些提示。不仅仅是你选择什么工具的问题，它当然会影响，但是大多数时候都是关于你做的决定…或者实际上是框架做的决定。

ritley 只在 http/传输层做出决定。这意味着你是唯一一个在其他问题上受到责备的人。很害怕，对吧？有些人可能会把它看作是对 T2 的救赎。

正如您在本教程的前几部分所看到的，简单易懂的架构使开发人员能够编写更好的代码。

现在我们需要添加最后一个特性，用户版。让我们在`src/resources/user.resource.js` :
上添加一个 PUT 方法处理程序

```
@Throws(UserInsufficientPermError, Forbidden)
@Default(Ok)
@ValidateSession
@ParseReqBody
@ReqTransformQuery
put(req, res, session, payload) {
  return this.userModel.putUser(req.query.uid, session.userUid, payload);
} 
```

Enter fullscreen mode Exit fullscreen mode

> 您是否注意到，如果用户没有有效的会话，请求有效负载将不会被解析？装饰顺序对:D 很重要，因为它在[自顶向下]之前。

这就是我们在 http 层要做的一切。

注意，我们在`src/models/user.model.js`上调用`putUser`。让我们看看这里有什么新的:

```
@@ -43,6 +43,14 @@ export default class UserModel {
     }
   }

+  isAllowedToEdit(requestedUserUid, currentUserUid) {
+    if(requestedUserUid === currentUserUid) {
+      return Promise.resolve();
+    } else {
+      return Promise.reject(new UserInsufficientPermError);
+    }
+  }
+
   update(uid, { mail, name }) {
     return this.database.update("users", { uid }, { mail, name });
   }
@@ -50,6 +58,10 @@ export default class UserModel {
   postUser(payload) {
     return this.validate(payload).then(() => this.isUnique(payload).then(() => this.create(payload)));
   }
+
+  putUser(requestedUserUid, currentUserUid, payload) {
+    return this.isAllowedToEdit(requestedUserUid, currentUserUid).then(() => this.update(requestedUserUid, payload));
+  }
 }

 export class UserValidationError extends Error {
@@ -63,3 +75,9 @@ export class UserMailInUseError extends Error {
     super("mail is already taken, try another one")
   }
 }
+
+export class UserInsufficientPermError extends Error {
+  constructor() {
+    super("you don't have permissions to perform this action")
+  }
+} 
```

Enter fullscreen mode Exit fullscreen mode

这意味着用户只能更新自己的个人资料。

让我们通过运行 curl 命令来尝试一下:

`$ curl -X PUT -H "x-session: <session_uid>" -d '{ "name": "Jean-Luc Godard"}' localhost:8080/users?uid=<target_user_uid>`

您得到的要么是一个详细的错误，要么是新更新的用户。

您可能注意到处理 querystring 参数和访问 req.query，我们已经在`put`方法上添加了`@ReqTransformQuery`。现在你可能会问:“*我必须为每一个案例添加一个装饰者吗？*”。如果你正在构建一个更复杂的应用程序，你可能需要定义一个更复杂的基类，而不是每个方法堆 7 个装饰器，你可以从`MyAbstractResource`扩展你的资源，而不是一般的 ritley 的`AbstractResource`。您可能需要在这个 LMAO 之上构建一个框架。

本章已完成。现在用户可以改名了！（...)

关于可扩展软件的一些提示:

像往常一样，试着横向扩展，避免类的两层以上的继承，记住你可以扩展最适合你的 decorators，等等。

例如，我们刚刚在编辑用户上添加的先前特性只涉及提交上的添加，我们没有改变先前的代码。这是可伸缩性的黄金法则。

任何与功能无关的代码块都应该易于扩展，但不适合修改。

你不应该试图抽象你的商业逻辑，因为你甚至不知道接下来会发生什么。作为一名程序员，你需要如实地表现业务逻辑，并用抽象和模块化的方法处理基础设施代码。这就是软件质量的定义。

例如，我们针对会话管理的特定 decorator 定义了一个封闭的设计，该设计很容易扩展，因为大多数业务逻辑都是在模型上定义的，decorator 本身只提供了将它附加到类中的胶水。

与可伸缩性相关的常见问题，如不相关领域的大量代码更改，是由于糟糕/封闭的设计*当您无法从这里访问该服务时，因为有另一个服务正在后台做令人讨厌的事情*..很可能是因为漏放责任。

你的工作总是保持对不同层面的关注。

* * *

# 9。结论

这是一个非常简单的概念。它是 9 个月前创建的，但被完全重写后作为 OSS 发布。基本概念是在传输层上提供通用模式，而不包装 nodejs 文档。事实上，它只通过映射你的类将请求发送给适当的处理程序，因此[它非常快](https://github.com/k1r0s/ritley/tree/master/benchmark)，经过良好的测试，并且容易掌握，因为如果你是一个临时的节点开发者，你不需要学习任何你可能知道的东西。

Library core 不到 80 行(在我写这篇文章的时候),并且可能会保持相当简单。尽管库扩展`@ritley/decorators`大约有 200 行。尽管与其他框架相比仍然很小。

恭喜同志。你喜欢这篇文章吗？请在下面告诉我你的想法，或者让我们在 [twitter](https://twitter.com/k1r0s) :)谢谢你❤

你喜欢关于图书馆的想法吗？[要不要投稿](https://github.com/k1r0s/ritley)？我总是乐于接受新想法！

[![ritley from metroid](img/6c96b2fa78b770f0acbb0e69af3afa58.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mCwlMtvz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7q8l002pngb479ra00l3.jpeg)