# 使用 hapijs 和 mongoose 构建一个 RESTful API

> 原文：<https://dev.to/chetan_raj/build-a-restful-api-using-hapijs-and-mongoose-33j4>

API 将使用 HTTP 动词——GET、PUT、POST 和 DELETE 来处理对象的 CRUD

好，我们开始吧

> 确保你已经在你的机器上安装了节点，并把它作为一个全局包

创建一个名为“/hapijs-mongose-restapi”的项目目录，运行命令并填写所需的详细信息。

```
yarn init 
```

> 将主文件指定为 app.js

yarn init 类似于 npm init，这将生成 package.json 文件，更多信息请参考 [yarn](https://yarnpkg.com/) 。

然后安装这个 API 所需的 node_modules

```
yarn add hapi mongoose --dev 
```

在 app.js 中构建 hapi 服务器

```
const Hapi = require('hapi');
const server = new Hapi.Server();

server.connection({
    host: 'localhost',    
    port: 4000
});

server.route({
    method: 'GET',
    path: '/',
    handler(request, reply) {
    reply('Hello, world!');
    }
});

server.route({
    method: 'GET',
    path: '/api',
    handler(request, reply) {
    reply('Hello, API!');
    }
});

server.start(err => {
    if (err) {
    throw err;
    }
    console.log(`Server running at: ${server.info.uri}`);
});

exports.server = server; 
```

*   server.connection —使用主机和端口连接到服务器

*   server.route 将包含路由对象

    > 在 server.router 中，处理程序将使用 return()API—[http://hapijs.com/api#replyerr-result](http://hapijs.com/api#replyerr-result)返回，其中该返回用于通过设置响应并将响应和状态返回给处理程序来结束处理程序活动。

*   server.start 启动服务器

启动服务器并测试索引路由

```
nodemon app.js 
```

[**使用 nodemon 在 node.js 应用程序的代码更改后自动重新加载服务器**
*是 node.js 的新手，每当我更改代码时，我都会在终端中不停地点击 Ctrl+C 和…*medium.com](https://medium.com/jsessentials/using-nodemon-for-auto-reload-of-server-after-code-change-74c9901a1d98)

让我们用[Postman](https://www.getpostman.com/)—Chrome App/Mac App 来测试一下 API。

打开 Postman，选择 **GET** HTTP 动词，输入网址 [http://localhost:4000](http://localhost:4000) 点击发送，响应正文应该显示**哈比神世界！**。

创建一个到 API 的 mongoDB 连接，在应用程序的根目录下创建一个 database.js 文件

```
var Mongoose = require('mongoose');

//load database
Mongoose.connect('mongodb://localhost/hapijs-mongoose-restapi/');
var db = Mongoose.connection;

db.on('error', console.error.bind(console, 'connection error'));
db.once('open', function callback() {
    console.log('Connection with database succeeded.');
});

exports.db = db; 
```

并将 database.js 包含在 app.js 中

```
// app.js

const db = require('./database').db; 
```

这将连接到 mongoDB，然后让我们为我们的狼创建 mongoose 模式模型，模型放在/models 目录中

### 狼模型

创建 Wolf 模型的路径是—/hapijs-mongose-restapi/models/Wolf . js

Wolf 模型将只有 **name** 字段，其中 SchemaType 是 String。

```
const mongoose = require('mongoose');
const Schema = mongoose.Schema;

var WolfSchema = new Schema({
    name: String
});

module.exports = mongoose.model('Wolf', WolfSchema); 
```

创建了 Wolf.js 模型后，在 app.js 中需要这样做

```
// app.js

const Hapi = require('hapi');
const server = new Hapi.Server();
const db = require('./database').db;

... 
```

nodemon 将在这里运行，它将监视应用程序目录中任何文件的变化，并为您重新启动服务器，没有 CTRL+C hazzle。

### 路线—默认 API 路线

URL—[http://localhost:4000/API](http://localhost:4000/api)

```
// app.js

...

server.route({
    method: 'GET',
    path: '/api',
    handler: function (request, reply) {
        reply({'message': 'Hello, API!'});
    }
}); 
```

### 路线

创建 routes.js 文件，并将该文件包含在 app.js 中

```
// routes.js

module.exports = []; 
```

然后将这个 routes.js 包含到 app.js 文件中

```
// app.js

…

const routes = require(‘./routes’);
server.route(routes); 
```

### 获取路线——获取所有的狼

要求在 router.js 上安装 Wolf 模型

URL—[http://localhost:4000/API/wolves](http://localhost:4000/api/wolves)

```
// routes.js

const Wolf = require('./models/Wolf');

module.exports = [
  {
      method: 'GET',
      path: '/api/wolves',
      handler: function (request, reply) {
          Wolf.find(function(error, wolves) {
              if (error) {
                  console.error(error);
              }

              reply(wolves);
          });
      }
  }
]; 
```

这是一条简单的路线——它将以 JSON 格式呈现所有的狼。

### 邮路——救一只狼

在 routes.js 中，为 POST 创建一个用于拯救狼的模块

URL—[http://localhost:4000/API/wolves/gray wolf](http://localhost:4000/api/wolves/GrayWolf)

```
// routes.js

const Wolf = require('./models/Wolf');

module.exports = [
  {
      method: 'GET',
      path: '/api/wolves',
      handler: function (request, reply) {
          Wolf.find(function(error, wolves) {
              ...
          });
      }
  },
  {
      method: ['PUT', 'POST'],
      path: '/api/wolves/{name}',
      handler: function (request, reply) {          
          const wolf = new Wolf({
              name: request.params.name
          });
          wolf.save(function(error, wolf) {
            if (error) {
                console.error(error);
            }

            reply(wolf.id);
          });
      }
  }
]; 
```

这将返回在 mongoDB 中创建的对象 id。

你可以在这里找到源代码[。](https://github.com/chetanraj/hapijs-mongoose-restapi)

### 结论

我们现在有办法使用 hapiJS 来处理狼身上的 CRUD。

请点击这篇文章下面的心形图标，如果你有任何问题，请在下面评论。