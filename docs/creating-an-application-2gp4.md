# 创建应用程序

> 原文：<https://dev.to/jakesweb/creating-an-application-2gp4>

### 我的第一份申请

从零开始可能会令人生畏。刚开始的时候，我觉得不知所措。奇怪的是，那只是大约一周前的事(现在看来是那么遥远)。当我开始创建一个实际的应用程序时，我有两个理由开始这么大的事情。

*   我想学习，需要把自己撞向墙壁
*   作为全栈开发人员，我正在从事一个团队项目

第二个原因可能不是最好的，但第一个是学习的好方法。不要误解我，我一直在用免费资源学习，我也做了部分应用程序，但没有从头开始。看着空白的屏幕，意识到你不能 100%确定你需要挑选什么东西来确保你做好工作，这可能是压倒性的。什么服务和 API 可以工作？除了闪烁的光标和对 Node 和 Express 的粗略了解之外，您一无所有。

我出发了。HTTP 我选了 Express。我选择了 MongoDB 和 Mongoose 作为我的数据库层。我选择了帕格作为我的模板语言。我开始编写代码，看看我能做些什么。我想做的第一件事是让用户注册和登录工作。我决定自己动手积累经验。这是我想到的:

*   在单独的文件中设置用户模式和模型
*   根据类别在单独的文件中设置快速路线。
*   创建模板页面以将表单发送到 my user.js routing 文件夹中的投递路径
*   使用快速会话来处理用户会话

我刚把它修好。这些路径的工作方式是创建一个文件，然后将它包含到您的入口点 app.js 文件中:

```
// setup the user session
app.use(session({ secret: SESSION_SECRET });

// setup the router file
app.use('/user', require('./router/user'); 
```

这段代码告诉 app.js 使用。/router/user.js 文件，用于任何以/user 开头的路由。从这里开始，user.js 包含了 user-controller.js，它被设置为与数据库进行交互。如果需要访问数据库，一些路由会使用控制器查询。对于路由器文件，它看起来像这样:

```
const userController = require('../controllers/user-controller');
// some other routes and code
router.post('/create', (req,res) => {
  userController.create(what to send to the database and then what to do with that)
}); 
```

在用户控制器文件中，您将拥有一个相应的`create()`函数来处理该请求。

```
// add the user model
const User = require('../models/user-model');

exports.create = (username,password,callback) => {
    // Create the user object with the user model
    // using the username and password from the router
    // then return the data you need from the callback
} 
```

一旦我设置好了所有的路由和控制器功能，一切都正常了(我还需要模式的模型)。我能够创建一个用户，登录，然后注销。我能够通过 cookie 存储用户会话信息。

我想说得更详细，但这已经是一篇长文了。随着我的学习，我会添加更多。我想说的是，开始你的第一个需要许多系统一起工作的项目可能看起来像是在碰壁。我从上周搭建这部分的过程中学到的是，很多时候，那堵砖墙是地面上的一种视觉错觉，你可以直接从它上面开过去。我希望现在，当我凝视着这些砖墙时，我可以毫不费力地继续开车。