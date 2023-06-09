# 使用这个简单的方法，避免在每个快速路由前手动添加“/api”

> 原文：<https://dev.to/ccleary00/avoid-manually-prepending-api-to-every-express-route-with-this-simple-method-1m44>

***原载于 [coreycleary.me](https://www.coreycleary.me/avoid-manually-prepending-api-to-every-express-route-with-this-simple-method/)** 。这是我的内容博客的交叉帖子。我每一两周发布一次新内容，如果你想直接在收件箱里收到我的文章，你可以[注册我的时事通讯](https://www.coreycleary.me/about/)！我还定期发送备忘单、其他开发人员的优秀教程的链接以及其他赠品！*

你是否曾经因为不得不总是手动*在你每条快递路线的开头加上 **'/api'** 而感到沮丧？通常我看到这有两种形式:*

 ***表格 1:** 中的
`app.js`文件:

```
app.use('/api/users', require('./routes/users'))
app.use('/api/posts', require('./routes/posts'))
app.use('/api/comments', require('./routes/comments'))
app.use('/api/subscriptions', require('./routes/subscriptions')) 
```

**表格 2:**
在路线文件:

```
router.post('/api/users', users.createUser)
router.post('/api/posts', blogpost.postBlogpost)
router.post('/api/comments', comment.postComment)
router.post('/api/subscriptions', subscription.addSubscription) 
```

无论哪种方式——它看起来很乱，如果你没有复制/粘贴，你必须记住每次都添加它，而且看起来应该有更好的方式来做这件事。

### 让我们把它打扫干净

幸运的是，解决这个问题非常简单。

在我们的 routes 文件中，从每个 route
中删除“/api”

```
const express = require('express')
const router = express.Router()

router.post('/users', users.createUser)
router.post('/posts', blogpost.postBlogpost)
router.post('/comments', comment.postComment)
router.post('/subscriptions', subscription.addSubscription)

module.exports = router 
```

在`app.js`(或`server.js`，无论你在哪里设置你的快递服务器):

```
const express = require('express')
const app = express()
const routes = require('./routes')

app.use('/api', routes) // routes is our routes file above 
```

因此，我们将快速路由器导入到`app.js`中，并预先添加“/api”，而不是手动为每个路由添加“/api”，这一次，只添加一次！-至`app.use()`

### 这是如何工作的？

我们在这里所做的基本上是组成一个管道。

让我们将`app`视为我们的主应用程序，将`router`(我们的路线文件)视为“子应用程序”。

当我们的应用程序收到一个 web 请求时，它将首先点击`app.use('/api', routes)`。如果请求被发送到一个端点，匹配诸如“/api/blah/blah”之类的东西，它将**匹配`app.use('/api', routes)`上的**，并从那里被路由到我们的子应用路由器，它包含我们的其余路由。

所以 Express 为您构建了这个管道，您不必再为每个路由手动添加'/api '！

### 最后一件事！

我正在编写许多新内容，以帮助 Node 和 JavaScript 更容易理解。更容易，因为我觉得不需要像有时候那么复杂。如果你喜欢这篇文章，并发现它很有帮助[这里是再次链接](https://www.coreycleary.me/about/)订阅我的时事通讯！*