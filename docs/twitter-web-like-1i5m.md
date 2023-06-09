# node 和 vue 搭建的网站，模仿 Twitter 的 UI 和功能

> 原文：<https://dev.to/jayzang/twitter-web-like-1i5m>

> [项目链接](https://github.com/JayZang/Twitter-Web-Like)
> 这个项目是用[节点](https://nodejs.org/en/)和 [Vue](https://vuejs.org/) 搭建的。目标是学习 Vue 框架和后端技术来实现一个水疗网站。
> 
> 图片及署名的所有权利保留给[推特](https://twitter.com/)。
> 
> 本项目使用的技术、工具和软件包并未被 [Twitter](https://twitter.com/) 实际使用。
> 
> 欢迎技术交流，如果本项目有错误的代码或编程概念，请告诉我，谢谢👍

[![Demo](img/0d8703f2c968a2d208e07696e5952d0c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ehm1KQp6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://github.com/JayZang/Twitter-Web-Like/blob/master/Demo.gif%3Fraw%3Dtrue)

## 主用包

*   [快递](#express)
*   [猫鼬](#mongoose)
*   [jsonwebtoken](#jsonwebtoken)
*   视图
*   武契特
*   视图-路由器
*   阿克斯

## 构建设置

安装所有依赖包:(假设已经安装了[节点](https://nodejs.org/en/)和【NPM】

```
npm install 
```

Enter fullscreen mode Exit fullscreen mode

在 [localhost:8080](http://localhost:8080/) :

热重装服务，这个步骤只能显示前端，后端服务器还没有启动。

```
npm run dev 
```

Enter fullscreen mode Exit fullscreen mode

运行 [MongoDB](https://www.mongodb.com/) 的服务器:(假设已经安装)

```
mongod --dbpath=YOUR_PATH_TO_STORE_DATA 
```

Enter fullscreen mode Exit fullscreen mode

在**开发**环境中运行后端服务器监听 3001 端口来处理 API 请求:

```
npm run start:dev 
```

Enter fullscreen mode Exit fullscreen mode

服务器 API 的 Uint 测试:

```
npm run unit 
```

Enter fullscreen mode Exit fullscreen mode

如果所有的单元测试都通过了，我们就可以连接到 [localhost:8080](http://localhost:8080/) 并开始工作(注册、登录等)...)成功。

以上构建应用的步骤足够我们学习`Vue`和`Node`。然而，如果我们想将我们的项目部署到云中，下面的步骤
是必需的。

### 捆绑部署

捆绑前端(Vue)代码:

```
npm run build 
```

Enter fullscreen mode Exit fullscreen mode

在 [localhost:3001](http://localhost:3001/) :

表示如果 http 请求 URL 不是针对 API 请求，服务器将响应捆绑的前端页面。

```
npm run start:dev 
```

Enter fullscreen mode Exit fullscreen mode

在**生产**环境上运行服务器:

部署到云端时，我们以 [Heroku](https://devcenter.heroku.com/) 为例， [Heroku](https://devcenter.heroku.com/) 默认会自动运行`npm start`
。我们只是自动获取 [Heroku](https://devcenter.heroku.com/) 系统的环境变量(例如:PORT)来设置我们的服务器工作(要连接的
MongoDB 的服务器需要自己配置)

```
npm start           #same as 'npm run start:prod' 
```

Enter fullscreen mode Exit fullscreen mode

可以在/server/config/config.json 中设置自定义配置

## 简单介绍

### 快递

[公文](https://expressjs.com/zh-tw/guide/routing.html)

使用 RESTful 路由处理 http 请求。

```
const app = require('expess')

app.get('/', (req, res, next) => {
  res.json({
    res: 'This is GET method'
  })
})
app.post('/', (req, res, next) => {
    res.json({
      res: 'This is POST method'
    })
})
app.delete('/', (req, res, next) => {
  res.json({
    res: 'This is DELETE method'
  })
})
app.update('/', (req, res, next) => {
  res.json({
    res: 'This is UPDATE method'
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

### 獴

使用关系数据库。

本项目有三种型号:

*   用户
*   邮件
*   评论

模式设置:

```
const userSchema = mongoose.Schema({
  posts: [{
    type: mongoose.Schema.Types.ObjectId,
    ref: 'Posts'
  }],
  //...
})
const postSchema = mongoose.Schema({
  author: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'Users'
  },
  comments: [{
    type: mongoose.Schema.Types.ObjectId,
    ref: 'Comments'
  }],
  //...
})
const commentSchema = mongoose.Schema({
  user: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'Users'
  },
  target: {
    model: String,
    id: mongoose.Schema.Types.ObjectId()
  },
  //...
})

const userModel = mongoose.Model('Users', userSchema)
const postModel = mongoose.Model('Posts', postSchema)
const commentModel = mongoose.Model('Comments', commentSchema) 
```

Enter fullscreen mode Exit fullscreen mode

获取填充数据:

```
userModel.findById(USER_ID)
  .then(user => {
    if (!user) {
      //...
    }

    let opt = {
      path: 'posts',
      populate: {
        path: 'comments'
      }
    }

    user.populate(opt).execPopulate()
      .then(populatedUser => {
        // Do what tou want to do
      }).
      catch(e => {
        //...
      })
  })
  .catch(e => {
    //...
  }) 
```

Enter fullscreen mode Exit fullscreen mode

### Jsonwebtoken

[公文](https://github.com/auth0/node-jsonwebtoken)

创建令牌，1 小时后作废。

你可以在令牌中放入一些数据，让服务器知道这个令牌的所有者和信息。

```
const jwt = require('jsonwebtoken')

const token = jwt.sign({
  id: USER_ID,
  access: 'auth',
  exp: Math.floor(Date.now() / 1000) + (60 * 60 * 1)
}, 'YOUR_SECRET_KEY') 
```

Enter fullscreen mode Exit fullscreen mode

令牌验证:

```
try {
  let data = jwt.verify(RECEIVED_TOKEN, 'YOUR_SECRET_KEY')
} catch (e) {
  // Verify fail
} 
```

Enter fullscreen mode Exit fullscreen mode

### 视图

[公文](https://cn.vuejs.org/v2/guide/index.html)

下图是一个实例组件的生命周期。

我认为最重要的是了解每个事件什么时候会被调用。

[![Vue component's life cycle](img/0b9a239f0f1503e9f977327c89e2c6f5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6M-lBymO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cn.vuejs.oimg/lifecycle.png)

如果我们有组件需要' userID '的属性来异步获取用户的信息。

当组件被实例化时，将调用`created`的函数，通过当前的“用户标识”获取用户信息。
但是如果下一个路由也有这个组件，并且有不同的“userID”属性，那么这个组件被重用，而不是
再次实例化一个新组件。此时`created`函数未被调用，所以另一种方法是使用`watch`
属性来监控‘userID’属性是否改变，如果指示的目标改变，您设置的函数将被调用。

```
Vue.component('your-component', {
  props:['userID'],
  data: function () {
    return {
      user: null
    }
  },
  created() {
    this.getUserInfo()
  },
  watch: {
    // here is important
    'userID': 'getUserInfo'
  },
  method: {
    getUserInfo() {
      // Some http Request to get user information from server
    }
  },
  template: '<div v-if="user">{{ user.name }}</div>'
}) 
```

Enter fullscreen mode Exit fullscreen mode