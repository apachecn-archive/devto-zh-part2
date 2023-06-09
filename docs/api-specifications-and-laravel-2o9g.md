# API 规范和 Laravel

> 原文：<https://dev.to/mattnmoore/api-specifications-and-laravel-2o9g>

构建一个 API 或任何代码库的最重要的部分之一，是确保它有良好的文档记录。如果没有文档，用户如何知道如何使用你的 API，或者它能做什么？如果没有描述他们应该如何表现的东西，你的团队成员(或者 6 个月后的你自己)如何知道如何扩展和测试你的端点？

根据我的经验，文档的最大障碍是维护它们。当有人急着构建一个特性，却忘记将它添加到文档中时，会发生什么？或者当业务规则改变并且某些输入和输出改变时？维护不良的文档很快变得毫无用处，因为用户和开发人员不再信任它们，突然间就好像根本没有文档一样，让他们去猜测合同。参见:[破窗理论](https://blog.codinghorror.com/the-broken-window-theory/)了解为什么会发生这种情况。

这就是规范的用武之地。

## API 规范与 API 文档

什么是 API 规范，它与文档有何不同？ [Nordic APIs 有一篇很棒的文章，详细介绍了这些差异](https://nordicapis.com/difference-api-documentation-specification-definition/)

简而言之，文档解释了如何使用 API，而规范描述了 API 的行为。我见过被描述为 API 蓝图的规范，我认为这种类比是准确的。事实上，您可以从一个构建良好的规范中获得许多东西，包括文档、验证规则、前端 API 客户端、[契约测试](https://blog.apisyouwonthate.com/tricking-colleagues-into-writing-documentation-via-contract-testing-206308b47e05)等等。

有许多规范可供选择，但是我将重点放在 OpenAPI 第 2 版(稍后我将解释原因)和 JSON 模式上。

## OpenAPI

记住，在本文的剩余部分，我们将使用 [Open API Version 2](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md) 。

### 生成规范

Open API 是由 SmartBear 开发的 API 规范，可以编写(或生成)为 YAML 或 JSON 文件。您可能还会注意到，在使用 Laravel 或 PHP 时，您可以使用诸如 [L5-Swagger](https://github.com/DarkaOnLine/L5-Swagger) 之类的库来使用注释来生成您的规范。尽管我的直觉告诉我这是一个可怕的想法，我还是决定试一试。我的直觉是对的，这是一个可怕的想法，原因有很多。

最显而易见的原因是，编写和维护这些注释是一项繁重的工作。没有 IDE 插件来帮助格式化，所以你最终只能摆弄星号、空格、花括号和逗号来保持它们的美观。

除此之外，一旦您完成了注释的编写，您会注意到它们现在占据了您的大部分源文件，增加了不必要的认知开销，并且在需要时很难找到。

对于这种疯狂，有什么更好的方法呢？我们仍然需要我们的规范是可伸缩的，所以它不能作为一个文件存在。我们希望它易于导航和编辑，我们希望文档、测试等的生成简单明了。

我选择在我的 Laravel 应用程序的根目录下，用 YAML 文件将我的规范和代码分开保存在一个名为`spec`的文件夹中。虽然我绝不是 YAML 的粉丝，但我更喜欢用手写而不是 JSON。

### 举例

让我们假设我们正在为一个博客构建一个简单的 REST API，其中有几个端点用于管理文章。端点可能看起来像这样:

```
GET /
GET /articles
POST /articles
GET /articles/{slug}
PUT /articles/{slug}
DELETE /articles/{slug} 
```

Enter fullscreen mode Exit fullscreen mode

#### 设置应用程序

如果您想继续，可以创建一个开发环境(直接在您的开发机器上，或者使用一个类似于 vagger 或 Docker 的工具)，并安装一个新的 Laravel 副本。

#### 设置构建工具

我们希望首先设置构建我们的规范所需的所有工具和脚本。我使用的工具有:json-refs、path-loader、js-yaml 和 swagger-cli。确保安装了 NPM，然后用
抓取这些包

```
npm install --save-dev json-refs path-loader js-yaml swagger-cli 
```

Enter fullscreen mode Exit fullscreen mode

在应用程序根目录下创建一个脚本目录，并将以下脚本放在名为 build-spec.js
的文件中

```
var JsonRefs = require('json-refs');
var PathLoader = require('path-loader');
var YAML = require('js-yaml');
var fs = require('fs');

var root = YAML.load(fs.readFileSync('../spec/api.yaml').toString());

var options = {
 loaderOptions: {
     processContent: function (res, callback) {
         callback(YAML.safeLoad(res.text));
     }
 },
 location: '../spec/api.yaml'
};

JsonRefs.resolveRefs(root, options).then(function (results) {
   var dir = '../spec/dist';
   if (!fs.existsSync(dir)){
       fs.mkdirSync(dir);
   }

   fs.writeFileSync('../spec/dist/api.yaml', YAML.dump(results.resolved), function(err) {
       if(err) {
           return console.log(err);
       }

       console.log('Specification compiled successfully.');
   });
}, function(err) {
 console.log(err);
}); 
```

Enter fullscreen mode Exit fullscreen mode

这个脚本将 YAML 文件转换成 JSON，并解析我们所有的外部引用。

最后，将下面一行添加到 package.json 文件的脚本部分。

```
"build-spec":  "cd scripts && node build-spec.js && swagger-cli validate ../spec/dist/api.yaml" 
```

Enter fullscreen mode Exit fullscreen mode

#### 创建规格对象

现在我们已经设置好了构建工具，在应用程序的根目录下创建 spec 文件夹，并创建一个名为`api.yaml`的文件。根据规范，有三个必填字段。swagger 字段，它指定了我们正在使用的 openapi 的版本(它在版本 3 中被称为 Open API)，info 字段，它包含关于我们的 API 的基本信息，如它的名称和版本，以及 paths 字段，它包含我们的端点定义。放在一起，一个基本的规范可能看起来像这样:

```
swagger: '2.0'
info:
 version: '1.0'
 title: Blog API

host: 'api.blog.test'
schemes: [http]
consumes: [application/json]
produces: [application/json]

paths:
 $ref: ./paths.yaml 
```

Enter fullscreen mode Exit fullscreen mode

在 paths.yaml 中，让我们创建一个简单的端点来显示我们的 API 版本

```
/:
 get:
   summary: 'Return  the  API's version'
   responses:
     200:
       description: 'API  version  successfully  returned.' 
```

Enter fullscreen mode Exit fullscreen mode

您现在可以运行构建脚本来解析外部引用，验证规范，并通过运行:
输出最终产品

```
npm run build-spec 
```

Enter fullscreen mode Exit fullscreen mode

#### 添加端点

让我们将 post 索引端点添加到我们的规范中。我喜欢按照[Scaling Laravel Architecture](https://mattdoescode.com/scaling-laravel-architecture)中概述的应用程序结构，将我们的规范分成模块。

创建一个文章目录、一个路径子目录和一个名为 article-index.yaml 的 YAML 文件

```
spec
├── dist
│   └── api.yaml
├── articles
│   └── paths
│       └── article-index.yaml
├── api.yaml
└── paths.yaml 
```

Enter fullscreen mode Exit fullscreen mode

让我们写我们的后索引端点规格！

```
summary: 'Return  a  list  of  articles.'
tags:
 - Articles
parameters:
 - name: page
   in: query
   required: false
   type: integer
 - name: limit
   in: query
   required: false
   type: integer
responses:
 200:
   description: 'Articles  returned  successfully.'
   schema:
     type: object
     properties:
       meta:
         type: object
         properties:
           page:
             type: integer
           total:
             type: integer
           last_page:
             type: integer
           limit:
             type: integer
       data:
         type: array
         items:
           type: object
           properties:
             title:
               type: string
             published_at:
               type: string
               format: date
             author:
               type: object
               properties:
                 name:
                   type: string
             body:
               type: string 
```

Enter fullscreen mode Exit fullscreen mode

然后在我们的 paths.yaml 文件:

```
/articles:
 get:
   $ref: ./articles/paths/article-index.yaml 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我们已经定义了端点、可用参数和输出模式的概要。路径操作也继承了我们在根级规范中定义的`consumes`和`produces`字段。如果我们愿意，也可以在这里覆盖它们。

#### 提取可复用组件

我们可以，也应该，收拾这个烂摊子。您会注意到这里有一些对抽象和重用有用的结构，比如分页查询参数、元数据分页输出和文章实体输出。将这些结构提取到一个可重用的结构中不仅节省了时间，而且允许我们在一个地方维护它们，而不是在它们出现的每个实例中。

OpenAPI 使得定义可重用参数变得容易。将名为`parameters.yaml`的文件添加到根规范目录中。

```
pageParam:
 name: page
 in: query
 required: false
 type: integer

limitParam:
 name: limit
 in: query
 required: false
 type: integer 
```

Enter fullscreen mode Exit fullscreen mode

然后像路径一样链接它 api.yaml。

```
parameters:
 $ref: ./parameters.yaml 
```

Enter fullscreen mode Exit fullscreen mode

要引用这里定义的对象，可以这样引用:

```
 - $ref: '#/parameters/pageParam' 
```

Enter fullscreen mode Exit fullscreen mode

所以 article-index.yaml 的参数部分现在看起来像这样:

```
parameters:
 - $ref: '#/parameters/pageParam'
 - $ref: '#/parameters/limitParam' 
```

Enter fullscreen mode Exit fullscreen mode

ref 以#开头，表示它是内部基准电压源。因为这些文件首先解析外部引用，所以我们所有的定义都和路径在同一个文件中，所以我们可以在内部引用它们。

现在我们需要将我们的 Post 定义提取到它自己的模式文件中。根据开放 API 规范版本 2:“模型是用模式对象描述的，它是 JSON 模式草案 4 的子集。”

模型可能是 JSON 模式的子集，但是据我所知，您不能在 Open API v2 中直接使用 JSON 模式对象。我想使用 JSON Schema 为我们的模型定义一个单一的事实来源，并将它们导入到我们的 Open API 规范中，但是当前的工具不允许在版本 2 中使用它。WeWork 的优秀人员已经用 Open API v3 构建了 [Speccy](https://github.com/wework/speccy) 来处理这种类型的设置，但不幸的是我们没有使用版本 3(我们将在后面讨论这背后的原因)。

所以现在，我们将简单地使用开放 API 规范来定义我们的模型。当有可行的方法时，我很乐意回到这个问题上来，进一步探索 JSON 模式。

在`spec/articles/schemas`
中创建一个名为`article.yaml`的文件

```
type: object
properties:
 id:
   type: integer
 title:
   type: string
 published_at:
   type: string
   format: date
 body:
   type: string
 author:
   $ref: '#/definitions/Author' 
```

Enter fullscreen mode Exit fullscreen mode

现在同一个文件夹中有一个名为`author.yaml`的文件。

```
type: object
properties:
 id:
   type: integer
 name:
   type: integer
required:
 - id
 - name 
```

Enter fullscreen mode Exit fullscreen mode

现在在 spec/目录中创建一个名为`definitions.yaml`的文件。

```
Article:
 $ref: ./articles/schemas/article.yaml
Author:
 $ref: ./articles/schemas/author.yaml 
```

Enter fullscreen mode Exit fullscreen mode

并链接根规范中的定义文件，就像我们处理路径和参数文件一样。

```
definitions:
 $ref: ./definitions.yaml 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以像这样引用路径操作中的模型:

```
responses:
 200:
   description: 'Articles  returned  successfully.'
   schema:
     type: object
     properties:
       meta:
         type: object
         properties:
           page:
             type: integer
           total:
             type: integer
           last_page:
             type: integer
           limit:
             type: integer
       data:
         type: array
         items:
           $ref: '#/definitions/Article' 
```

Enter fullscreen mode Exit fullscreen mode

我们可以用同样的方式提取元对象。在根规范中创建一个共享的/ module，并添加一个 schemas 文件夹，其中包含一个名为`pagination.yaml`
的文件

```
type: object
properties:
 page:
   type: integer
 total:
   type: integer
 last_page:
   type: integer
 limit:
   type: integer 
```

Enter fullscreen mode Exit fullscreen mode

最后的路径操作是这样的:

```
summary: 'Return  a  list  of  articles.'
tags:
 - Articles
parameters:
 - $ref: '#/parameters/pageParam'
 - $ref: '#/parameters/limitParam'
responses:
 200:
   description: 'Articles  returned  successfully.'
   schema:
     type: object
     properties:
       meta:
         $ref: '#/definitions/Pagination'
       data:
         type: array
         items:
           $ref: '#/definitions/Article' 
```

Enter fullscreen mode Exit fullscreen mode

干净多了！这个流程肯定有可以改进的地方，但是我相信这是构建可伸缩和可维护的 API 规范的好模式。

## 带 ReDoc 的文档

现在我们有了基本的规范，让我们生成一些文档！使用 [ReDoc](https://github.com/Rebilly/ReDoc) ，我们可以将我们的规范转化为干净、易读的文档。首先，通过运行
来获取 redoc-cli

```
npm install -g redoc-cli 
```

Enter fullscreen mode Exit fullscreen mode

然后将以下内容添加到`package.json`
中的脚本部分

```
"serve-docs":  "redoc-cli serve spec/dist/api.yaml --watch" 
```

Enter fullscreen mode Exit fullscreen mode

您现在可以通过运行
来提供您的文档

```
npm run docs 
```

Enter fullscreen mode Exit fullscreen mode

导航到 [http://127.0.0.1:8080](http://127.0.0.1:8080) ，您应该会看到类似这样的内容:

[![ReDoc documentation landing page](img/ca4948cafa336f3f9d021bbd8b690876.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UyUYHlf7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/blbf2l8jbrvwte7udjj9.png)

容易的

## 测试您的规格

对我来说，能够根据 API 测试你的规范，并将这个过程集成到你的 CI/CD 中，是拥有一个规范的最大好处之一。我见过的唯一能做到这一点的工具是 [Dredd](https://github.com/apiaryio/dredd) ，这也是我建议使用 Open API v2 的原因。

Dredd 还没有开放 API v3 支持，一个[票已经开放添加了一年多](https://github.com/apiaryio/dredd/issues/894)。我不确定什么时候能看到它，鉴于有工具可以帮助从 Open API v2 转换到 v3，我认为坚持使用 v2 来获得 Dredd 的好处是值得的。

### 设置

使用 NPM
安装 Dredd

```
npm install -g dredd 
```

Enter fullscreen mode Exit fullscreen mode

通过运行
初始化 Dredd

```
dredd init 
```

Enter fullscreen mode Exit fullscreen mode

```
? Location of the API description document (apiary.apib) spec/dist/api.yaml
? Command to start the API server under test (npm start) 
? Host of the API under test (http://127.0.0.1:3000) http://api.blog.test
? Do you want to use hooks to customize Dredd's behavior? (Y/n) Y
  Go 
  JavaScript 
  Perl 
❯ PHP 
  Python 
  Ruby 
  Rust 
? Do you want to report your tests to the Apiary inspector? (Y/n) n
? Dredd is best served with Continuous Integration. Do you want to create CI configuration? (Y/n) N 
```

Enter fullscreen mode Exit fullscreen mode

确保您已将应用程序配置为在 [http://api.blog.test](http://api.blog.test) 提供服务，或者用您的 Laravel 应用程序的位置替换它。接下来，用
安装 Dredd PHP 挂钩

```
composer require ddelnano/dredd-hooks-php --dev 
```

Enter fullscreen mode Exit fullscreen mode

当 Dredd 运行时，我们可以使用这些钩子为测试模式配置我们的应用程序。现在，让我们看看运行 Dredd:
时会发生什么

```
info: Configuration './dredd.yml' found, ignoring other arguments.
info: Beginning Dredd testing...
fail: GET (200) / duration: 248ms
fail: GET (200) /articles duration: 36ms 
```

Enter fullscreen mode Exit fullscreen mode

由于长度原因，我已经截断了输出的其余部分，但是正如您将看到的，我们所有的测试都失败了！每一次失败，你都会得到请求、预期的输出和实际的输出，因为我们在实际应用中没有做任何改变，所以它不能反映我们所指出的！让我们用嘲笑的回答来解决这个问题。

### 我们的第一条路

我们添加到规范中的第一个路径是显示应用程序版本的根端点。它产生 JSON，并且应该输出一个版本号。让我们来看看 Dredd 告诉我们的这条道路:

```
request: 
method: GET
uri: /
headers: 
    Content-Type: application/json
    Accept: application/json
    User-Agent: Dredd/5.2.0 (Linux 4.15.0-38-generic; x64)

body: 

expected: 
headers: 
    Content-Type: application/json

statusCode: 200

actual: 
statusCode: 200
content-type: text/html; charset=UTF-8 
```

Enter fullscreen mode Exit fullscreen mode

同样，我已经截断了它，因为它有大量的输出，但是您可以看到它期望一个带有 application/json mime 类型的 200 响应，但是它实际上收到了一个带有 text/html 的 200 响应。我们可以通过用 JSON 响应替换路由中的欢迎视图来快速解决这个问题，如下所示:

```
Route::get('/', function () {
   return response()->json([
       'name' => 'Blog API',
       'version' => '1.0'
   ]);
}); 
```

Enter fullscreen mode Exit fullscreen mode

如果我们重新运行我们的测试，我们看到它现在通过了！

### 文章端点

如果我们看下一个测试，我们会发现它也失败了。

```
fail: GET (200) /articles duration: 38ms
fail: statusCode: Status code is '404' instead of '200' 
```

Enter fullscreen mode Exit fullscreen mode

该路由不存在，所以让我们通过在 routes 文件中添加一个条目来添加它。

**注意:我们不想把这些直接放在我们的 routes 文件中，这样更容易演示。**

```
Route::get('/articles', function () {
}); 
```

Enter fullscreen mode Exit fullscreen mode

如果我们重新运行 Dredd，我们会得到以下输出

```
fail: GET (200) /articles duration: 74ms
fail: headers: Header 'content-type' has value 'text/html; charset=UTF-8' instead of 'application/json' 
```

Enter fullscreen mode Exit fullscreen mode

我们现在得到 200，但是内容类型无效。让我们返回一个 JSON 响应，看看会发生什么。

```
Route::get('/articles', function () {
   return response()->json([]);
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在德雷德告诉我们…

```
fail: GET (200) /articles duration: 71ms
fail: body: At '' Invalid type: array (expected object) 
```

Enter fullscreen mode Exit fullscreen mode

我们正在返回正确的状态代码和内容类型，但是实际的响应被 Dredd 标记为无效。让我们看看文档，看看 Dredd 期望什么:

[![Articles endpoint documentation in ReDoc](img/9ba2da21251734a38b29500241924640.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CwpDOlZT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/38bgwp9awnvu0s04gd83.png)

我们可以看到，我们的响应应该是一个具有元和数据属性的对象。Meta 应该包含我们在模式中定义的分页对象，data 属性应该是包含 Article 对象的数组。Dredd 验证了我们的 API 正在返回正确的数据结构，所以让我们模拟一下它的样子:

```
Route::get('/articles', function () {
   return response()->json([
       'meta' => [
           'page' => 1,
           'total' => 1,
           'last_page' => 1,
           'limit' => 10 
       ],
       'data' => [
       [
           'id' => 1,
           'title' => 'My Article Title',
           'published_at' => '2018-11-13',
           'body' => 'My article body',
           'author' => [
               'id' => 1,
               'name' => 'Test'
           ]
       ]]
   ]);
}); 
```

Enter fullscreen mode Exit fullscreen mode

如果我们运行 Dredd，我们可以看到我们所有的测试都通过了！

```
info: Configuration './dredd.yml' found, ignoring other arguments.
info: Beginning Dredd testing...
pass: GET (200) / duration: 174ms
pass: GET (200) /articles duration: 22ms
complete: 2 passing, 0 failing, 0 errors, 0 skipped, 2 total
complete: Tests took 198ms 
```

Enter fullscreen mode Exit fullscreen mode

这是防止文档腐烂的最终保障！除非您的规范与您的实际 API 相匹配，否则您可以并且应该设置措施来防止部署。围绕您的规范的这种保护是无价的。

## 其他工具

ReDoc 和 Dredd 只是新规范的开始。有一些工具可以让你生成验证规则、客户端库、模拟服务器等等！

## 总结

这篇文章比我预期的要长一点，我甚至没有触及我想要的一切，所以我可能会在未来的某个时候写一篇后续文章。同时，请随时在下面的部分分享任何问题、评论或建议！感谢阅读！

帖子 [API 规范和 Laravel](https://mattdoescode.com/api-specifications-and-laravel) 最先出现在 [Matt Does 代码](https://mattdoescode.com)上。