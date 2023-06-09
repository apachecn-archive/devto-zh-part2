# 模式优先的 API 设计

> 原文：<https://dev.to/yos/schema-first-api-design--41de>

> 在 [yos.io](https://yos.io/) 找到更多有趣的文章
> 
> 本指南向您介绍了模式优先的 API 设计领域，以及如何开始使用 OpenAPI 生态系统。

[![](img/0b64c338918447237c0b9ff39912c57e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hQIy1_MY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/XrBO4V4.jpg)

你在构建一个 API。

您开发一个带有几个端点的后端服务，并将其部署到生产环境中。您发布了几个特定于官方语言的 API 客户端以及一个 API 文档。这一天在快乐中结束。

第二天，一个新的特性被添加到 API 中。你必须:

*   更新服务器实现以支持新功能。
*   更新所有客户端库(每个支持的平台和语言一个 SDK。)
*   更新文档。
*   以上都必须相互一致。
*   此外，前端团队被阻塞，直到您的后端 API 完成。

你发出一声沉重的叹息。

> 有更好的方法吗？

对于 API 的每一个变更，必须执行几个步骤来保持所有开发工件的一致性。官方支持的 API 客户端 SDK 必须支持最新的 HTTP API 端点。文档还必须与实际实现保持一致和真实。

这些步骤中的每一步都是劳动密集型的，并且容易出错。因此，对我们来说，将这些任务自动化是有意义的，这样我们就可以专注于实际构建特性。问题是:怎么做？

我们没有直接进行开发，而是从 API 规范开始。

# 模式优先的 API 设计

模式优先的 API 设计方法提倡在编写任何代码之前，先用许多 API 规范语言之一编写 API 定义。编写 API 规范有几个好处:

## 改进的 API 设计

API 规范代表了一个 API 的契约。

[![](img/163a42f38b806db931e3a3c657f69cdf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Sc-pEocB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/F6pvy24.png)

有了 API 规范和 Swagger UI 之类的工具，您可以可视化您的 API，以便其他开发人员和利益相关者可以学习并尽早给出设计反馈。您甚至可以运行一个基于 API 规范的模拟服务，其他团队可以立即与之交互。

在编写任何代码之前识别设计中的问题，比在实现已经就绪之后这样做要高效和简化得多。

## 跨多个团队迭代更快

有了 API 规范，涉及多个团队(后端、web、移动等)的开发项目。)可以比传统方法进行得更快。

无论后端组件是否准备好，前端团队都可以立即开始构建组件，因为我们可以基于 API 规范生成和运行模拟服务。团队可以并行工作，不会被另一个团队的进度所阻碍。

## 为你的 API 创建测试

您的 API 规范提供了一个契约，它描述了当有人调用您的 API 时，成功的响应是什么样子的。这个契约可以重新用于生成测试用例，这可以极大地减少测试 API 所需的工作量。您可以根据 API 规范创建功能测试并运行模拟服务。

## 生成代码和文档

[![](img/0b413fa99a7ebaf02a016512cc88e8fe.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_Sit2TAe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/Ug7AkZ6.png)

模式优先的方法创建了可用于生成各种开发工件的单一事实来源。定义良好的 API 规范可用于自动构建 API、生成 API 引用以及与 API 通信的客户端 SDK。

## 总结

采用模式优先 API 设计的初始成本很小，但是从中获得的好处是巨大的。

# API 规范语言

API 规范语言定义了 REST APIs 的语言无关的标准表示。API 规范语言的例子包括 [OpenAPI](https://swagger.io/specification/) 、 [API 蓝图](https://apiblueprint.org/)和 [RAML](https://raml.org/) 。

在下一节中，我们将研究 OpenAPI 规范语言和围绕它的工具。

# open API 规范语言

[![](img/cfcc47848ef74833d7e7746325140e58.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QwaVhdpS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.openapis.org/wp-content/uploads/sites/3/2016/10/OpenAPI_Pantone.png)

## 概述

API 形成了现代应用程序之间的粘合剂。几乎每个应用程序都使用 API 来连接企业数据源、第三方数据服务或其他应用程序。为 API 服务创建一种开放的描述格式，这种格式是厂商中立的、可移植的和开放的，对于加速实现真正互联世界的愿景至关重要。

OpenAPI 是一种基于 JSON/YAML 的 API 规范语言和框架，用于描述、生产、消费和可视化 RESTful web 服务。OpenAPI 的首要目标是使客户机和文档系统能够与服务器同步更新。

> OpenAPI 和 Swagger 有什么区别？
> 
> OpenAPI 是该规范的正式名称。该规范的开发是由 OpenAPI 倡议推动的，该倡议涉及来自技术世界不同领域的 30 多个组织，包括微软、谷歌、IBM 和 CapitalOne。Smartbear Software 是领导 Swagger 工具开发的公司，也是 OpenAPI 计划的成员，帮助领导该规范的发展。
> 
> Swagger 是一些最著名的、广泛使用的实现 OpenAPI 规范的工具的名字。OpenAPI 3.0 规范基于 Swagger 2.0 规范。Swagger 工具集包括开源、免费和商业工具的组合，可以在 API 生命周期的不同阶段使用。

OpenAPI 提供的不仅仅是一种规范语言，生态系统中的其他工具还包括:

*   **[Swagger 编辑器](https://editor.swagger.io//#/) :** Swagger 编辑器让你在浏览器内编辑 YAML 的 OpenAPI 规范，并实时预览文档。
*   **[Swagger UI](https://swagger.io/swagger-ui/):**Swagger UI 是 HTML、Javascript 和 CSS 资产的集合，可以从符合 OAS 的 API 动态生成漂亮的文档。
*   **[Swagger Codegen](https://github.com/swagger-api/swagger-codegen) :** 允许自动生成 API 客户端库(SDK 生成)、服务器存根和文档。
*   **[斯瓦格检查员](https://swagger.io/swagger-inspector/) :** API 检查工具，允许您从现有 API 生成 OpenAPI 定义

## 入门

OpenAPI 规范允许您描述 API，包括(除了别以外):

*   关于 API 的一般信息
*   可用路径(如`/resources`)
*   每条路径上可用的操作(如`GET /resources/{id}`)
*   每个操作的输入/输出

这里是 YAML 的一个最小的 OpenAPI 规范:

```
openapi: 3.0.0

info:
  title: Sample API
  description: Optional multiline or single-line description in [CommonMark](http://commonmark.org/help/) or HTML.
  version: 0.1.9

servers:
  - url: http://api.example.com/v1
    description: Optional server description, e.g. Main (production) server
  - url: http://staging-api.example.com
    description: Optional server description, e.g. Internal staging server for testing

paths:
  /users:
    get:
      summary: Returns a list of users.
      description: Optional extended description in CommonMark or HTML.
      responses:
        '200':    # status code
          description: A JSON array of user names
          content:
            application/json:
              schema:
                type: array
                items:
                  type: string 
```

Enter fullscreen mode Exit fullscreen mode

在 OpenAPI 3.0 规范中，[根级别](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.0.md#oasObject)有 8 个部分。在这些根级对象中有许多嵌套对象，但是在根级，只有这些对象:

*   `openapi` : **必输。**OpenAPI 文档使用的 OpenAPI 规范版本的语义版本号(如`3.0.0`)。)
*   `info` : **必选。**提供关于 API 的元数据。
*   `servers`:服务器对象的数组，提供与目标服务器的连接信息。
*   `components`:保存规范的各种模式的元素。
*   `security`:声明哪些安全机制可以跨 API 使用。
*   `tags`:带有附加元数据的规范使用的标签列表。
*   `externalDocs`:附加外部文件。
*   `paths` : **必选。**API 的可用路径和操作。

> [![](img/24d834d9c32462e4788d17b3bc158f7e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ITLW3YUL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://techsparx.com/software-development/openapi/img/swagger-editor-online.jpg)
> 
> 你可以使用在线的 [Swagger 编辑器](https://editor.swagger.io//#/)跟随！

让我们简单地看一下上面的每一部分。

### `info`节

`info`对象提供了关于 API 的元数据。

```
info:
  title: Sample Pet Store App
  version: 1.0.1
  description: This is a sample server for a pet store.
  termsOfService: http://example.com/terms/
  contact:
    name: API Support
    url: http://www.example.com/support
    email: support@example.com
  license:
    name: Apache 2.0
    url: http://www.apache.org/licenses/LICENSE-2.0.html 
```

Enter fullscreen mode Exit fullscreen mode

> 记得去网上[霸气编辑器](https://editor.swagger.io//#/)试试！

### `servers`节

`servers`部分指定了 API 服务器和基本 URL。您可以定义一个或多个服务器，如生产服务器和沙盒服务器。

```
servers:
  - url: https://my-api.com
    description: Production server
  - url: https://beta.my-api.com
    description: Beta server
  - url: https://some-other.my-api.com
    description: Some other server 
```

Enter fullscreen mode Exit fullscreen mode

在 Swagger UI 上，用户可以从这个预定义的列表中选择发送请求的服务器:

[![](img/d30d7f05a9e7138c6f24f7b1ea7ca2bc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ajdYN9-5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/P49dxc8.png)

### `components`节

[组件](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.0.md#components-object)是 API 其余部分的一组可重用对象，例如:

#### 模式对象

[基于 JSON-schema](http://json-schema.org/) 的输入输出数据类型的定义。

global `components/schemas`部分允许您定义 API 中使用的公共数据结构。只要在参数、请求体和响应体中需要模式，就可以通过$ref 引用它们。

比如这个 JSON 对象:

```
{
  "name": "Garfield",
  "type": "Cat"
} 
```

Enter fullscreen mode Exit fullscreen mode

可以表示为:

```
 components:
  schemas: # Schema object definition
    Pet:
      type: object
      properties:
        name:
          type: string
        petType:
          type: string
      required:
      - name
      - petType 
```

Enter fullscreen mode Exit fullscreen mode

并在别处用`$ref: '#/components/schemas/Pet'`引用。

> 了解更多关于 OpenAPI 数据模型的信息。

#### 响应对象

操作的不同 HTTP 响应代码的预期响应。

```
components:
  responses:
    ListPetsSuccessResponse:
      description: A an array response with headers
      content:
        application/json:
          schema:
            type: array
            items:
              $ref: '#/components/schemas/Pet'
          headers:
            X-Rate-Limit-Limit:
              description: The number of allowed requests in the current period
              schema:
                type: integer 
```

Enter fullscreen mode Exit fullscreen mode

#### 参数对象

操作可以通过 URL 路径(`/users/{userId}`)、查询字符串(`/users?role=admin`)、头(`X-CustomHeader: Value`)或 cookie(`Cookie: debug=0`)传递参数。您可以定义参数数据类型、格式、是必选还是可选，以及其他详细信息:

```
# A path parameter of string value

name: username
in: path
description: username to fetch
required: true
schema:
  type: string 
```

Enter fullscreen mode Exit fullscreen mode

```
# A header parameter with an array of 64 bit integer numbers:
name: token
in: header
description: token to be passed as a header
required: true
schema:
  type: array
  items:
    type: integer
    format: int64 
```

Enter fullscreen mode Exit fullscreen mode

#### 示例对象

下面是响应正文中关键字`example`的一个示例:

```
responses:
  '200':
    description: A cat object.
    content:
      application/json:
        schema:
          $ref: '#/components/schemas/Pet'   # Reference to an object
        example:
          # Example properties of the referenced object
          name: Garfield
          type: Cat 
```

Enter fullscreen mode Exit fullscreen mode

#### 其他组件对象

还可以在`components`部分定义安全方案、Header、[链接](https://swagger.io/docs/specification/links/)(超媒体)、以及[回调](https://swagger.io/docs/specification/callbacks/) (webhook)对象。

#### 组件汇总

使用`components`完全是可选的。它对于以可重用的方式存储任何经常使用的域对象非常有用。

> 除非从 components 对象外部的属性中显式引用，否则在 components 对象中定义的所有对象对 API 都没有影响。

### `security`节

对象指定了提交请求时使用的安全或授权协议。OpenAPI 支持以下身份验证方法:

*   HTTP 认证:基本认证、承载认证等等。
*   API 键作为标题或查询参数或在 cookies 中
*   OAuth 2
*   OpenID 连接发现

在 OpenAPI 文档的根级别，添加一个安全对象，它定义了我们用于安全性的全局方法:

```
security:
- API-Key: [] 
```

Enter fullscreen mode Exit fullscreen mode

在`components`对象中，我们添加了一个 securitySchemes 对象，它定义了我们正在使用的安全方案的细节:

```
components:
  securitySchemes:
    API-Key:
      type: apiKey
      description: "The API authorizes requests through an API key in your header."
      name: X-API-Key
      in: header 
```

Enter fullscreen mode Exit fullscreen mode

### `tags`和`externalDocs`部分

想象一下，如果您有一个包含 50 多条路径的 API 来描述。您可能希望将它们组织成逻辑组，以便用户导航。`tags`对象提供了一种在 Swagger UI 显示中对路径(端点)进行分组的方法。

在根级别定义您的标签:

```
tags:
  - name: Products
    description: Handles product information.
  - name: Orders
    description: Operations for processing purchases and orders. 
```

Enter fullscreen mode Exit fullscreen mode

然后，您可以在单个操作中使用任何已定义的标签:

```
paths:
 ...
 /products:
   get:
     ...
     tags:
     - Products
     ... 
```

Enter fullscreen mode Exit fullscreen mode

这将在 Swagger UI 中对具有相同标签的操作进行分组:

[![](img/a49a57f2621f34a8cd3672aa6a56c039.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cx5htxtN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://idratherbewriting.com/learnapidimg/openapitutorial_tags.png)

下面是一个`externalDocs`对象的例子:

```
externalDocs:
  description: Product documentation for the API
  url: https://myapi.com/docs 
```

Enter fullscreen mode Exit fullscreen mode

在 Swagger UI 中，这个链接出现在 API 描述以及关于 API 的其他信息之后。

### `paths`节

在 OpenAPI 术语中，`paths`是你的 API 公开的端点(资源)，比如`/pets`或者`/products/summary/`，操作是用来操纵这些路径的`HTTP`方法，比如`GET`、`POST`或者`DELETE`。

首先，让我们列出 API 的路径:

```
paths:
  /products:
    get:

  /orders:
    post: 
```

Enter fullscreen mode Exit fullscreen mode

每个路径对象(`/products.get`，`/orders.post`)都是一个操作。这里有一个完整的例子:

```
paths:
  /products/{id}:
    get:
      tags:
        - Products
      summary: Gets a product by ID.
      description: >
        A detailed description of the operation.
        Use markdown for rich text representation,
        such as **bold**, *italic*, and [links](http://swagger.io).
      operationId: getProductById
      parameters:
        - name: id
          in: path
          description: Product ID
          required: true
          schema:
            type: integer
            format: int64
      responses:
        '200':
          description: Successful operation
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Product'
      externalDocs:
        description: Learn more about product operations provided by this API.
        url: http://api.example.com/docs/product-operations/ 
```

Enter fullscreen mode Exit fullscreen mode

每个操作都记录了操作的任何参数、不同种类的响应和其他元数据。您可以在这里引用您在`components`部分定义的公共数据结构。

## OpenAPI summary

这就是你需要开始！[试试看！](https://editor.swagger.io)

一旦编写完成，OpenAPI 规范和 Swagger 工具可以进一步推动您的 API 开发，并在长期内节省大量的时间和精力:

*   使用 [Swagger Codegen](https://github.com/swagger-api/swagger-codegen) 为您的 API 生成一个服务器存根。剩下的唯一事情就是实现服务器逻辑——您的 API 已经准备好投入使用了！
*   使用 Swagger Codegen 为您的 API 生成 40 多种语言的客户端库。
*   使用 [Swagger UI](https://swagger.io/swagger-ui/) 生成交互式 API 文档，让用户直接在浏览器中尝试 API 调用。
*   [还有更多！](https://swagger.io/open-source-integrations/)

# 在关闭

模式优先的 API 设计方法提倡在编写任何代码之前，先用许多 API 规范语言之一编写 API 定义。

采用模式优先的 API 设计有一个小的初始投资和学习曲线，但是从中获得的好处是显著的。编写 API 规范有很多好处，比如改进的 API 设计、更快的迭代以及自动化的代码和文档生成。

# 附录

## 如果我已经有一个 API 了怎么办？

如果你已经实现了你的 API(至少部分实现了)，你可以从 [Swagger Inspector](https://inspector.swagger.io/builder) 中调用你的 API。
为你调用的任何端点自动生成 OpenAPI 文件。

## 补充阅读

以下是一些推荐的进一步学习资源:

*   [OpenAPI 3.0 规范](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.0.md)
*   [Swagger 规范文档](https://swagger.io/docs/specification/about/)

> 在 [yos.io](https://yos.io/) 找到更多有趣的文章