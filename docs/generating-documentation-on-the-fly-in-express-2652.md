# 在 express 中即时生成文档

> 原文：<https://dev.to/akshendra/generating-documentation-on-the-fly-in-express-2652>

不久前，我决定为我将要构建的一些 HTTP APIs 生成文档。这些 API 将在内部使用，所以我不想承诺任何在线解决方案，比如 [API 蓝图](https://apiblueprint.org/)、[养蜂场](https://apiary.io/)或[招摇](https://swagger.io/)。从头开始建立一些东西，...有点多。此外，我希望将文档集成到代码中，避免仅仅为了编写文档而进行任何类型的上下文切换。

我知道`jsdoc`和`esdoc`，它们都允许我们在注释中写文档。然而，他们的工作是记录 javascript 代码，而不是 HTTP APIs。然后，我找了一个工具 [`swagger-jsdoc`](%7B%%20raw%20%%7D%60https://github.com/Surnet/swagger-jsdoc%60%7B%%20endraw%20%%7D) ，从注释中生成 swagger / OpenAPI 规范。这正是我一直在寻找的。

### 现在让我们来看看一些`code`

只是一个简单的列出动物的服务器，你也可以添加你最喜欢的动物。*相当新颖的概念*。

```
const express = require('express');
const bodyparser = require('body-parser');

const app = express();
app.use(bodyparser.json({
  strict: false,
}));

const animals = [
  'panda', 'racoon', 'python',
];

app.get('/list', (req, res) => {
  return res.json(req.query.sort === 'yes' ? Array.from(animals).sort() : animals); // why is .sort inplace 😠
});

app.post('/add', (req, res) => {
  animals.push(...req.body.animals);
  return res.json({
    message: 'Added',
  });
});

app.listen(3000, () => {
  console.log('Server started at port 3000');
}); 
```

Enter fullscreen mode Exit fullscreen mode

`swagger-jsdoc`要求注释遵循 [OpenAPI 规范](https://swagger.io/docs/specification/about/)，相当直观。

添加`/list`路线的文档注释。

```
/**
 * @swagger
 * /list:
 *   get:
 *     summary: List all the animals
 *     description: Returns a list of all the animals, optionally sorted
 *     tags:
 *       - animals
 *     parameters:
 *       - in: query
 *         name: sort
 *         type: string
 *         required: false
 *         enum:
 *           - yes
 *           - no
 *     responses:
 *       200:
 *         description: List of animals
 *         schema:
 *           type: object
 *           properties:
 *             animals:
 *               type: array
 *               description: all the animals
 *               items:
 *                 type: string
 */

app.get('/list', (req, res) => {
  // ...
}); 
```

Enter fullscreen mode Exit fullscreen mode

第一行是`@swagger`，它帮助`swagger-jsdoc`将这个注释块识别为 swagger (OpenAPI)规范。接下来的几行定义了路径、方法、一些总结和描述。`tags`用于对 API 进行分组。

接下来描述预期参数`query`和`path`。我们的`/list` API 需要一个可选的`sort`查询参数，用于在发送之前决定动物列表是否应该排序。

然后我们定义回应。首先是状态，稍加描述，然后是响应的模式。我们在这里返回 JSON。但是，记录其他内容类型也很容易。

我们将为`/add`请求做同样的事情。

```
/**
 * @swagger
 * /add:
 *   post:
 *     summary: Add more animal
 *     description: Add animals to the list
 *     tags:
 *       - animals
  *     requestBody:
 *       content:
 *         application/json:
 *           schema:
 *             type: object
 *             properties:
 *               animals:
 *                 type: array
 *                 items:
 *                   type: string
 *     responses:
 *       200:
 *         description: Adds the animals in body
 *         schema:
 *           type: object
 *           properties:
 *             message:
 *               type: string
 *               default: 'Added'
 */
app.post('/add', (req, res) => {
  // ...
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经准备好了注释，我们将连接`swagger-jsdoc`模块。

```
// ... other modules
const swaggerJSDoc = require('swagger-jsdoc');

const app = express();
app.use(bodyparser.json({
  strict: false,
}));

const animals = [
  'panda', 'racoon', 'python',
];

// -- setup up swagger-jsdoc --
const swaggerDefinition = {
  info: {
    title: 'Animals',
    version: '1.0.0',
    description: 'All things animlas',
  },
  host: 'localhost:3000',
  basePath: '/',
};
const options = {
  swaggerDefinition,
  apis: [path.resolve(__dirname, 'server.js')],
};
const swaggerSpec = swaggerJSDoc(options);

// -- routes for docs and generated swagger spec --

app.get('/swagger.json', (req, res) => {
  res.setHeader('Content-Type', 'application/json');
  res.send(swaggerSpec);
});

// other routes 
```

Enter fullscreen mode Exit fullscreen mode

这将在`/swagger.json`提供一个大摇大摆的规格。剩下要做的就是以一种更加人性化的方式来渲染这个规范。为此我选择了 [ReDoc](https://github.com/Rebilly/ReDoc) 。它有一个简单的设置。

包含一个 HTML 文件

```
<!DOCTYPE html>
<html>
  <head>
    Quizizz Docs
    <!-- needed for adaptive design -->
    <meta charset="utf-8"/>
    <link rel="shortcut icon" type="image/x-icon" href="https://quizizz.com/favicon.ico" />
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link href="https://fonts.googleapis.com/css?family=Montserrat:300,400,700|Roboto:300,400,700" rel="stylesheet">

    <!--
    ReDoc doesn't change outer page styles
    -->
    <style>
      body {
        margin: 0;
        padding: 0;
      }
    </style>
  </head>
  <body>
    <!-- we provide is specification here -->
    <redoc spec-url='http://localhost:3000/swagger.json' expand-responses="all"></redoc>
    <script src="https://cdn.jsdelivr.net/npm/redoc@next/bundles/redoc.standalone.js"> </script>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

我们已经将`http://localhost:3000/docs/swagger.json`设置为服务器 JSON 规范的位置。让我们设置一个路由来服务这个 HTML。

```
app.get('/docs', (req, res) => {
  res.sendFile(path.join(__dirname, 'redoc.html'));
}); 
```

Enter fullscreen mode Exit fullscreen mode

结果，

[![ReDoc rendered documentation](img/0a888395326ae55f2608663e75906f82.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9z8K6R4r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/QN1LULM.gif)

> OpenAPI 和`swagger-jsdoc`有更多的组件，使得这个过程更加容易。您可以为多次使用的模式/请求/响应编写定义，然后在文档中使用它们。查看[组件部分| Swagger](https://swagger.io/docs/specification/components/) 和[入门| Swagger JSDoc](https://github.com/Surnet/swagger-jsdoc/blob/master/docs/GETTING-STARTED.md) 以 JavaScripty 方式定义它们。
> 
> 代码可以在[这里](https://github.com/akshendra/akshendra.github.io/tree/master/docs-on-fly/code)找到