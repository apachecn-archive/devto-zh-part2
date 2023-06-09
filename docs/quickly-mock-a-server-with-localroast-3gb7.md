# 用 Localroast 快速模拟服务器

> 原文：<https://dev.to/caalberts/quickly-mock-a-server-with-localroast-3gb7>

针对 API 进行开发时通常面临的一个挑战是需要一个 API 的模拟，该模拟可用于测试正在开发的 API 客户端。为了解决这个问题而不必设置集成环境，我创建了一个工具来快速模拟外部 API。

介绍[local broast](https://github.com/caalberts/localroast)，这是一个命令行程序，可以快速启动 web 服务器，为您需要的端点提供模拟响应。它所需要的只是一个包含模拟列表的 JSON 文件。

让我们看一个例子，基于在 [Swagger 编辑器](https://editor.swagger.io/)中可用的 Swagger Petstore API。让我们从这个例子中选取几个 API 来研究:

*   `GET /pet/{petId}`
*   `POST /pet`
*   `DELETE /pet/{petId}`
*   `GET /user/login`

首先通过`brew install caalberts/tap/localroast`安装`localroast`。

现在创建一个包含模拟的文件，我们称之为`pets.json`

```
[  {  "method":  "GET",  "path":  "/pet/:pet_id",  "status":  200,  "response":  {  "id":  1,  "category":  {  "id":  1,  "name":  "dogs"  },  "name":  "Hachiko",  "tags":  [  {  "id":  1,  "name":  "japanese"  }  ],  "status":  "available"  }  },  {  "method":  "POST",  "path":  "/pet",  "status":  201,  "response":  {  "id":  2,  "category":  {  "id":  1,  "name":  "dogs"  },  "name":  "Bolt",  "tags":  [  {  "id":  2,  "name":  "fictional"  }  ],  "status":  "available"  }  },  {  "method":  "DELETE",  "path":  "/pet/:id",  "status":  204,  "response":  {  "status":  "deleted"  }  },  {  "method":  "GET",  "path":  "/user/login",  "status":  400,  "response":  {  "status":  "invalid username/password supplied"  }  }  ] 
```

用`localroast pets.json`启动模拟服务器。

```
$ localroast pets.json                                                                                                           
INFO[0000] brewing on port 8080 
```

一旦服务器开始运行，我们就可以点击任何 API 并接收前面指定的 JSON 响应。

```
$ curl -XPOST localhost:8080/pet
{ "id": 1, "category": { "id": 1, "name": "dogs" }, "name": "Hachiko", "tags": [ { "id": 1, "name": "japanese" } ], "status": "available" }

$ curl -XPOST localhost:8080/pet -D "{ \"name\": \"Bolt\" }"
{ "id": 2, "category": { "id": 1, "name": "dogs" }, "name": "Bolt", "tags": [ { "id": 2, "name": "fictional" } ], "status": "available" }

$ curl localhost:8080/user/login
{ "status": "invalid username/password supplied" } 
```

通常，我们需要在各种场景之间保持测试，比如成功的调用、错误的场景、错误的请求等等。Localroast 允许我们非常容易地自动加载对文件的任何更改，使更改立即可用。

假设我们想要测试一个有效的登录响应，我们需要做的就是用一个成功的场景更新登录 API 响应。

```
{  "method":  "GET",  "path":  "/user/login",  "status":  200,  "response":  {  "status":  "logged in"  }  } 
```

一旦文件被保存，我们可以看到服务器自动更新。

```
INFO[0533] file changed: pets.json
INFO[0533] parsed new schema
INFO[0533] updating router with new schema 
```

现在，如果我们再次点击登录 API，就会得到新的响应。

```
$ curl localhost:8080/user/login
{ "status": "logged in" } 
```

希望这个工具可以加快你的开发工作流程。欢迎任何反馈、功能请求和拉动请求。