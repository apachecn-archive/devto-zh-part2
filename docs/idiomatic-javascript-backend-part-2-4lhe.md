# 惯用的 JavaScript 后端。第二部分

> 原文：<https://dev.to/k1r0s/idiomatic-javascript-backend-part-2-4lhe>

大家好！系列的这一部分**惯用 JavaScript 后端**。

[1/3 部分](https://dev.to/k1r0s/idiomatic-javascript-backend-part-1-4g0b)
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

# 4。创建实体模型

Ritley 并没有告诉你如何构建实体模型，这不是我们关心的，所以我会尽量简短。

模型封装了与领域相关的所有逻辑。例如创建用户、加密密码、验证字段等。而资源将这个逻辑翻译成 HTTP 层。

我们的第一款车型将定位于`src/models/user.model.js` :

```
import DataService from "../services/database.service";
import EncryptService from "../services/encrypt.service";
import { Provider, Dependency } from "@ritley/decorators";

@Provider.factory
@Dependency("database", DataService)
@Dependency("encrypt", EncryptService)
export default class UserModel {

  static userPublicPredicate = collection => collection.map(({ pass, ...user }) => ({
    ...user
  }))

  validate(payload) {
    const requiredProps = ["name", "pass", "mail"];
    const props = Object.keys(payload);
    if(requiredProps.every(prop => props.includes(prop))) {
      return Promise.resolve();
    } else {
      return Promise.reject();
    }
  }

  create(payload) {
    const pass = this.encrypt.encode(payload.pass);
    return this.database.create("users", { ...payload, pass });
  }

  isUnique({ mail }) {
    return new Promise((resolve, reject) =>
      this.database.exists("users", { mail }).then(reject, resolve));
  }

  searchBy(predicate) {
    return this.readUsers(predicate).then(UserModel.userPublicPredicate);
  }

  readUsers(predicate) {
    if(predicate) {
      return this.database.filter("users", predicate);
    } else {
      return this.database.read("users");
    }
  }

  update(uid, { mail, name }) {
    return this.database.update("users", { uid }, { mail, name });
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们刚刚实现了许多稍后会用到的方法，现在我们将使用`validate`、`isUnique`和`create`来满足[关于用户创建的需求](https://github.com/k1r0s/ritley-tutorial/blob/master/REQUIREMENTS.md)。

注意，我们已经包含了一个新的[密码加密](https://www.npmjs.com/package/cpass)包。

同样，所有非主题包都只是占位符，你可以使用任何其他你喜欢的:)

我们跑:`$ npm install cpass`

现在让我们来看看`src/resources/user.resource.js` :

```
 import { AbstractResource } from "@ritley/core";
-import DataService from "../services/database.service"; +import UserModel from "../models/user.model"; 
-import { Dependency, ReqTransformBodySync } from "@ritley/decorators";
+import { Dependency, ReqTransformBodyAsync } from "@ritley/decorators"; 
-@Dependency("database", DataService)
+@Dependency("userModel", UserModel)
 export default class UserResource extends AbstractResource {
   constructor() {
     super("/users");
   }

-  @ReqTransformBodySync
-  post(req, res) {
-    const payload = req.body.toJSON();
-    this.database.create("users", payload).then(user => {
-      res.statusCode = 200;
-      res.end(JSON.stringify(user));
-    }); +  @ReqTransformBodyAsync
+  async post(req, res) {
+    const body = await req.body;
+    const payload = body.toJSON();
+    await this.userModel.validate(payload);
+    await this.userModel.isUnique(payload);
+    const user = await this.userModel.create(payload);
+    res.statusCode = 200;
+    res.end(JSON.stringify(user));
   }
 } 
```

Enter fullscreen mode Exit fullscreen mode

正如我之前说过的，使用 async/await 特性将我们的`post`方法转换为一个承诺，所以我们将使用 **@ReqTransformBodyAsync** 而不是之前的@ReqTransformBodySync。第一个是基于*承诺的*，所以将它与 async/await 代码一起使用是有意义的，比如前面的代码片段。

当然，我们已经从资源中移除了`this.database`调用和数据服务。您不想弄乱 http 层上的持久层；)

我们的服务现在满足了用户创建的要求，但是我们这里缺少异常处理。如果 JSON 格式不正确，有效载荷不包含必填字段，假设电子邮件被占用或其他情况，我们将持有一个未处理的拒绝，或者可能是一个异常将终止我们的应用程序😰

让我们看看接下来会发生什么！

* * *

# 5。处理异常

那么，当出现错误时，如何在任何时候做出适当的响应呢？

嗯，首先我们需要看看那里:

```
const body = await req.body;
const payload = body.toJSON();
await this.userModel.validate(payload);
await this.userModel.isUnique(payload);
const user = await this.userModel.create(payload);
res.statusCode = 200;
res.end(JSON.stringify(user)); 
```

Enter fullscreen mode Exit fullscreen mode

所有的错误都源于那里或随后的调用，应该在这里(这里附近)处理，因为它涉及到向客户发送反馈。

但是那是相当困难的，并且涉及许多你可能认为的侵扰。

为了更好地理解在 nodejs 中处理嵌套承诺拒绝意味着什么，我推荐[这篇关于承诺拒绝的文章](http://thecodebarbarian.com/unhandled-promise-rejections-in-node.js.html)，或者至少放在桌面上。

用`try ... catch`包装每一个具体案例可能是一场噩梦。让我们从将每个任务分成处理单个操作的新方法开始，例如有效载荷解析:

```
parseBody(req, res) {
  try {
    return req.body.toJSON();
  } catch (e) {
    res.statusCode = 400; // Bad Request
    res.end("payload isn't well formed");
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

当然这是可行的！让我们看看它是什么样子的:

```
import { AbstractResource } from "@ritley/core";
import UserModel from "../models/user.model";

import { Dependency, ReqTransformBodyAsync } from "@ritley/decorators";

@Dependency("userModel", UserModel)
export default class UserResource extends AbstractResource {
  constructor(_database) {
    super("/users");
  }

  @ReqTransformBodyAsync
  async post(req, res) {
    const body = await req.body;
    const payload = this.parseBody(body, res);
    await this.validate(payload, res);
    await this.isUnique(payload, res);
    const user = await this.create(payload, res);
    res.statusCode = 200;
    res.end(JSON.stringify(user));
  }

  parseBody(body, res) {
    try {
      return body.toJSON();
    } catch (e) {
      res.statusCode = 400;
      res.end("payload isn't well formed");
    }
  }

  validate(payload, res) {
    return this.userModel.validate(payload).catch(() => {
      res.statusCode = 400;
      res.end("missing fields, required: [name, mail, pass]");
    })
  }

  isUnique(payload, res) {
    return this.userModel.isUnique(payload).catch(() => {
      res.statusCode = 409;
      res.end("mail is already taken, try another one");
    })
  }

  create(payload, res) {
    return this.userModel.create(payload).catch(() => {
      res.statusCode = 500;
      res.end("there was an error creating your user, try again");
    })
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

mmh！那是巨大的，仅仅为了正确地捕捉异常而扩展我们的代码有意义吗？良好的...

即使我们正在处理每一个可能涉及拒绝或异常的任务中的错误，我们也会遇到`UnhandledPromiseRejectionWarning`,因为异步生成器将整个方法包装成一个承诺，但是我们不能自己处理`post`,因为它被库调用，它不应该自己处理。

为了避免这种情况，我们可以创建一个新的异步方法，通过 post 调用 get，这样我们就可以处理来自外部的异步调用，这是一种变通方法:

```
post(req, res) {
  this.handledPost(req, res).catch(() => console.log('rejection from inside'));
}

async handledPost() {
  ...lots of awaits that may be rejected but locally handled
} 
```

Enter fullscreen mode Exit fullscreen mode

另一个优雅的解决方案是使用更多的抽象，因为我们多次重复相同的模式。`@ritley/decorators`提供了一些为了让我们的生活更轻松，例如:

```
 import {
+  Default,
+  Catch,
   InternalServerError,
   BadRequest,
   Conflict,
   Created
 } from "@ritley/decorators"; 
```

Enter fullscreen mode Exit fullscreen mode

而且大概也没有太多需要解释的:

```
import { AbstractResource } from "@ritley/core";
import DataService from "../services/database.service";
import UserModel from "../models/user.model";

import {
  Dependency,
  ReqTransformBodyAsync,
  Default,
  Catch,
  InternalServerError,
  BadRequest,
  Conflict,
  Created
} from "@ritley/decorators";

@Dependency("userModel", UserModel)
export default class UserResource extends AbstractResource {
  constructor(_database) {
    super("/users");
  }

  @Default(Created)
  @ReqTransformBodyAsync
  async post(req, res) {
    const payload = await this.parseBody(req, res);
    await this.validate(payload, res);
    await this.isUnique(payload, res);
    return await this.create(payload, res);
  }

  @Catch(BadRequest, "payload isn't well formed")
  parseBody(req) {
    return req.body.then(body => body.toJSON());
  }

  @Catch(BadRequest, "missing fields, required: [name, mail, pass]")
  validate(payload) {
    return this.userModel.validate(payload);
  }

  @Catch(Conflict, "mail is already taken, try another one")
  isUnique(payload) {
    return this.userModel.isUnique(payload);
  }

  @Catch(InternalServerError, "there was an error creating your user, try again")
  create(payload) {
    return this.userModel.create(payload);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，这些抽象减少了一点我们的代码库，提高了可读性。

您可能想知道`@Catch(responseFn, content)`在方法上寻找**同步异常**，但是也检查返回值是否是一个承诺，如果是，向它添加一个`catch()`回调。要么处理同步错误，要么拒绝承诺，并且用我们的`res <Response>`对象调用`responseFn`。

所以:`BadRequest, Conflict, InternalServerError, Created`...是由`@ritley/decorators`导出的函数，它接收一个`res <Response>`对象，并将正确的消息解析给客户端。因此，通过调用`BadRequest(res, "wrong!")`，客户机将收到一个带有“错误！”的 HTTP 400 作为一个响应机构。

另一方面，`@Default(responseFn)`做同样的事情，但是使用`then()`检查承诺解析。它还附加了一个`catch()`来防止可能的未处理的拒绝，但是它会用 **HTTP 500** 解决这样的情况，因为那个错误确实没有得到妥善处理。

换句话说， **Default** 告诉我们如果一切顺利将会发生什么，而 **Catch** 用错误消息包装合理的调用，就像检查点一样。

但是还有更多:

```
 import { AbstractResource } from "@ritley/core";
-import UserModel from "../models/user.model"; +import UserModel, { UserValidationError, UserMailInUseError } from "../models/user.model"; 
-import { Dependency, ReqTransformBodyAsync } from "@ritley/decorators";
+import {
+  Dependency,
+  ReqTransformBodyAsync,
+  Default,
+  Throws,
+  InternalServerError,
+  BadRequest,
+  Conflict,
+  Created
+} from "@ritley/decorators"; 
 @Dependency("userModel", UserModel)
 export default class UserResource extends AbstractResource {
@@ -9,14 +18,16 @@ export default class UserResource extends AbstractResource {
     super("/users");
   }

+  @Throws(SyntaxError, BadRequest)
+  @Throws(UserValidationError, BadRequest)
+  @Throws(UserMailInUseError, Conflict)
+  @Default(Created)
   @ReqTransformBodyAsync
   async post(req, res) {
     const body = await req.body;
     const payload = body.toJSON();
     await this.userModel.validate(payload);
     await this.userModel.isUnique(payload);
-    const user = await this.userModel.create(payload);
-    res.statusCode = 200;
-    res.end(JSON.stringify(user)); +    return this.userModel.create(payload);
   }
 } 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用`@Throws` decorator 明确地告诉我们将会遇到哪种类型的异常，以便触发对客户端的特定响应。令人震惊，对吧？

看看我们是如何从我们的模型层`src/models/user.model.js` :
导出定制错误的

```
 if(requiredProps.every(prop => props.includes(prop))) {
       return Promise.resolve();
     } else {
-      return Promise.reject(); +      throw new UserValidationError
     }
   }

@@ -29,7 +28,7 @@ export default class UserModel {

   isUnique({ mail }) {
     return new Promise((resolve, reject) =>
-      this.database.exists("users", { mail }).then(reject, resolve)); +      this.database.exists("users", { mail }).then(() => reject(new UserMailInUseError), resolve));
   }

   searchBy(predicate) {
@@ -48,3 +47,15 @@ export default class UserModel {
     return this.database.update("users", { uid }, { mail, name });
   }
 }
+
+export class UserValidationError extends Error {
+  constructor() {
+    super("missing fields, required: [name, mail, pass]")
+  }
+}
+
+export class UserMailInUseError extends Error {
+  constructor() {
+    super("mail is already taken, try another one")
+  }
+} 
```

Enter fullscreen mode Exit fullscreen mode

所以`@Throws(errorType, responseFn)`超越了。虽然`@Catch`会处理任何异常，不管错误类型如何，`@Throws`只是提供了更具体的方法来处理 http 层。

这是`src/resources/user.resource.js` :
的最终造型

```
import { AbstractResource } from "@ritley/core";
import UserModel, { UserValidationError, UserMailInUseError } from "../models/user.model";

import {
  Dependency,
  ReqTransformBodyAsync,
  Default,
  Throws,
  InternalServerError,
  BadRequest,
  Conflict,
  Created
} from "@ritley/decorators";

@Dependency("userModel", UserModel)
export default class UserResource extends AbstractResource {
  constructor() {
    super("/users");
  }

  @Throws(SyntaxError, BadRequest)
  @Throws(UserValidationError, BadRequest)
  @Throws(UserMailInUseError, Conflict)
  @Default(Created)
  @ReqTransformBodyAsync
  async post(req, res) {
    const body = await req.body;
    const payload = body.toJSON();
    await this.userModel.validate(payload);
    await this.userModel.isUnique(payload);
    return this.userModel.create(payload);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

简单回顾一下。是否使用`@Throws`或`@Catch`由你决定，尽管`@Catch`可以被认为是一个`@Throws(Error, fn)`别名，因为它将在任何异常时被执行。但是`@Throws`更具确定性，因为您可以将 HTTP 响应与特定类型的错误或成功联系起来。

基本上所有的*框架逻辑*都在 http 层。不管提供者是谁，模型都是完全独立的。

定义了 http 触发器，这些触发器将调用模型上的特定操作，无论成功还是失败，都将在同一个触发器上使用一组声明性的表达式进行处理。这基本上允许在后端进行非侵入式和声明式开发。

伙计们，现在就到这里吧！系列的下一章将是关于处理会话、关注点分离以及保持 ritley 的可伸缩性。Cya！

[![ritley from metroid](img/6c96b2fa78b770f0acbb0e69af3afa58.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mCwlMtvz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7q8l002pngb479ra00l3.jpeg)