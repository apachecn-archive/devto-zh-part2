# 使用无服务器 Webhooks 的 Algolia 索引

> 原文：<https://dev.to/martyndavies/algolia-indexing-with-serverless-webhooks-45jj>

很长一段时间以来，创建一个“快速脚本”来完成一项任务一直是您可以在本地机器上实现的领域。

大多数情况下这是没问题的，但是如果您遇到需要在您的“工作”时间之外让您的小 script-o 可用的情况，您需要忙于确保它被部署在某个总是可用的地方。

多年来，赫罗库是我的选择。他们的工具带总是安装在我的机器上，相关的命令从非常常规的使用中把一些活的东西*永远烙进我的大脑*。

然而，对于一些简单的事情，这种方法确实总感觉有些矫枉过正。此外，免费的 dynos 最终会停止运行，然后在低流量情况下需要更长时间才能醒来。不管怎样，我继续说道。

我一直坚持到*功能即服务*成为现实，并且成熟到可以使用 CLI 工具带来创建和部署一个小项目*和*能够选择在哪个基础设施上部署它。

## 回车...无服务器

如果你一直在关注谷歌和微软为赶上亚马逊在面向开发者的云基础设施上的主导地位所做的努力，你可能也会遇到无服务器项目。

当谈到云基础设施的功能时，有这么多选择是很好的，但必须安装并学习亚马逊 AWS、谷歌云、微软 Azure 和其他工具的所有 CLI 工具的命令是更多的开销，我相信我们大多数人都可以不需要。

无服务器意味着你不必。允许您不仅为上述任何服务创建项目，还可以部署它们。全部使用*完全相同的*组 CLI 命令。

## 咱们造个*的东西*！

我对这类东西的主要用途之一是构建 webhooks，我可以用它在我的 Algolia 索引中存储信息。

在本例中，我们将执行以下操作:

*   使用 Serverless 创建一个项目，该项目将接受通过 POST 请求发送给它的任何 JSON 对象，并将其存储在一个特定的 Algolia 索引中。
*   将我们的代码部署到 Google Cloud，并使用 CURL 检查它是否工作。

### 先决条件

如果你要继续下去，你需要:

