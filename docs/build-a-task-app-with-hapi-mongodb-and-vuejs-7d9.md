# 用哈比神、MongoDB、Vue.js 搭建一个任务 app

> 原文：<https://dev.to/connor11528/build-a-task-app-with-hapi-mongodb-and-vuejs-7d9>

> 最初在媒体上发布的帖子

本教程的想法是，我们将使用 Node.js、MongoDB 和 Vue.js 构建一个任务应用程序。用户将能够从他们的 web 浏览器中读取、创建和删除任务。所有任务都将存储在数据库中，更新将从 web 浏览器异步进行。这意味着用户不必刷新页面就能看到更改生效。这个应用程序很简单，应该清楚地介绍了如何使用这些技术进行构建。

最终产品看起来会像这样:

使用 Node.js 服务器、Mongo 数据库和 Vue 客户端应用程序的 todo 应用程序示例。
🐙服务器端代码在 GitHub 上是开源的。

🐱客户端代码在 GitHub 上是开源的。

首先，我们将从构建服务器开始。因此，我们必须介绍…

Hapijs
哈比神是一个 Node.js 服务器框架，被 Lob、Stitch Labs 和 Auth0 等大公司使用。要开始使用 npm init 生成一个项目，并将其用作您的主服务器文件:

```
const Hapi     = require('hapi');
const routes   = require('./routes');
require('./utils/database');
const server = Hapi.server({
  port: 3000,
  host: 'localhost',
  routes: { cors: true }
});
const startServer = async () => {
  try {
    routes.forEach((route)=>{
      server.route(route);
    });
await server.start();
    console.log(`Server running at: ${server.info.uri}`);
  } catch (err) {
    console.error(err);
  }
};
startServer();
module.exports = server; 
```

如果你熟悉 express，这看起来很像一个 express 应用程序。尽管我们使用了 async / await 函数，但我们可以清楚地看到。这是 Node.js 最新版本的一个新特性，也是对 Javascript 语言的一个很好的补充。本质上，前缀为“async”的函数可以保持执行，直到 await promise 调用被返回。异步函数返回承诺。

我们在这里用默认的哈比神选项配置 cors，并通过 require 语句连接到数据库。

为了连接一个 d query MongoDB，我们将使用一个叫做 mongose 的 ORM 来查询和写入 Mongo。

```
const mongoose = require('mongoose');
require('../models');
mongoose.connect('mongodb://localhost/task-app-backend', {
  useNewUrlParser: true
}, (err) => {
  if (err) throw err;
}); 
```

它连接到 MongoDB 数据库(您可能需要让 mongod 在本地机器上的一个单独的选项卡中运行)。

在 MongoDB 中，没有数据库迁移。ORM 包装器有一个我们可以利用的模型概念。由于这是一个任务应用程序，我们将创建一个任务模型。

```
const mongoose = require('mongoose');
const taskModel = mongoose.Schema({
  name: {type: String, required: '{PATH} is required!'},
  description: {type: String},
}, {
  timestamps: true
});
module.exports = mongoose.model('Task', taskModel); 
```

这为我们建立了一个 MongoDB 集合，它基本上是一个表，形状像一个旧的大型 Javascript 对象。他们使用一种叫做 BSON 的东西，他们在 MongoDB 上写了一份白皮书。

MongoDB 股价。

相比之下，Mongoose 是一个开源的 npm 包。

我们可以将路由定义为 Javascript 对象数组，每个对象都有“方法”、“路径”和“处理程序”属性。您还可以包含一个可选的“options”属性，我们将把它绑定到 api/index.js 文件。

哈比神路由文件

```
const api = require('./api');
const routes = [
  {
    method: 'GET',
    path: '/',
    handler: (request, h) => {
      return {success: true};
    }
  },
  {
    method: 'GET',
    path: '/api',
    handler: (request, h) => {
      return {success: true};
    }
  },
  {
    method: 'GET',
    path: '/api/task',
    options: api.task.all
  },
  {
    method: 'POST',
    path: '/api/task',
    options: api.task.create
  },
  {
    method: 'GET',
    path: '/api/task/{task}',
    options: api.task.get
  },
  {
    method: 'PUT',
    path: '/api/task/{task}',
    options: api.task.update
  },
  {
    method: 'DELETE',
    path: '/api/task/{task}',
    options: api.task.remove
  },
];
module.exports = routes; 
```

最后，对于 CRUD 端点，这是我所拥有的:

```
const {Task} = require('./../models');
const Boom = require('boom');
const taskApi = {
  all: {
    async handler(request, h) {
      try {
        return await Task.find({}).sort({ createdAt: 'desc' });
      } catch (err) {
        Boom.badImplementation(err);
      }
    }
  },
  create: {
    async handler(request, h) {
      try {
        const task = await new Task({
          name: request.payload.name,
          description: request.payload.description
        });
        task.save();
        return { message: "Task created successfully", task };
      } catch (err) {
        Boom.badImplementation(err);
      }
    }
  },
  get: {
    async handler(request, h) {
      try {
        const task = request.params.task;
        return await Task.findOne({
            _id: task.id
        });
      } catch (err) {
          Boom.badImplementation(err);
      }
    }
  },
  update: {
    async handler(request, h) {
      try {
        const task = request.params.task;
        const updates = request.payload;
        // todo: submit a pull request

      } catch (err) {
          Boom.badImplementation(err);
      }
    }
  },
  remove: {
    async handler(request, h){
        try {
            const task = await  Task.findById(request.params.task).remove();
            return { success: true, message: 'Successfully removed task!' };
        } catch (err) {
            Boom.badImplementation(err);
        }
    }
  }
};
module.exports = taskApi; 
```

这个文件使用 Mongoose 从数据库中获取我们的记录。await 调用解析承诺，并在承诺解析时阻止执行。请求对象来自我们的 Vue.js 应用程序，它将存放在一个单独的 repo 中。

Vue.js
这是 Vue，React，Lindsay Lohan，Taylor Swift 的框架对比。

看起来谷歌预测泰勒在未来几周会有大作为。

从上图来看，泰勒·斯威夫特显然是框架比较的赢家。抱歉，反应过来。
不管怎样，前端源代码在这里。它使用一个名为 vue-webpack-simple 的便捷应用生成器，由 Vue.js 核心团队维护。

我学到的一个有趣的东西是，有一个过渡和过渡组组件，你可以用它来用 CSS 排列动画。该组件的 HTML 类似于:

```
<transition-group name="task-list">    
  <div class="row mb-2" v-for="(task, index) in tasks" :key="task._id">       
    <div class="col-sm-4">          
      {{ task.name }}       
    </div>       
    <div class="col-sm-2">          
      <span @click='updateTask(task._id, index)' class="task-action"><i class="fas fa-pencil-alt"></i>
      </span>          
      <span @click='deleteTask(task._id, index)' class="task-action badge badge-danger badge-pill">X</span>
    </div>    
  </div> 
</transition-group> 
```

在 Vue.js 主页上有很多不同用例的很棒的入门示例代码，用于文档:进入/离开&列出转换。

当进入和离开转换发生时，Vue.js 应用特殊的类。通过将过渡组命名为“task-list ”, task-list 将被附加到特殊的 Vue.js 事件类名中:

```
.task-list-item {
   display: inline-block;
   margin-right: 10px;
}
.task-list-enter-active, .task-list-leave-active {
   transition: opacity .5s;
}
.task-list-enter, .task-list-leave-to {
   opacity: 0;
} 
```

当我们添加和删除任务时，Vue.js 事件与 transition CSS 属性一起负责淡入淡出。(视频在最上面。)

差不多就是这样！开放源代码链接如下:

🐙服务器端代码在 GitHub 上是开源的。

🐱客户端代码在 GitHub 上是开源的。

如果你在湾区的就业市场上，请考虑在 Employbl.com 上创建一个求职者档案。