*   NodeJS(更确切地说，是 NPM)
*   安装了[无服务器 CLI](https://serverless.com/framework/docs/getting-started/)
*   Google Cloud 帐户

### 脚手架

在你选择的文件夹中，用 Serverless 初始化一个新项目

```
serverless create --template google-nodejs --path serverless-indexer 
```

上面我们告诉 Serverless 创建一个新项目，并使用 Google Cloud 上的 NodeJS 项目的模板。最后的`path`标志指定了我们项目的名称。

接下来，进入新创建的文件夹并安装依赖项

```
npm install 
```

您现在需要为您的 Google Cloud 帐户设置身份验证凭据。这不是一个很长的过程，但是[这里有很好的记录](https://serverless.com/framework/docs/providers/google/guide/credentials/)。

### 添加代码

在你的编辑器中打开`index.js`，它看起来会是这样的:

```
'use strict';

exports.http = (request, response) => {
  response.status(200).send('Hello World!');
};

exports.event = (event, callback) => {
  callback();
}; 
```

这里有一个响应 HTTP 请求的函数和另一个响应基于事件的调用的函数的基本代码。

对于这个项目，我们只处理 HTTP，所以您可以删除第二个函数。

接下来，我们将添加使用 Algolia 所需的代码，从他们的 JavaScript SDK 开始。

使用 NPM(或纱线，如果那是你的包)安装它:

```
npm install --save algoliasearch 
```

像任何 NodeJS 项目一样，这将把 Algolia 库添加到`package.json`中，稍后它也将被部署到 Google Cloud 中。

像这样初始化库:

```
'use strict'

const algolia = require('algoliasearch');
const algoliaApp = algolia('your-app-id', 'your-admin-api-key');
const index = algoliaApp.initIndex('serverless-demo');

// Your function continues here...
exports.http... 
```

现在，我们正在导出一个名为`http`的函数，它不是特别具有描述性。我们改成`updateIndex`吧。

```
'use strict';

const algolia = require('algoliasearch');
const algoliaApp = algolia('your-app-id', 'your-admin-api-key');
const index = algoliaApp.initIndex('serverless-demo');

// Change the function name from http to updateIndex
exports.updateIndex = (request, response) => {

}; 
```

接下来，我们将添加代码来获取传入的 JSON 对象，并将其写入 Algolia 索引。

从 [Algolia 文档](https://www.algolia.com/doc/api-reference/api-methods/add-objects/)中，我们需要的方法是`addObject`。我们将使用承诺而不是文档中使用的回调风格来编写它。

```
exports.updateIndex = (request, response) => {
  index.addObject(request.body)
    .then(newAlgoliaObject => {
      response.status(200).json(newAlgoliaObject);
    })
    .catch(err => console.log(err));
} 
```

在这里，我们将任何传入请求的主体添加到我们的 Algolia 索引中，如果成功，我们将对象数据作为 JSON 响应返回。

如果失败，我们将错误写到控制台。

我们完成的脚本现在看起来像这样:

```
'use strict';

const algolia = require('algoliasearch');
const algoliaApp = algolia('your-app-id', 'your-admin-api-key');
// Remember you will need to create a new index in Algolia
const index = algoliaApp.initIndex('serverless-demo');

exports.updateIndex = (request, response) => {
  index.addObject(request.body)
    .then(newAlgoliaObject => {
      response.status(200).json(newAlgoliaObject);
    })
    .catch(err => console.log(err));
} 
```

这是一个基本的脚本，它不会对传入的数据进行太多的错误检查或清理。对于实际的生产使用，您可能希望添加更多这样的内容，以确保获得预期的效果。

### 部署时间

除了您的`index.js`，Serverless 还会创建一个名为`servless.yml`的文件。在您的编辑器中打开这个文件，我们将进行必要的更改，将脚本部署到 Google Cloud。

需要更新的部分是`functions`。这是你定义在你的`index.js`中写了什么函数以及它们做什么的地方。

无服务器生成的样板文件如下所示:

```
functions: # Defines the functions section
  first: # The name of your function
    handler: http # The exported function
    events: # Defines the events that you call
      - http: path # Sets this function as an HTTP function 
```

考虑到这些。进行以下更改:

```
functions:
  updateIndex: # Change the function name
    handler: updateIndex # Change the handler
    events:
      - http: path 
```

如果您按照无服务器文档中的设置来设置包含 Google Cloud 认证密钥的`keyfile.json`,那么现在就可以开始部署了。

```
serverless deploy 
```

如果一切顺利，您将获得一个新部署的函数的 URL，看起来像这样:[https://us-central 1-server less-indexer . cloud functions . net/update index](https://us-central1-serverless-indexer.cloudfunctions.net/updateIndex)

### 考验它的时间

打开一个新的终端窗口。然后修改下面的命令，使其包含您在使用无服务器将功能部署到 Google Cloud 时获得的 URL。

```
curl -X POST https://us-central1-serverless-indexer.cloudfunctions.net/update -d '{"product":"iPhone X", "price":"1199", "capacity":"256gb"}' 
```

当一个新对象被写入索引时，Algolia API 通常会做出这样的响应。大概是这样的:

```
{  "createdAt":"2018-06-25T19:27:59.353Z",  "taskID":107402530,  "objectID":"120246040"  } 
```

## 搞定！

有许多其他的无服务器函数和 Algolia 的伟大组合，你可以使用无服务器使用更多的可用 API，所以深入挖掘，看看你能想出什么。

如果你正在寻找一点额外的灵感，看看 [Essential Mix API](https://github.com/martyndavies/essential-mix-api) 。我去年创建的一个项目使用亚马逊的 Lambda 函数和 Algolia 索引作为数据库。